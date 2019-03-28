---
layout: post
title:  "浅析分布式事务"
categories: 分布式
tags:  分布式 分布式事务
author: W.Fly
---
* content
{:toc}
浅析分布式事务：XA、TCC、MQ事务、最大努力通知

# 事务的定义

事务提供一种机制将一个活动涉及的所有操作纳入到一个不可分割的执行单元，也就是说事务提供一种 “要做就全部执行成功，要不做就一个也不做“ 的机制

# 事务的特性

数据库的事务具有四大特性：原子性 `Actomicity`、一致性 `Consistency`、隔离性 `Isolation`、持久性 `Durability`

# InnoDB 事务

InnoDB 是 MySQL 的一个存储引擎，它的事务是由本地事务资源管理器进行管理的：

![数据库事务](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/%E5%88%86%E5%B8%83%E5%BC%8F/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BA%8B%E5%8A%A1.png)

事务的 ACID 通过 InnoDB 日志和锁来保证，事物的隔离性是通过数据库锁机制实现的。

1. 原子性和一致性通过 Undo Log 来实现

   在操作任何数据之前，首先将数据备份到一个地方（这个存储备份的地方称为 Undo Log），然后进行数据的修改，如果出现用户错误或者用户执行 ROLLBACK 语句，系统可以利用 Undo Log 中的备份将数据恢复到事务开始之前的状态

2. 持久性和通过 Redo Log 来实现

   Redo Log 记录的是新数据的备份，在事务提交前，只要将 Redo Log 持久化即可，不需要将数据持久化。当系统崩溃时，虽然数据没有持久化，但是 Redo Log 已经持久化，系统可以根据 Redo Log 的内容，将所有数据恢复到最新的状态

# 分布式事务

分布式事务就是指事务的参与者，支持事务的服务器，资源服务器已将事务管理器分别位于不同的分布式系统的不同节点之上。简单的说，就是一次大的操作由不同的小操作组成，这些小操作分布在不同的服务器上，且属于不同的应用，分布式事务需要保证这些小操作要么全部成功，要么全部失败，本质上说，分布式事务就是为了保证不同数据库的数据一致性。换句话说，分布式事务 = n 个本地事务。通过事务管理器实现 n 个本地事务要么全部成功要么全部失败。

# 分布式事务产生

这里举一个分布式事务的典型例子 —— 用户下单过程。当整个系统采用微服务架构后，一个电商系统往往被拆分成多个子系统：商品系统、订单系统、支付系统、积分系统等。

![电商分布式](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/%E5%88%86%E5%B8%83%E5%BC%8F/%E7%94%B5%E5%95%86%E5%88%86%E5%B8%83%E5%BC%8F.png)

这样，整个下单流程如下：

1. 用户浏览商品，选择某个商品，点击下单
2. 订单系统会生成一条订单
3. 订单创建成功后，支付系统提供支付功能
4. 支付完成后，积分系统为用户增加积分

在上述的步骤中，2、3 和 4 是需要在一个事务中完成的。对于单体应用来说，实现事务很简单，只要将这三个步骤放在同一个方法中，再用 `Spring @Transaction` 注解标识该方法就可以。但是在分布式架构中，这三个步骤要涉及三个系统和三个数据库，因此必须要通过分布式事务，实现三个数据库的本地事务同时成功或同时失败。

# 分布式事务基础

## CAP 定理

一个分布式系统不可能同时满足一致性、可用性和分区容错性

- C（一致性）：对于数据分布在不同节点上的数据来说，一致性是指数据在多个副本之间都能保持一致的特性。如果某个节点更新了数据，那么在其他节点如果都能读取到这个最新的数据，那么就称为强一致性，如果某个节点没有被读取到，那么就是分布式不一致
- A（可用性）：非故障节点在合理的时间内返回合理的响应。也就是说集群中一部分节点故障后，集群整体是否还能响应客户端的读写请求（数据高可用）
- P（分区容错性）：当遇到网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务

高可用和数据一致性是很多系统的设计目标，但是分区又是不可避免的事情。

- **CA without P**：如果不要求 P（不允许分区）则 C 和 A 是可以保证的。但是分区是始终会存在的，因此 CA 系统更多的是允许分区后各子系统仍然保持 CA
- **CP without A**：如果不要求 A，相当于每个请求都需要在 Server 之间强一致，而 P 会导致同步时间无限延长，因此 CP 也是可以保证的。MySQL 主从半同步复制、Zookeeper
- **AP without C：**要高可用并允许分区，则需要放弃一致性。一旦分区发生，节点之间就会失去联系，为了高可用，每个节点只能用本地数据提供服务，而这样会导致全局数据的不一致，很多 NoSQL 都属于此类。MySQL 主从同步异步复制、Redis 主从同步

## Base 理论

Base 理论是 Basically Available（基本可用），Soft state（软状态）和 Eventually consisten（最终一致性）三个短语的缩写，是对 CAP 中 AP 的一个扩展

- BA 基本可用：分布式系统出现故障时，允许损失部分可用功能，保证核心功能可用
- S 软状态：允许系统中存在中间状态，这个状态不影响系统可用性，这里指的是 CAP 中的不一致
- E 最终状态：最终一致是指经过一段时间后，所有节点数据都将会达到一致

Base 解决了 CAP 中理论没有网络延迟，在 Base 中用软引用和最终一致保证了延迟后的一致性。BASE 和 ACID 是相反的，它完全不同于 ACID 的强一致模型，而是通过牺牲强一致性来获得可用性，并允许数据在一段时间内是不一致的，但最终达到一致状态。

## 酸碱平衡

ACID 能保证事务的强一致性，即数据是实时一致的。这在本地事务中是没问题的，在分布式事务中，强一致性会极大影响分布式系统的性能，因此分布式系统中遵循 BASE 理论即可。但分布式系统的不同业务场景对一致性的要求也不同，比如交易场景，就要求强一致，因此遵循 ACID 理论，而在注册成功发送短信验证码等场景下，并不需要实时一致，因此遵循 BASE 理论即可，所以需要根据具体的业务场景，在 ACID 和 BASE 之间寻求平衡。

# 分布式事务协议

## 两阶段提交协议：2PC

分布式系统的一个难点是如何保证架构下多个节点在进行事务性操作的时候保持一致，为实现这个目的，两阶段提交算法的成立基于以下假设：

1. 该分布式系统中，存在一个节点作为协调者，其他节点作为参与者，且节点之间可以进行网络通信
2. 所有节点都采用预写式日志，且日志被写入之后立即被保持在可靠的设备上，即使节点损坏也不会导致日志数据的消失
3. 所有节点不会永久性损坏，即使损坏后仍然可以恢复

![2PC](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/%E5%88%86%E5%B8%83%E5%BC%8F/2PC.png)

###  第一阶段：提交事务请求

1. 事务询问：协调者向所有的参与者发送事务内容，询问是否可以执行事务提交操作，并开始等待各参与者的响应
2. 执行事务：各参与者节点执行事务操作，并将 Undo 和 Redo 信息记入事务日志中
3. 各参与者向协调者反馈事务询问的响应：如果参与者成执行了事务操作，那么就反馈给协调者 YES，表示事务可以执行；反之为 NO，事务不可以执行

### 第二阶段：执行事务提交

协调者从所有的参与者获得的反馈都是 YES

1. 发送提交请求：协调者向所有参与者节点发出 Commit 请求
2. 事务提交：参与者在接收到 Commit 请求后，会正式执行事务提交操作，并在完成提交之后释放在整个事务执行期间占用的事务资源
3. 反馈事务提交结果：参与者在完成事务提交后，向协调者发送 Ack 消息
4. 完成事务：协调者接收到所有参与者反馈的 Ack 消息后，完成事务

### 第二阶段：中断事务

假设任何一个参与者向协调者反馈了 NO 请求，或者等待超时后吗，协调者尚无法接收所有参与者的反馈响应，就会中断事务

1. 发送回滚请求：协调者向所有参与者节点发送 Rollback 请求
2. 事务回滚：参与者收到 Rollback 请求后，会利用其在阶段一中记录的 Undo 信息来执行事务回滚操作，并在完成回滚之后释放在这个事务执行期间占用的资源
3. 反馈事务回滚结果：参与者在完成事务回滚之后，向协调者发送 Ack 消息
4. 中断事务：协调者接收到所有参与者反馈的 Ack 消息后，完成事务中断

###  优缺点

**优点：**原理简单，实现方便

**缺点：**

1. 同步阻塞：在事务提交过程中，所以参与该事务操作的逻辑都处于阻塞状态，无法进行其他任何操作
2. 单点问题：如果协调者在事务提交阶段挂掉，那么其他参与者将一直处于锁定事务资源的状态，而无法继续完成事务操作
3. 数据不一致：如果协调者在