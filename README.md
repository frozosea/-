# 快速排序算法

## 概念

快速排序（英语：quicksort），通常称为 qsort（因为它在 C 语言标准库中的名称），是由英国计算机科学家托尼·霍尔于 1960 年在莫斯科国立大学工作期间开发的排序算法。

## 设计思路

它基于“分而治之”的原理，通过将数组分成两部分并递归地排序每一部分来工作。
1. 选择枢轴元素（pivot）：从数组中选择一个元素作为枢轴，用于将数组分成两部分。通常选择第一个元素、最后一个元素、中间元素或随机元素。
2. 分割（partition）：所有小于或等于枢轴元素的元素移动到数组的左侧，所有大于枢轴元素的元素移动到数组的右侧。枢轴元素在排序后的数组中处于正确的位置。
3. 递归排序：然后将算法递归地应用于枢轴元素左侧和右侧的子数组。

```C lang
void quicksort(array: T[n], int left, int right)
     if left < right
        int q = partition(array, left, right)
        quicksort(array, left, q)
        quicksort(array, q + 1, right)

```
<h5 align="center">图1-1 快速排序算法例如</h5>

要对整个数组进行排序，需要执行以下过程：quicksort(array, 0, length[array] - 1)

## 理论分析
从实际使用的角度来看，qsort 是最佳算法之一，具有最低的时间开销。然而，不能断言它是最好的算法之一。

其平均运行时间为 $O(nlogn)$，这是基于比较的算法的渐近最优运行时间。尽管在最坏情况下，对 $n$ 个元素的数组进行排序的时间可能是 $O(n^2)$，但在实际应用中，该算法是最快的之一。

#### 最坏情况下的运行时间
假设我们将数组划分为两部分，一部分包含 n-1 个元素，另一部分包含 1 个元素。由于分割过程需要 O(n) 的时间，我们得到运行时间 T(n) 的关系式:
$$T(n) = T(n-1) + \Theta(n) = \sum_{k=1}^{n} \Theta(k) = \Theta\left( \sum_{k=1}^{n} k \right) = \Theta(n^2)$$
我们看到，在最不平衡的划分情况下，运行时间为 $O(n^2)$。特别是，当数组初始排序时，就会发生这种情况。

## 优化改进

在本部分中，我们将讨论改进 C 语言库函数中的 qsort1()。快速排序（函数 qsort1）在处理随机数数组时表现良好，但如果输入是部分排序的序列或包含相同元素的子序列，算法的执行时间会显著增加，趋向于 $O(n^2)$。

改进方法是选择第一个元素、中间元素和最后一个元素的中位数作为枢轴（pivot）。这有助于避免与枢轴选择相关的最坏情况，并确保数组更均匀地划分。

三数中值选择：选择当前数组中的第一个元素、中间元素和最后一个元素。对这三个元素进行排序，并使用中间值作为枢轴。这提高了选择良好枢轴的概率。
- 枢轴移动：选择中间值后，将枢轴移动到第一个位置，以简化划分算法。
- 数组划分：围绕枢轴对数组进行划分，使所有小于枢轴的元素在左边，而所有大于枢轴的元素在右边。
- 递归排序：将算法递归应用于枢轴左侧和右侧的子数组。

```C lang
#include "qsort3.h"

// 交换两个整数的值
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// 找到三个数的中位数：第一个，中间的和最后一个
int median_of_three(int *x, int l, int u) {
    int m = (l + u) / 2; // 计算中间元素的索引

    // 确保x[l] <= x[m]，如果不是，则交换
    if (x[l] > x[m]) 
        swap(&x[l], &x[m]);

    // 确保x[l] <= x[u]，如果不是，则交换
    if (x[l] > x[u]) 
        swap(&x[l], &x[u]);

    // 确保x[m] <= x[u]，如果不是，则交换
    if (x[m] > x[u]) 
        swap(&x[m], &x[u]);

    // 返回中间元素的索引作为中位数
    return m;
}
```
<h5 align="center">图2-1 median函数C语言实行</h5>

```C lang
#include "qsort3.h"
#include <stdio.h>

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int median_of_three(int *x, int l, int u) {
    int m = (l + u) / 2;
    if (x[l] > x[m]) swap(&x[l], &x[m]);
    if (x[l] > x[u]) swap(&x[l], &x[u]);
    if (x[m] > x[u]) swap(&x[m], &x[u]);
    return m;
}

void qsort3(int *x, int l, int u) {
    int i = l;
    int j = u;

    if (l >= u)
        return;
    
    int pp = median_of_three(x, l, u);
    int pivot = x[pp];

    do {
        while (x[i] < pivot) {
            i++;
        }
        while (pivot < x[j]) {
            j--;
        }

        if (i <= j) {
            swap(&x[i], &x[j]);
            i++;
            j--;
        }

    } while (i <= j);


    if (l < j) {
        qsort3(x, l, j);
    }
    if (i < u) {
        qsort3(x, i, u);
    }
}

```
<h5 align="center">图2-2 C语言优化快速排序算法实现</h5>


## 实际运行和分析
<p align="center">
     <img width="507" alt="Снимок экрана 2024-06-21 в 18 38 57" src="https://github.com/frozosea/-/assets/99795132/5c1fefc9-18d0-472a-b3de-e31634dfb211">
</p>
<h5 align="center">表2.4-1 优化前后整形数据排序时间（Release配置版）（秒)</h5>


<p align="center">
     <img width="392" alt="Снимок экрана 2024-06-23 в 14 21 43" src="https://github.com/frozosea/-/assets/99795132/df0c1771-debe-475e-97af-4b04a17b14f5">
</p>
<h5 align="center">表2.4-2 从结构体数据看稳定性（65536大小）（Release配置版）（分)</h5>
