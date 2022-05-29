# 0x04 高精度

[toc]

---

## 1. 什么是高精度

所谓高精度，是指高精度存储和运算。因为计算机存储数据的内存是有限的，对于整型数字，在Cpp最大的是`long long`类型即64位符号整数类型。最大能表示的数字范围也不过`-2^63 ~ 2^63 - 1`。对于更大类型的数字是存不下的。因此在涉及一些大数运算时，我们需要手动实现高精度的存储和运算。

在涉及高精度运算时，一般时间复杂度只与数字的位数（log~10~n)有关系，因为要逐位计算。

## 2. 高精度的存储

高精度的存储较为简单，我们可以将其存入变长数组中，如Cpp中的`vector`。

需要注意存储数字的顺序。当从标准输入读入数字存储时，实际上是逆序存储的。例如，

```cpp
string s;
s = "123456";
```

对于`s[0]`实际上是最高的位，而非最低的个位。对于加减乘这种需要对齐个位再运算的运算，很不方便。因此在存储大数时我们将其逆转后存储，以便后续运算。

比较方便的方法是先存入一个`string`字符串中，再逆序遍历字符串而后`push_back`到`vector`中。

要注意存入`vector`时，注意`- '0'`转换成整型。

## 3. 高精度加法

对于高精度加法，只需要正常列竖式计算两数之和一样相加即可计算。

需要注意如果当前位相加大于10的时候要进位。

```cpp
vector<int> add (vector<int> &n1, vector<int> &n2) {
    int carry = 0;
    vector<int> res;
    for (int i = 0; i < n1.size() || i < n2.size(); i++) {
        carry += i < n1.size() ? n1[i] : 0;
        carry += i < n2.size() ? n2[i] : 0;
        res.push_back(carry % 10);
        carry /= 10;
    }
    if (carry)	res.push_back(carry);
    return res;
}
```

现在分析一下高精度加法的时间复杂度。可以看出只和两个大数的位数有关系。

```
T(n) = c1 + T(n / 10)
T(n) = log 10 n
```

因此高精度加法的时间复杂度为**O(log~10~n)**。

## 4. 高精度减法

高精度减法与高精度加法类似，都是通过列竖式直接计算。但是可以发现会出现大数减小数和小数减大数两种情况。我们可以判断两数的大小，将两种情况化简为一种情况。

```cpp
bool cmp (vector<int> &n1, vector<int> &n2) {
    if (n1.size() != n2.size())	return n1.size() > n2.size();
    for (int i = n1.size(); i; i--) {
        if (n1[i - 1] != n2[i - 1])	return n1[n - 1] > n2[n - 1];
    }
    return true;
    // n1 == n2
}
if cmp(n1, n2) {
    putchar('-');
    sub(n2, n1);
}
```

而后，考虑竖式的计算。与加法类似，但是不是进位而是借位。因为我们假设了是大数减小数，因此永远有高位可借。

在计算结束后，还需要考虑消除掉前导0，即存储在数字前的无用0。需要注意如果结果长度只有一位，那么这个零是有效的。

```cpp
vector<int> sub (vector<int> &v1, vector<int> &v2) {
    if (!cmp(v1, v2)) {
        putchar('-');
        return sub(v2, v1);
    }
    int borrow = 0;
    vector<int> res;
    for (int i = 0; i < v1.size() || i < v2.size(); i++) {
        borrow += i < v1.size() ? v1[i] : 0;
        borrow -= i < v2.size() ? v2[i] : 0;
        res.push_back((borrow + 10) % 10);
        borrow = (borrow < 0 ? -1 : 0);
    }
    while (res.size() > 1 && res.back() == 0){
        res.pop_back();
    }
    return res;
}
```

高精度减法与高精度加法的时间复杂度一样，都只与数字的位数有关，即**O(log~10~n)**.

## 5. 高精度乘法

要注意这个高精度乘法并不是两个大数相乘，而是一个高精度大数与一个正常精度的整数相乘。

思路则依然是列竖式，把第二个乘数与大数的每一位相乘。需要注意的是最终计算结束有可能还有进位，需要判断一下，以及对前导0的消除。

```cpp
vector<int> mul (vector<int> &v1, int &times) {
    vector<int> res;
    int carry = 0;
    for (int i = 0; i < v1.size(); i++) {
        carry += v1[i] * times;
        res.push_back(carry % 10);
        carry /= 10;
    }
    while(carry != 0) {
        res.push_back(carry % 10);
        carry /= 10;
    }
    while (res.size() > 1 && res.back() == 0)   res.pop_back();
    return res;
}
```

## 6. 高精度除法

同乘法一样，除法也是大数除正常数。但是除法与加减乘法不同，除法从高位开始除。但因为我们大数的存储方式是逆序存储，因此在计算结束后需要逆转一下。

```cpp
vector<int> div (vector<int> &v1, int &n1) {
    int r = 0;
    vector<int> res;
    for (int i = v1.size() - 1; i >= 0; i--) {
        r *= 10;
        r += v1[i];
        res.push_back(r / n1);
        r %= n1;
    }
    reverse(res.begin(), res.end());
    while (res.size() > 1 && res.back() == 0) res.pop_back();
    n1 = r;
    return res;
}
```