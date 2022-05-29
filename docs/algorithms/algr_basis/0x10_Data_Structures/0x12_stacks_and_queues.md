# 0x12 栈和队列

[toc]

---

## 1. 什么是栈和队列

栈是LIFO (Last In First Out)，先进后出的数据结构；队列是一种FIFO (First In First Out)，先进先出的数据结构。简单的线性结构有时候不能满足我们的存储需求。

## 2. 为什么用数组模拟栈和队列

一般的栈和队列可能是由链表构成的，这样方便动态分配管理。但是同数组模拟链表的原因一样，每次new的过程太慢了。而链表模拟的trade off好处为方便动态管理，不浪费空间；缺点就是每次都需要动态分配，可能有点慢；其次不能O1访问其中的元素。

但是因为算法题只需要在意这一次的运行，不需要考虑后续的可维护性以及空间是否浪费，主要要求速度。因此数组模拟刚好正中下怀。

## 3. 数组模拟栈和队列的实现

### 3.1. 数组模拟栈

数组模拟栈比较简单。正常存入数组，再加一个变量存储栈顶的下标。入栈就++，出栈--。默认为-1。

```cpp
// stack array and top pointer for stack
int stk[N], top_p;

// init before use
void init()
{
    top_p = -1;
}

// push val into stack
void push(int val)
{
    stk[++top_p] = val;
}

// pop the top element from stack
void pop()
{
    top_p--;
}

// query the top element for stack
int query()
{
    return stk[top_p];
}

// query if the stack is empty
bool empty()
{
    return top_p < 0;
}
```

### 3.2. 数组模拟队列

数组模拟队列与模拟栈类似。正常存入数组，`head`存入数组头的下标，`tail`存入数组尾的下标。

```cpp
const int N = 1e5 + 10;
int queue[N], head, tail;

void init()
{
    head = 0, tail = -1;
}

void push(int val)
{
    queue[++tail] = val;
}

void pop()
{
    ++head;
}

int query()
{
    return queue[head];
}

bool empty()
{
    return head > tail;
}
```



## 4. 拓展

### 4.1. 表达式求值

#### 4.1.1. 题目

> 给定一个表达式，其中运算符仅包含 `+,-,*,/`（加 减 乘 整除），可能包含括号，请你求出表达式的最终值。
>
> **注意：**
>
> - 数据保证给定的表达式合法。
> - 题目保证符号 `-` 只作为减号出现，不会作为负号出现，例如，`-1+2`,`(2+2)*(-(1+1)+2)` 之类表达式均不会出现。
> - 题目保证表达式中所有数字均为正整数。
> - 题目保证表达式在中间计算过程以及结果中，均不超过 231−1231−1。
> - 题目中的整除是指向 00 取整，也就是说对于大于 00 的结果向下取整，例如 5/3=15/3=1，对于小于 00 的结果向上取整，例如 5/(1−4)=−15/(1−4)=−1。
> - C++和Java中的整除默认是向零取整；Python中的整除`//`默认向下取整，因此Python的`eval()`函数中的整除也是向下取整，在本题中不能直接使用。

#### 4.1.2. 简单运算

~~本题乍一看比较难~~，可以先从简单的地方下手，然后由易到难。

首先不妨先实现一个只有一个运算符，两个运算数的简单运算算法。详细来看就是取出两个运算数，根据运算符的不同选择不同的运算。表达式作为`string`类型读入，需要实现的点为

1. 如何将数字从string中读入
2. 如何从string中读入运算符
3. 如何根据运算符计算

第2比较简单，可以直接读入为`char`类型；第1点需要考虑后面的一位是不是还是数字，如果是的话，继续读入。第三点，条件判断，然后选择运算即可，需要注意**减法、除法这种注重运算数顺序的运算应该考虑好位次**。

那么本质上需要实现的只有这个读入数字。

```cpp
// return the operand and change the index
// call this function if the current element is digit
int get_num(int &index)
{
    int number = expr[index] - '0';
    while (index + 1 < expr.size() && isdigit(expr[index + 1])) {
        number *= 10;
        number += expr[index + 1] - '0';
        index++;
    }
    return number;
}
// 这个函数有一个小trick，即它会让参数中的index索引不会干扰到正常循环的迭代
// 正常读入数字后，如果紧跟着的几个数组元素也是数字，那么数组下标会移动，因此为了正常的循环迭代，需要将index也移动。
// 但读到哪一位就移动到哪一位，因为循环结束后还会移动数组下标，因此移动数组下标不应影响正常的循环迭代。
```

#### 4.1.3. 不考虑次序的复合运算

接下来再考虑复杂一些，复合运算。因为运算符之间存在优先级，暂不考虑这个复杂因素。现在要实现的是不考虑次序，计算一个符合运算表达式。

复合运算表达式本质上也是由4.1.2.的简单运算组成的，而简单运算由两个运算数和一个运算符组成。所以计算复合运算其实就是计算一个个简单运算，而计算一个个简单运算就是拿到一个运算符（双目，本题没有单目运算符，如负号）和其相匹配的两个运算数。完成本次计算后，得到了一个结果，而这个结果又作为一个新的运算数继续参与运算。直到没有运算符，那么当前也应只剩下一个运算数，即为结果。

可以观察到，运算数在取出最新的两个数后，要立即存入一个数参与最新的运算。这个特征符合栈的LIFO（后进先出）特征。故用栈存取。运算符暂时没有特殊要求。

```cpp
const int N = 1e5 + 10;
int operators[N], operands[N];
int top_operators = -1, top_operands = -1;
// 此处用数组模拟栈

void eval()
{
    // 注意次序，栈的顺序相反。
    int n2 = operands[top_operands--], n1 = operands[top_operands--];
    char op = operators[top_operators--];
    int result;
    if (op == '+')	result = n1 + n2;
    ...
    // push result onto the stack as new operand
    operands[++top_operands] = res;
}

// 函数实现省略，参考上面的
int get_num(int &index);

int main()
{
    string expr;
    cin >> expr;
    for (int i = 0; i < expr.size(); i++) {
        if (isdigit(expr[i])) {
            int num = get_num(i);
            operands[++top_operands] = num;
        } else {
            operators[++top_operators] = expr[i];
        }
    }
    while (top_operators >= 0) eval();
    cout << operands[0] << endl;
}
```

#### 4.1.4. 考虑次序不考虑括号的复合运算

上面已经实现了不考虑次序的复合运算。现在实现考虑次序但不考虑括号的。

首先考虑上述的实现对运算次序的影响，即，会忽略加减的运算优先级小于乘除。因为运算符也被压入栈中，因此实际上在取出时是逆序的（LIFO），那么考虑以下情况（当前运算符用`op`表示，前一个运算符用`pre`表示）：

1. op＜pre（如，op为乘法，pre为加减），那么不影响运算次序。
2. op＞pre（如，op为加减，pre为乘除），如果这样就会影响正常次序。
3. op=pre（如，当前是加减，pre也是加减），也会影响次序。(2-3+5，先算3+5是错的）

对于2、3的解决办法为，立即计算前一个运算即可。

```cpp
bool cmp_op(char op)
{
    char pre_op = operators[top_operators];
    if (op == '*' || op == '/') {
        if (pre_op == '+' || pre_op == '-')	return true;
    }
    return false;
}

void eval();
int get_num(int &index);

int main()
{
    string expr;
    cin >> expr;
    for (int i = 0; i < expr.size(); i++) {
        if (isdigit(expr[i])) {
            ...
        } else {
            while (top_operators >= 0 && !cmp_op(expr[i]))	eval();
            operators[++top_operators] = expr[i];
        }
    }
    while (top_operators >= 0)	eval();
    cout << operands[0] << endl;
}
```

#### 4.1.5. 考虑括号的复合运算

最后，将括号考虑入内。括号内的表达式本质上就是一个运算表达式，但是需要优先计算。可以使用上述的算法进行优先计算，然后把结果压入运算数栈中。

```cpp
bool cmp(char op)
{
    char pre_op = operators[top_operators];
    if (pre_op == '(')	return false;
    if (op == '*' || op == '/') {
        if (pre_op == '+' || pre_op == '-')	return true;
    }
    return false;
}

void eval();

int get_num(int &index);

int main()
{
    string expr;
    cin >> expr;
    for (int i = 0; i < expr.size(); i++) {
        if (isdigit(expr[i])) {
            ...
        } else if (expr[i] == '(') {
            operators[++top_operators] = expr[i];
        } else if (expr[i] == ')') {
            while (operators[top_operators] != ')')	eval();
            top_operators--;
        } else {
            while (top_operators >= 0 && !cmp_op(expr[i]))	eval();
            operators[++top_operators] = expr[i];
        }
    }
    ...
}
```

### 4.2. 单调栈

#### 4.2.1. 题目

>给定一个长度为 N 的整数数列，输出每个数左边第一个比它小的数，如果不存在则输出 −1。
>
>#### 输入格式
>
>第一行包含整数 N，表示数列长度。
>
>第二行包含 N 个整数，表示整数数列。
>
>#### 输出格式
>
>共一行，包含 N 个整数，其中第 i 个数表示第 i 个数的左边第一个比它小的数，如果不存在则输出 −1。
>
>#### 输入样例：
>
>```
>5
>3 4 2 7 5
>```
>
>#### 输出样例：
>
>```
>-1 3 -1 2 2
>```

#### 4.2.2. 朴素做法

朴素做法是对于数组中的每个元素，都从当前位置遍历到数组头，找到比它小的数。时间复杂度为

$$
\sum_{i=0}^n i=(1+n)\times n \div 2 = {n^2 + n \over 2} = O(n^2)
$$

#### 4.2.3. 单调栈做法

现在考虑其中一些没有考虑到的性质和重复的计算。首先，要寻找的是当前数字之前第一个比它小的数。假设当前数为`n`，前一个数字为`n-1`，如果`n > n-1`，那么对于后续数字来说，`n-1`没有任何存在的必要性。因此，对于每一个$n \in array$，都应做到$n > n-1, n下标\ne 0$.

```cpp
for (int i = 0; i < n; i++) {
        int element;
        cin >> element;
        while (top > -1 && element <= stk[top])  top--;
        if (top < 0)    cout << -1 << ' ';
        else    cout << stk[top] << ' ';
        stk[++top] = element;
}
```

现在分析时间复杂度，对于每个元素，只会入栈与出栈一次。总时间复杂度为$2n = O(n)$。

### 4.3. 单调队列

#### 4.3.1. 题目

>给定一个大小为 n≤10^6^ 的数组。
>
>有一个大小为 kk 的滑动窗口，它从数组的最左边移动到最右边。
>
>你只能在窗口中看到 k 个数字。
>
>每次滑动窗口向右移动一个位置。
>
>以下是一个例子：
>
>该数组为 `[1 3 -1 -3 5 3 6 7]`，k 为 33。
>
>| 窗口位置            | 最小值 | 最大值 |
>| :------------------ | :----- | :----- |
>| [1 3 -1] -3 5 3 6 7 | -1     | 3      |
>| 1 [3 -1 -3] 5 3 6 7 | -3     | 3      |
>| 1 3 [-1 -3 5] 3 6 7 | -3     | 5      |
>| 1 3 -1 [-3 5 3] 6 7 | -3     | 5      |
>| 1 3 -1 -3 [5 3 6] 7 | 3      | 6      |
>| 1 3 -1 -3 5 [3 6 7] | 3      | 7      |
>
>你的任务是确定滑动窗口位于每个位置时，窗口中的最大值和最小值。
>
>#### 输入格式
>
>输入包含两行。
>
>第一行包含两个整数 n 和 k，分别代表数组长度和滑动窗口的长度。
>
>第二行有 n 个整数，代表数组的具体数值。
>
>同行数据之间用空格隔开。

#### 4.3.2. 朴素做法

首先考虑朴素做法，对于每个滑动窗口，找出最大、小值需要k次，k为滑动窗口的范围。总共需要`n-k+1`次。时间复杂度为

$$
Time\ Complexity:\ \sum_{i=k}^n2k = 2(nk-k^2+k)=2k(n-k+1)=O(nk)
$$

这里数量级在1e6，n取1e6，k取1e3为最大值，最终复杂度约为1e9，很明显是不行的。

#### 4.3.3. 单调队列做法

因本题求最大值本质上和求最小值的思路类似，只要解出最大值的做法，最小值做法也相应就出来了。因此只考虑最小值。

观察朴素做法时，可以发现，如果求最小值，

$$
\forall\ element_i \in 滑动窗口, if\ element_{i-1} \geq element_i,\ then\ element_{i-1}\ is\ useless.
$$

 也就是说，单调队列不断移动时，越靠后的元素生命周期越长，因此后面的元素的生命周期永远比前面的长；如果靠前的元素>=靠后的元素，那么在靠前的元素生命周期内，它将永远不会被选中，因为后面有比它小的元素。那么如果它的生命周期内一直不被选中，那该元素就是无用元素，可以去除。

进一步可以推论，如果滑动窗口是不严格单调递增的，那么所有影响严格单调递增的元素都是无用元素，可以去除。最终滑动窗口应是一个严格单调递增的队列，而窗口内的最小值应是队首的元素。

现在考虑时间复杂度，因为所有输入元素只会入队一次，至多出队一次（最后一次的滑动窗口内可能有元素不出队），最终的时间复杂度为$O(n)$。

因此，本题只需要维护一个严格单调的滑动队列，在有元素不断入队仍然保证严格单调，同时保证队列元素在队列内应随着窗口的滑动而相应出队即可。

首先考虑如何维护一个严格单调的滑动队列。基础情况时，只有一个元素，队列一定严格单调；当有新元素进入时，如果队尾元素即为最大值，如果新元素大于该元素，那么直接队尾入队，队列仍然单调，反之，队尾出队，继续比较新队尾，直到符合第一种情况，入队。

该队列的特征为LIFO（后进先出），可以考虑栈实现。

其次考虑如何保证队列内元素会随着窗口的滑动而出队。为了维护严格单调，该队列失去了元素的连续紧密性，因此无法通过队列内元素数量而判断是否出队。只能通过元素自身所带有的特征来判断该元素是否属于当前滑动窗口。可以考虑在队列内存取元素在原数组中的下标，如果队首下标-当前元素下标超出滑动窗口范围，那么队首应出队。因为每次只会入队一个新元素，而基本情况时队列内元素无需要出队的，即每次至多只有一个需要出队的元素，因此每次只需要判断队首元素即可。

空间复杂度为$Space\ Complexity:全部元素数组n+单调队列数组n=O(n)$.实际上在空间上还可以继续优化。

最后，数据结构为需要双向出队的线性结构，可以考虑双向队列。本题采用数组模拟队列。

```cpp
const int N = 1e6 + 10;
int queue_min[N], num[N];
int tail_min, head_min;

void init()
{
    tail_min = -1;
    head_min = 0;
}

int main()
{
    init();
    
    int n, range;
    cin >> n >> range;
    for (int i = 0; i < n; i++) {
        cin >> num[i];    
    }
    for (int i = 0; i < n; i++) {
        // 维护队列单调性
        while (tail_min >= head_min && num[queue_min[tail_min]] >= num[i])   tail_min--;
        // 入队
        queue_min[++tail_min] = i;
        // 判断队首元素是否已经不属于滑动窗口
        if (i - queue_min[head_min] + 1 > range)    head_min++;
        // 满足滑动窗口位置，输出最小值
        if (i + 1 >= range) cout << num[queue_min[head_min]] << ' ';
    }
    cout << endl;
```

