# 0x01 快速排序
---
## 1. 什么是快速排序

快速排序基于**分而治之（Divide and conquer）**的思想。

输入是一串数字（整形数组），输出是一串有序的数字。

1. 确定分界点
   1. 分界点可以是`l + r >> 1`，边界条件（2个数字）即为左边界，因为向下取整。
   2. 可以是`l + r + 1 >> 1`，边界条件是右边界，向上取整。
2. 然后分别调整左右区间，使左边界<= pivot，右边界>= pivot。
   1. 左右边界不一定以`pivot`原来的位置划分。
3. 递归处理左右边界。

快速排序是不稳定的，因为在调整左右边界时，会打乱数字顺序，递归划分时不能把顺序调整回来。

相应的，可以给每个数字添加上它的位置信息，在比较同样的数字，可以再比较其位置，从而把同样的值的元素变稳定。这样快速排序就稳定了。

## 2. 关键点

快速排序的关键点在于第二部，如何分别调整左右区间。这一步的目的是将区间划分为<= pivot的元素和>= pivot元素的左右两个区间。

### 2.1. 朴素做法

遍历原区间所有元素，小于等于pivot的元素，加入左区间；大于等于pivot的放入右区间。而后合并左右区间至原区间。

```c
int ori[], left[], right[];
int pl = 0, pr = 0;
// divide the left and right intervals
for (int i = 0; i < length; i++) {
    if (ori[i] <= pivot)	left[pl++] = ori[i];
    else	right[pr++] = ori[i];
}
// merge them to original array
for (int i = 0; i < pl; i++)	ori[i] = left[i];
for (int i = pl, j = 0; j < pr; i++, j++)	ori[i] = right[j];
```

### 2.2. 双指针做法

使用两个指针，左指针指向数组起始点，右指针指向终点。左指针右移，遇到大于等于pivot的停下；右指针左移，遇到小于等于pivot的停下。当双指针都停下时，交换双指针的元素。重复，直到双指针相遇。

这一过程中，左指针之前的元素一定满足左区间的要求，右指针同理。因此双指针相遇时，就划分好了左右区间。

```c
int pl = start - 1, pr = end + 1;
int pivot = a[pl + pr >> 1];
while (pl <= pr) {
    do pl++; while (a[pl] >= pivot);
    do pr--; while (a[pr] <= pivot);
    if (pl < pr)	swap(a[pl], a[pr]);
}
quick_sort(start, pr), quick_sort(pr + 1, end)
```

​	注意边界问题，如果选取的`pivot = a[pl + pr + 1 >> 1]`需要在最后的边界改为`start, pl - 1; pl, end`。

## 3. 时间复杂度分析

对于快速排序，会对区间划分`log n`层，每一层的调整左右区间都是`n`次。总的时间复杂度为**`O(n * log n)`**.

## 4. [拓展] 快速选择算法

快速选择算法（Quick Select）可以快速地在一个无序区间内选择出第**`K`**大/小的数字。时间复杂度为**`O(n)`**.

首先我们知道快速排序的调整左右区间这一过程，可以将当前区间分为两个区间，左边小于等于pivot，右边大于。这时候我们就能发现我们要找的K所在的区间，随后对K所在的区间进行调整左右区间这一过程，不断缩小区间，最终`a[K - 1]`就是要找的数字。

```cpp
#include <iostream>
using namespace std;

const int N = 1e5 + 10;
int n, k;
int a[N];

int quick_select (int left, int right, int k) {
    if (left == right) return a[left];
    int l = left - 1, r = right + 1;
    int mid = a[l + r >> 1];
    while (l < r) {
        do l++; while (a[l] < mid);
        do r--; while (a[r] > mid);
        if (l < r) swap(a[l], a[r]);
    }
    int front_l = r - left + 1;
    int back_l = right - r;
    if (k <= front_l) {
        return quick_select(left, r, k);
    } else {
        return quick_select(r + 1, right, k - front_l);
    }
}

int main() {
    cin >> n >> k;
    for (int i = 0; i < n; i++) cin >> a[i];
    cout << quick_select(0, n - 1, k) << endl;
    return 0;
}
```

对于时间复杂度，每次划分的区间减少一半，从n到1，总和为`S = n + n/2 + n/4 + ... + 1`。通过等比数列求和公式和求极限可以得出最终的时间复杂度为**`O(n)`**.
