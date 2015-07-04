---
layout: post
title: "排序算法之冒泡排序（Bubble Sort）"
date: 2011-04-15 14:56
comments: true
categories: [Algorithm]
---

**冒泡排序**是指依次对序列中相邻的两个元素作比较，若顺序不当，则进行交换，如此反复执行，直至序列有序即可。  

### 一、基本原理
若现在要对一组整数进行冒泡排序，则大致过程为：依次比较相邻的两个数，将小的数放在前面，大的数放在后面。第一趟中，比较第1个数和第2个数，将小的放在前面，大的放在后面，然后再比较第2个数和第3个数，同样小的放在前面，大的放在后面，如此继续，直到比较最后的两个数，此时小数放前，大数放后，这样经过第一趟的排序，最后一个数便是最大的数。然后进行第二趟，同样从第1个和第2个数开始，小数放前，大数放后，直到比较完倒数第二个数，这样经过第二趟排序，放在倒数第二个位置上的数是第二大的数，如此下去，直到所有的数都按序找到自己的位置，排序便完成。   
<!-- more -->
**冒泡排序**此名便是由于在排序过程中，小数总是在前，大数总是在后，就像水中的气泡一般总是往上升，故得之。

### 二、示例
让我们以整数数组“5 1 4 2 8”为例，用冒泡排序算法进行递增排序，过程如下：    
**第一趟：**    
(**5 1** 4 2 8) ==> (**1 5** 4 2 8)， 5 > 1，故交换    
(1 **5 4** 2 8) ==> (1 **4 5** 2 8)， 5 > 4，故交换  
(1 4 **5 2** 8) ==> (1 4 **2 5** 8)， 5 > 2，故交换  
(1 4 2 **5 8**) ==> (1 4 2 **5 8**)， 5 < 8，不交换   
**第二趟：**  
(**1 4** 2 5 8) ==> (**1 4** 2 5 8),   
(1 **4 2** 5 8) ==> (1 **2 4** 5 8)， 4 > 2, 故交换   
(1 2 **4 5** 8) ==> (1 2 **4 5** 8),   
(1 2 4 **5 8**) ==> (1 2 4 **5 8**)   
**第三趟：**（虽然到此步，数组元素已经有序了，但是我们的算法并不知道，它需要继续执行，直到整个过程都不再需要交换时，它才知道已经完成排序了）   
(**1 2** 4 5 8) ==> (**1 2** 4 5 8)   
(1 **2 4** 5 8) ==> (1 **2 4** 5 8)   
(1 2 **4 5** 8) ==> (1 2 **4 5** 8)   
(1 2 4 **5 8**) ==> (1 2 4 **5 8**)   

### 三、实现
冒泡排序的c代码实现如下：
{% codeblock bubble_sort.c lang:c %}
//a is an array, n is the size of the array.
void bubble_sort(int a[], int n)
{
    int i, j, k;
    for(i = n-2; i >= 0; i--) {
        for(j = 0; j <= i; j++) {
            if(a[j] > a[j+1]) {
                k = a[j];
                a[j] = a[j+1];
                a[j+1] = k;
            }
        }
    }
}
{% endcodeblock %}
冒泡排序的java代码实现如下：
{% codeblock BubbleSort.java(version 1) lang:java %}
public static void bubbleSort1(int[] arr) {
    int temp;
    for(int i = 0; i < arr.length - 1; i++) {
        for(int j = 0; j < arr.length - i - 1; j++) {
            if(arr[j] > arr[j+1]) {
                temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
            }
        }
    }
}
{% endcodeblock %}
{% codeblock BubbleSort.java(version 2) lang:java %}
public static void bubbleSort2(int[] arr) {
    int j;
    boolean flag = true;
    int temp;

    while(flag) {
        flag = false;
        for(j  = 0; j < arr.length - 1; j++) {
            if(arr[j] > arr[j+1]) {
                temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
                flag = true;
            }
        }
    }
}
{% endcodeblock %}
