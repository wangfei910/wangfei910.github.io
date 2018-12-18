---
layout: post
title:  "八种常用排序算法"
categories: 数据结构与算法
tags:  排序算法
author: W.Fly
---
* content
{:toc}
数据结构与算法：排序算法

## 01算法分类
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Sort%20Algorithm.png)

## 02时间复杂度
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Time%20complexity.png)

## 03相关概念

**稳定**：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。

**不稳定**：如果a原本在b的前面，而a=b，排序之后a可能会出现在b的后面。

**时间复杂度**：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。

**空间复杂度**：是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。

## 1、冒泡排序(Bubble Sort)
冒泡排序是一种简单的排序算法。它循环遍历要排序的数列，一次比较两个元素，如果前者比后者大就交换彼此的位置。

### 1.1 算法描述

- 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
- 针对所有的元素重复以上的步骤，除了最后一个；
- 重复步骤1~3，直到排序完成。

### 1.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Bubble%20Sort.gif)

### 1.3代码实现
```java
//冒泡排序
/**
 * 5,7,2,9,4,1,0,5,7		共需要比较length-1轮
 * 5,7,2,9,4,1,0,5,7	
 * 5,2,7,9,4,1,0,5,7
 * 5,2,7,4,1,0,5,7,9
 * 2,5   
 */
public static void bubbleSort(int[]  arr) {
	//控制共比较多少轮
	for(int i=0;i<arr.length-1;i++) {
		//控制比较的次数
		for(int j=0;j<arr.length-1-i;j++) {
			if(arr[j]>arr[j+1]) {
				int temp=arr[j];
				arr[j]=arr[j+1];
				arr[j+1]=temp;
			}
		}
	}	
}

```

## 2、快速排序(Quick Sort)
基本思想：通过一次排序将待排数列分隔成独立的两个数列，其中一部分数列的关键字均比另一部分数列的关键字小，则可分别对这两部分数列继续进行排序，以达到整个数列有序。

### 2.1 算法描述
快速排序使用分治法来把一个串(list)分为两个子串(sub-lists)。具体算法描述如下：

- 从数列中挑出一个元素，称为 “基准”(pivot)；
- 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区(partition)操作；
- 递归地(recursive)把小于基准值元素的子数列和大于基准值元素的子数列排序。

### 2.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Quick%20Sort.gif)
### 2.3 代码实现

```java
//快速排序
public static void quickSort(int[] arr,int start,int end) {
	if(start<end) {
		//把数组中的第0个数字做为标准数
		int stard=arr[start];
		//记录需要排序的下标
		int low=start;
		int high=end;
		//循环找比标准数大的数和比标准数小的数
		while(low<high) {
			//右边的数字比标准数大
			while(low<high&&stard<=arr[high]) {
				high--;
			}
			//使用右边的数字替换左边的数
			arr[low]=arr[high];
			//如果左边的数字比标准数小
			while(low<high&&arr[low]<=stard) {
				low++;
			}
			arr[high]=arr[low];
		}
		//把标准数赋给低所在的位置的元素
		arr[low]=stard;
		//处理所有的小的数字
		quickSort(arr, start, low);
		//处理所有的大的数字
		quickSort(arr, low+1, end);
	}
}
```

## 3、插入排序(Insertion Sort)
工作原理：通过构建有序数列，对于未排序元素，在已排序数列中从后向前扫描，找到相应位置并插入。

### 3.1 算法描述
一般来说，插入排序都采用in-place在数组上实现。具体算法描述如下：
- 从第一个元素开始，该元素可以认为已经被排序；
- 取出下一个元素，在已经排序的元素数列中从后向前扫描；
- 如果该元素（已排序）大于新元素，将该元素移到下一位置；
- 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
- 将新元素插入到该位置后；
- 重复步骤2~5。

### 3.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Insertion%20Sort.gif)

### 3.3 代码实现
```java
//插入排序
public static void insertSort(int[] arr) {
	//遍历所有的数字
	for(int i=1;i<arr.length;i++) {
		//如果当前数字比前一个数字小
		if(arr[i]<arr[i-1]) {
			//把当前遍历数字存起来
			int temp=arr[i];
			int j;
			//遍历当前数字前面所有的数字
			for(j=i-1;j>=0&&temp<arr[j];j--) {
				//把前一个数字赋给后一个数字
				arr[j+1]=arr[j];
			}
			//把临时变量（外层for循环的当前元素）赋给不满足条件的后一个元素
			arr[j+1]=temp;
		}
	}
}
```
### 3.4 算法分析
插入排序在实现上，通常采用in-place排序（即只需用到O(1)的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

## 4、希尔排序(Shell Sort)
1959年Shell发明，第一个突破O(n2)的排序算法，是简单插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫缩小增量排序。

### 4.1 算法描述
先将整个待排序的记录数列分割成为若干子数列分别进行直接插入排序，具体算法描述：
- 选择一个增量数列x1，x2，…，xk，其中xi>xj，xk=1；
- 按增量数列个数k，对数列进行k 次排序；
- 每次排序，根据对应的增量ti，将待排数列分割成若干长度为m 的子数列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个数列作为一个表来处理，表长度即为整个数列的长度。

### 4.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Shell%20Sort.gif)

### 4.3 代码实现
```java
//希尔排序
public static void shellSort(int[] arr) {
	int k = 1;
	// 遍历所有的步长
	for (int d = arr.length / 2; d > 0; d /= 2) {
		// 遍历所有有元素
		for (int i = d; i < arr.length; i++) {
			// 遍历本组中所有的元素
			for (int j = i - d; j >= 0; j -= d) {
				// 如果当前元素大于加上步长后的那个元素
				if (arr[j] > arr[j + d]) {
					int temp = arr[j];
					arr[j] = arr[j + d];
					arr[j + d] = temp;
				}
			}
		}
		System.out.println("第" + k + "次排序结果：" + Arrays.toString(arr));
		k++;
	}
}
```
### 4.4 算法分析

希尔排序的核心在于间隔数列的设定。既可以提前设定好间隔数列，也可以动态的定义间隔数列。动态定义间隔数列的算法是《算法（第4版）》的合著者Robert Sedgewick提出的。

## 5、选择排序(Select Sort)

工作原理：首先在未排序数列中找到最小（大）元素，存放到排序数列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序数列的末尾。以此类推，直到所有元素均排序完毕。

### 5.1 算法描述

n个记录的直接选择排序可经过n-1次直接选择排序得到有序结果。具体算法描述如下：

- 初始状态：无序区为R[1..n]，有序区为空；
- 第i次排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n)。该次排序从当前无序区中-选出关键字最小的记录R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
- n-1次结束，数组有序化了。

### 5.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Select%20Sort.gif)

### 5.3 代码实现

```java
//选择排序
public static void selectSort(int[] arr) {
	//遍历所有的数
	for(int i=0;i<arr.length;i++) {
		int minIndex=i;
		//把当前遍历的数和后面所有的数依次进行比较，并记录下最小的数的下标
		for(int j=i+1;j<arr.length;j++) {
			//如果后面比较的数比记录的最小的数小。
			if(arr[minIndex]>arr[j]) {
				//记录下最小的那个数的下标
				minIndex=j;
			}
		}
		//如果最小的数和当前遍历数的下标不一致,说明下标为minIndex的数比当前遍历的数更小。
		if(i!=minIndex) {
			int temp=arr[i];
			arr[i]=arr[minIndex];
			arr[minIndex]=temp;
		}
	}
}
```
### 5.4 算法分析

表现最稳定的排序算法之一，因为无论什么数据进去都是O(n2)的时间复杂度，所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。理论上讲，选择排序可能也是平时排序一般人想到的最多的排序方法了吧。

## 6、堆排序(Heap Sort)

堆排序(Heapsort)是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

### 6.1 算法描述

- 将初始待排序关键字数列(R1,R2….Rn)构建成大顶堆，此堆为初始的无序区；
- 将堆顶元素R[1]与最后一个元素R[n]交换，此时得到新的无序区(R1,R2,……Rn-1)和新的有序区(Rn),且满足R[1,2…n-1]<=R[n]；
- 由于交换后新的堆顶R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,……Rn-1)调整为新堆，然后再次将R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2….Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为n-1，则整个排序过程完成。

### 6.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Heap%20Sort.gif)

### 6.3 代码实现

```java
//堆排序
public static void heapSort(int[] arr) {
	//开始位置是最后一个非叶子节点，即最后一个节点的父节点
	int start = (arr.length-1)/2;
	//调整为大顶堆
	for(int i=start;i>=0;i--) {
		maxHeap(arr, arr.length, i);
	}
	//先把数组中的第0个和堆中的最后一个数交换位置，再把前面的处理为大顶堆
	for(int i=arr.length-1;i>0;i--) {
		int temp = arr[0];
		arr[0]=arr[i];
		arr[i]=temp;
		maxHeap(arr, i, 0);
	}
}

public static void maxHeap(int[] arr,int size,int index) {
	//左子节点
	int leftNode = 2*index+1;
	//右子节点
	int rightNode = 2*index+2;
	int max = index;
	//和两个子节点分别对比，找出最大的节点
	if(leftNode<size&&arr[leftNode]>arr[max]) {
		max=leftNode;
	}
	if(rightNode<size&&arr[rightNode]>arr[max]) {
		max=rightNode;
	}
	//交换位置
	if(max!=index) {
		int temp=arr[index];
		arr[index]=arr[max];
		arr[max]=temp;
		//交换位置以后，可能会破坏之前排好的堆，所以，之前的排好的堆需要重新调整
		maxHeap(arr, size, max);
	}
}
```

## 7、归并排序(Merge Sort)

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法(Divide and Conquer)的一个非常典型的应用。将已有序的子数列合并，得到完全有序的数列；即先使每个子数列有序，再使子数列段间有序。若将两个有序表合并成一个有序表，称为2-路归并。

### 7.1 算法描述

- 把长度为n的输入数列分成两个长度为n/2的子数列；
- 对这两个子数列分别采用归并排序；
- 将两个排序好的子数列合并成一个最终的排序数列。

### 7.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Merge%20Sort.gif)

### 7.3 代码实现

```java
//归并排序
public static void mergeSort(int[] arr,int low,int high) {
	int middle=(high+low)/2;
	if(low<high) {
		//处理左边
		mergeSort(arr, low, middle);
		//处理右边
		mergeSort(arr, middle+1, high);
		//归并
		merge(arr,low,middle,high);
	}
}

public static void merge(int[] arr,int low,int middle, int high) {
	//用于存储归并后的临时数组
	int[] temp = new int[high-low+1];
	//记录第一个数组中需要遍历的下标
	int i=low;
	//记录第二个数组中需要遍历的下标
	int j=middle+1;
	//用于记录在临时数组中存放的下标
	int index=0;
	//遍历两个数组取出小的数字，放入临时数组中
	while(i<=middle&&j<=high) {
		//第一个数组的数据更小
		if(arr[i]<=arr[j]) {
			//把小的数据放入临时数组中
			temp[index]=arr[i];
			//让下标向后移一位；
			i++;
		}else {
			temp[index]=arr[j];
			j++;
		}
		index++;
	}
	//处理多余的数据
	while(j<=high) {
		temp[index]=arr[j];
		j++;
		index++;
	}
	while(i<=middle) {
		temp[index]=arr[i];
		i++;
		index++;
	}
	//把临时数组中的数据重新存入原数组
	for(int k=0;k<temp.length;k++) {
		arr[k+low]=temp[k];
	}
}
```

### 7.4 算法分析

归并排序是一种稳定的排序方法。和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，因为始终都是O(nlogn)的时间复杂度。代价是需要额外的内存空间。

## 8、基数排序(Radix Sort)

基数排序是按照低位优先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前。

### 8.1 算法描述

取得数组中的最大数，并取得位数；
arr为原始数组，从最低位开始取每个位组成radix数组；
对radix进行计数排序(利用计数排序适用于小范围数的特点)；

### 8.2 动画演示
![image](https://github.com/wangfei910/wangfei910.github.io/raw/master/_pic/Radix%20Sort.gif)

### 8.3 代码实现

```java
//基数排序（普通方法实现）
public static void  radixSort(int[] arr) {
	//存最数组中最大的数字
	int max=Integer.MIN_VALUE;
	for(int i=0;i<arr.length;i++) {
		if(arr[i]>max) {
			max=arr[i];
		}
	}
	//计算最大数字是几位数
	int maxLength = (max+"").length();
	//用于临时存储数据的数组
	int[][] temp = new int[10][arr.length];
	//用于记录在temp中相应的数组中存放的数字的数量
	int[] counts = new int[10];
	//根据最大长度的数决定比较的次数
	for(int i=0,n=1;i<maxLength;i++,n*=10) {
		//把每一个数字分别计算余数
		for(int j=0;j<arr.length;j++) {
			//计算余数
			int ys = arr[j]/n%10;
			//把当前遍历的数据放入指定的数组中
			temp[ys][counts[ys]] = arr[j];
			//记录数量
			counts[ys]++;
		}
		//记录取的元素需要放的位置
		int index=0;
		//把数字取出来
		for(int k=0;k<counts.length;k++) {
			//记录数量的数组中当前余数记录的数量不为0
			if(counts[k]!=0) {
				//循环取出元素
				for(int l=0;l<counts[k];l++) {
					//取出元素
					arr[index] = temp[k][l];
					//记录下一个位置
					index++;
				}
				//把数量置为0
				counts[k]=0;
			}
		}
	}
}
```

```java
//基数排序（用队列实现）
public static void  radixQueueSort(int[] arr) {
	//存最数组中最大的数字
	int max=Integer.MIN_VALUE;
	for(int i=0;i<arr.length;i++) {
		if(arr[i]>max) {
			max=arr[i];
		}
	}
	//计算最大数字是几位数
	int maxLength = (max+"").length();
	//用于临时存储数据的队列的数组
	MyQueue[] temp = new MyQueue[10];
	//为队列数组赋值
	for(int i=0;i<temp.length;i++) {
		temp[i]=new MyQueue();
	}
	//根据最大长度的数决定比较的次数
	for(int i=0,n=1;i<maxLength;i++,n*=10) {
		//把每一个数字分别计算余数
		for(int j=0;j<arr.length;j++) {
			//计算余数
			int ys = arr[j]/n%10;
			//把当前遍历的数据放入指定的队列中
			temp[ys].add(arr[j]);
		}
		//记录取的元素需要放的位置
		int index=0;
		//把所有队列中的数字取出来
		for(int k=0;k<temp.length;k++) {
			//循环取出元素
			while(!temp[k].isEmpty()) {
				//取出元素
				arr[index] = temp[k].poll();
				//记录下一个位置
				index++;
			}
		}
	}
}
```

### 8.4 算法分析

基数排序基于分别排序，分别收集，所以是稳定的。但基数排序的性能比桶排序要略差，每一次关键字的桶分配都需要O(n)的时间复杂度，而且分配之后得到新的关键字数列又需要O(n)的时间复杂度。假如待排数据可以分为d个关键字，则基数排序的时间复杂度将是O(d*2n)，当然d要远远小于n，因此基本上还是线性级别的。

基数排序的空间复杂度为O(n+k)，其中k为桶的数量。一般来说n>>k，因此额外空间需要大概n个左右。