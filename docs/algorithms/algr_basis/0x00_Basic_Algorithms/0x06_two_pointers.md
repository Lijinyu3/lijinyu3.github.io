# 0x06 双指针算法

[toc]

---

## 1. 什么是双指针算法

双指针具体的定义我也解释不清，在我看来双指针更是一种技术、技巧，而非一种算法。在处理连续的字符串、数组等等时，本来的朴素方法没有合理地运用数据的性质，因此可以用双指针进行优化，将时间复杂度下降。所以我觉得双指针就是一种根据数据的性质，把时间复杂度降下来的技巧。

大致的模板为

```cpp
for (int i = 0, j = 0; i < n; i++) {
    while (i < j && check(i, j))	j++;
    ...
}
```

## 2. 例子

举一道例题，最长连续不重复子序列。

`给定一个长度为 n 的整数序列，请找出最长的不包含重复的数的连续区间，输出它的长度。`

对于这道题，首先想到朴素方法，遍历该序列，对于每一次当前的位置，找出最长的区间。这样做的时间复杂度是O(n^2^)的，首先遍历序列n，对于每一个位置还需要遍历一遍从开始到这个位置。

但是根据根据数列本身的性质，可以发现其实在第二层遍历的过程中存在很多不必要的遍历。在遍历数组的过程中，每次增加一个数字，那么对于新增加的这个数字来说新产生的有效的最长子序列只可能有两种：

1. 新数字的加入不影响之前序列的有效，那么有效的子序列为`原序列+新数字`。
2. 新数字的加入使原序列无效，即这个序列会产生重复数字，重复的数字就是新加入的数字。那么新的有效子序列就为`原序列中的新数字出现之后的位置~新数字`。

而如何判断新序列是否有效是一个问题，如果这里的时间复杂度不是常数时间的话，那么时间复杂度又会变成O(n^2^)，我们可以使用一个长度为n的数组存储已经出现过的数字的次数，如果新数字的加入后次数为2，那么就说明重复了。

这样我们就可以让第二个指针指向当前最长有效系列的起始位置，如果出现重复就让第二个指针向后走，走的过程把略去的数字出现数字减一，直到不重复。对于第一个指针每次遍历，都存一下最长的序列长度，就可以以O(n)的复杂度解题了。

写成伪代码就是：

```cpp
for (int i = 0, j = 0; i < n; i++) {
	record[a[i]]++;
    while (i < j && record[a[i]] > 1)	record[a[j--]]--;
    max_len = max(max_len, i - j + 1);
}
```

