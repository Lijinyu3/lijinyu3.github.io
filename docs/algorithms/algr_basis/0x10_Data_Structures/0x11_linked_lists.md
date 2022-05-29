# 0x11 单双链表

[toc]

---

## 1. 什么是单双链表

链表是链式结构的线性存储方式，不多赘述。这里的单双链表的特殊之处在于，是由数组模拟而出的链表。

链表主要用于树和图的邻接表，即n个单链表，用于存储每个点的临边。（第三章细讲）

## 2. 为什么使用数组模拟

而之所以使用数组模拟链表，是因为在算法竞赛中，时间是需要考虑的第一要素。传统链表在创建链表时`new`的过程太慢。而数组一次性开辟好足够的空间，速度要远快于传统的链表。（结构体与数组本质上同理）

数组模拟带来的劣势就是可维护性差，会有浪费的空间。但是在算法题目中，不需要考虑后续的维护性。

## 3. 如何使用数组模拟

### 3.1. 数组模拟单链表

单链表主要由1. `head`头节点指针；2. `nodes` 节点 组成。

其中对于每个`nodes`，包含两部分：1. 该节点的值，2. 指向下一节点的指针。而对于`head`，本质上也是一个`node`，只是这个节点用不到值，只需要指针即可。

其次，传统链表是动态分配管理，如果用数组模拟，即提前开好足够的空间等着用，那么就需要知道当前空间的使用情况，才能模拟出链表动态分配的过程。

综上，使用数组模拟单链表，需要

1. `val[]`数组，用于存储当前节点的值
2. `nxt[]`数组，用于存储下一节点的下标。（充当下一节点的指针）
3. `idx`变量，用于存储当前可用节点的下标，每次用完++。

而对于头结点，直接使用**第0个节点**代替。指向空节点的下标值用-1代替。

接下来给出实现过程

```c++
// data range
const int N = 1e5 + 10;
int val[N], nxt[N], idx;

// init before use
void init()
{
    nxt[0] = -1;
    idx = 1;
}

// insert v after the pos_th node
void insert(int pos, int v)
{
    val[idx] = v;
    nxt[idx] = nxt[pos];
    nxt[pos] = idx;
    idx++;
}

// delete the node after pos_th
void del(int pos)
{
    nxt[pos] = nxt[nxt[pos]];
}

void print_list(void)
{
    for (int i = 0; nxt[i] != -1; i = nxt[i])	cout << val[nxt[i]] << ' ';
}
```

### 3.2. 数组模拟双链表

与模拟单链表类似，双向链表对于每一个节点多了一个前指针，同时也多了一个尾结点。

因此需要多申请一个数组用作存储前指针，数组下标0已经作为头结点使用，那么1作为尾结点使用。

需要注意的是，模拟单链表时，如果是最后一个节点，那么后指针是值为-1。但是双链表因为最后一个节点是尾结点，因此判断是否是最后一个节点需要判断后指针是否是尾结点是下标。

```cpp
// data range
const int N = 1e5 + 10;
int val[N], pre[N], nxt[N], idx;
const int HEAD = 0, TAIL = 1;

// init before use
void init()
{
    pre[HEAD] = -1, nxt[HEAD] = TAIL;
    pre[TAIL] = HEAD, nxt[TAIL] = -1;
    idx = 2;
}

// insert v after pos_th node
void insert(int pos, int v)
{
    val[idx] = v;
    pre[idx] = pos, nxt[idx] = nxt[pos];
    nxt[pos] = pos, pre[nxt[idx]] = pos;
    idx++;
}

// delete the pos_th node
void del(int pos)
{
    nxt[pre[pos]] = nxt[pos];
    pre[nxt[pos]] = pre[pos];
}

// output the list
void print_list(void)
{
    for (int i = HEAD; nxt[i] != TAIL; i = nxt[i])	cout << val[nxt[i]] << ' ';
}
```

