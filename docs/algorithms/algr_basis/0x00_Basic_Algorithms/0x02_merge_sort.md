# 0x02 归并排序
---
## 1. 什么是归并排序

归并排序和快速排序类似，都是一种基于分而治之（Divide and Conquer）的排序思想。

与快速排序相反的是，快速排序是划分并调整左右区间，随后对左右区间重复过程；而归并排序是划分左右区间，对左右区间重复过程，而后归并左右区间。并且归并排序是**稳定**的。

此外，归并排序在归并左右区间时需要一个额外的数组进行存储数据，

归并排序的步骤可以分为三步：

1. 划分左右区间
2. 对左右区间进行归并排序
3. 归并左右区间

归并排序的实际排序发生在第三步：在边界条件区间长度为2时，因为长度为1会直接返回，因此会到达第三步，也就是归并左右区间，这样的话就会让这个区间长度为2的数组变得有序，而后不断合并有序区间就显得简单不少了。

## 2. 代码实现

归并排序的边界条件没有快速排序那么麻烦，只需要注意归并即可。

```cpp
#include <iostream>
#include <cstdio>
using namespace std;

const int N = 1e5 + 10;
int a[N], tmp[N];
int n;

void merge_sort(int left, int right) {
    if (left >= right) return;
    int mid = left + right >> 1;
    merge_sort(left, mid), merge_sort(mid + 1, right);
    int pl = left, pr = mid + 1, pt = 0;
    while (pl <= mid && pr <= right) {
        if (a[pl] <= a[pr]) {
            tmp[pt++] = a[pl++];
        } else {
            tmp[pt++] = a[pr++];
        }
    }
    while (pl <= mid)   tmp[pt++] = a[pl++];
    while (pr <= right) tmp[pt++] = a[pr++];
    for (int i = left, j = 0; j < pt; i++, j++) {
        a[i] = tmp[j];
    }
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) scanf("%d", &a[i]);
    merge_sort(0, n - 1);
    for (int i = 0; i < n; i++) printf("%d ", a[i]);
    
    return 0;
}
```

## 3. 时间复杂度分析

每次会对半划分区间，并对区间长度进行归并排序，对于每个区间需要区间长度的归并过程。

```
T(n) = 2 * T(n/2) + cn
T(n) = 2 * n/2 + 2*2*T(n/4) + cn
T(n) = cn + n/2 * 2 + n/4 * 4 + ... + 1 * n
T(n) = n * log n
```

## 4. [拓展] 逆序对的数量

```
给定一个长度为 n 的整数数列，请你计算数列中的逆序对的数量。

逆序对的定义如下：对于数列的第 i 个和第 j 个元素，如果满足 i<j 且 a[i]>a[j]，则其为一个逆序对；否则不是。
```

本题可以借助分而治之的思想。对于一个区间内的逆序对数量，可以视为

1. 左区间逆序对的数量
2. 右区间逆序对的数量
3. 当前区间逆序对的数量，即左右区间合并的逆序对的数量

可以看出关键点在于第三步，而1、2步分治下去的边界条件也是第三步。

对于第三步，我们可以借助归并排序。在排序的同时统计出当前区间逆序对的数量。首先左右区间内的逆序对已经统计好，那么在于左右区间合并时的逆序对数量。

在左右区间归并时，如果发现右区间内有小于左区间的数字，已知左右区间都已经归并排序完成，那么假设当前左区间下标为`pl`，右区间当前下标为`pr`，分界点为`mid`（属于左区间）。那么就可以得知

```
for x in a[pl, mid]:
	x <= a[pl]
a[pl] < a[pr]
for x in a[pl, mid]:
	a[pr] > x && a[pr].index > x.index
	// 左区间的下标严格小于右区间
```

此时对于`a[pr]`这一个元素来说，在当要合并的左右区间，出现了`mid - pl + 1`个逆序对。对于后面的元素同理。这样就完成了对当前区间（左右区间合并出现的逆序对）的逆序对数量计算。

需要注意值溢出问题，对于一个长度为n的数组来说，逆序对数量最多的情况为该数组为逆序的。此时第一个元素与后n-1个元素互为逆序对，第二个与后n-2个。最终共有(n - 1 + 2) * (n - 1) / 2个，是n^2^的数量级。当n为1e5时，n^2^为1e10，int会溢出。

```cpp
#include <iostream>
#include <cstdio>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
int a[N], t[N];
int n;

ll merge_sort(int left, int right) {
    if (right <= left)  return 0;
    int mid = left + right >> 1;
    ll res = merge_sort(left, mid) + merge_sort(mid + 1, right);
    int pl = left, pr = mid + 1, pt = 0;
    while (pl <= mid && pr <= right) {
        if (a[pl] <= a[pr]) t[pt++] = a[pl++];
        else    t[pt++] = a[pr++], res += mid - pl + 1;
    }
    while (pl <= mid) t[pt++] = a[pl++];
    while (pr <= right) t[pt++] = a[pr++];
    for (int i = left, j = 0; j < pt; i++, j++) a[i] = t[j];
    return res;
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i++) scanf("%d", &a[i]);
    cout << merge_sort(0, n - 1) << endl;
    
    return 0;
}
```
