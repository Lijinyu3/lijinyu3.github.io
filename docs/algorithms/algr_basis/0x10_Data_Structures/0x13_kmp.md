# 0x13 KMP

---

## 1. 模式匹配问题

模式匹配问题，也叫子字符串搜索问题（substring search）：给定一个长度为N的字符串，称其为`text`；和一个长度为M的字符串，称其为`pattern`；找出在`text`中`pattern`出现的位置。

```python
>>> text = 'lambdalambdalambda'
>>> pattern = 'lambda'
>>> ans = [i for i in range(0, len(text), len(pattern))]
# ans就是pattern在text中出现的首下标
>>> ans
[0, 6, 12]
# 验证
>>> [text[i:i + len(pattern)] for i in ans]
['lambda', 'lambda', 'lambda']
```

那怎么找出来呢？老规矩，还是朴素算法走起。对`text`中所有可能出现`pattern`字符串的位置，都搜索一遍，就能找出`pattern`在`text`中的所有位置了。而`text`中的每个字符后（包括其自身）都有可能出现`pattern`，除非不够`pattern`的长度了。(`bda`中肯定不可能出现`lambda`) 那么就写一个二重循环来搜索所有的可能。

```cpp
// 这里为了算法的简洁，只考虑搜索到的第一个位置。
// 如果想要找出所有的位置也不难，继续搜索把匹配到的位置加进数组就行。
int pattern_search(char *text, char *pattern) {
	const int N = strlen(text), M = strlen(pattern);
    // N, M for the length of text and pattern.
    for (int i = 0; i <= N - M; i++) {
        // 搜索text中所有可能的位置
        // N-M是刚好最后一个符合长度的位置
        for (int j = 0; j < M; j++) {
            if (text[i + j] != pattern[j]) {
                break;
            }
            if (j == M - 1) {
                return i;
                // 匹配成功
            }
        }
    }
    return N;
    // N作为text中的非法位置，预示着匹配失败
}
```

这个算法的时间复杂度上界为`O(n*m)`。外循环最差需要循环N次，内循环最差每次都要匹配到最后一位。为了更好的引入KMP算法，可以将上面的算法理解视角稍作转换。本来是对`text`中的每个位置可能出现的`pattern`长度的字符串进行匹配，现在理解为对`text`的每个字符和`pattern`中的每个字符进行匹配，一旦匹配失败，就意味着我们之前的匹配付诸东流，需要进行回退，从下一个字符开始匹配。可以将这个角度的理解写成另一个等价的算法。

```cpp
int pattern_search_v2(char *txt, char* ptn) {
    const int N = strlen(txt), M = strlen(ptn);
    int ai, mi;
    // ai for actual index; mi for matching index.
	for (ai = 0, mi = 0; ai < N && mi < M; ai++) {
        if (txt[ai] == ptn[mi]) mi++;
        // 当前字符匹配成功，继续匹配
        else {
            ai -= mi;
            mi = 0;
            // 匹配失败，回退。下一个字符位置重新来吧。
        }
    }
    if（mi == M) return ai - M;
    // 匹配成功，在ai结尾处完成匹配。
    else 		 return N;
}
```

虽然这样写看起来比较粘合和恶心，但是它的思想揭示了字符串匹配的一个过程：匹配-回退。

## 2. 优化重复

现在来观察这个匹配-回退的过程中，我们有哪些重复工作，可以优化掉哪些步骤。虽然比较晦涩，但是在我们匹配失败`text`前一个字符的位置时，在最后一个字符匹配失败前，我们已经匹配成功了一些字符，而这些字符就是我们要回退回去的起点或更未来要回退的起点。

```python
txt = '456783456456789'
ptn = '456789'
# 假设我们刚刚对下标0的位置进行匹配，发现最后匹配失败了，456783和456789不匹配。
# 那我们也就知道了下一个要回退的位置，下标1，值为5，和我们的pattern字符串中第一个字符不匹配
```

以上述代码为例，在下标5的位置(值为'9')匹配失败，我们其实没有必要回退到下标1，因为我们已经知道它和`pattern`中的5匹配了，而`pattern`的开头是4，因此肯定不匹配；同理，一直到下标5都没有必要再回退回去，因为我们已经知道了它不可能与`pattern`匹配。但是如果盲目的不回退，就有可能错过本来能匹配到的字符串。

```python
txt = '1112'
ptn = '112'
# 这里txt的前2位字符能匹配上，在第3位匹配失败。
# 如果直接跳过前2位，就会错失从第2位开始的匹配，which最终会匹配成功。
```

那到底怎么样知道什么时候该回退什么时候可以安全地跳过呢，可以发现跟`pattern`自身的性质有关系，能回退的都是因为已匹配的字符串可以重新作为`pattern`中前部分重新匹配（'1112'中的'111'虽然匹配失败，但是后两个'11'可以重新匹配）。而已匹配成功的部分为`pattern`的前部分，因此如果`pattern`字符串具备这样的性质，就需要回退：$pattern[0:i] == pattern[j-i:j],\ i < j$。其中，`j`是本次匹配过程中的最后一位下标，也就是`pattern`中有一部分和自身的前缀字串匹配。那对于这样的情况，就说明已匹配的部分中存在一部分可能重新匹配能够匹配成功的可能性。

因此，每次匹配失败时，只需要查看当前匹配失败前的最后一位下标，是否满足上述的式子的性质。如果满足，那么就说明有必要回退重新匹配（但是不需要真的回退，因为已经知道后半部分匹配成功了，直接从匹配成功的后一位开始匹配）；否则对下一位字符重新开始匹配。这样我们的搜索下标就永远不会回退，在搜索阶段是`O(n)`的。

但是想要知道每个`j`满足不满足性质还需要计算量，或许我们可以使用一个数组$back[j] = max(i), where\ i<j\ and \ pattern[0:i]==pattern[j-i:j]$，记录了对于每个`j`是否需要回退，如果能回退，最多回退到多少。朴素的计算这个数组需要计算`M`个`j`的位置，对于每个`j`的位置，都有`M - j - 1`种可能的`i`的位置，因此最终的时间复杂度可能是`O(M^2)`的。

```cpp
// 这里back数组记录的是从下一个字符开始从哪个下标开始匹配
// 就'1231234'来说，back[4]是1，因为已经匹配成功了下标为0位置的'1'.
void find_back(char *ptn, int *back, const int M) {
    back[0] = 0;
    // j = 0时，只匹配成功了第一个字符，没有回退的必要，直接重新匹配，因此是0
    for (int j = 1; j < M; j++) {
        back[j] = 0;
        int len = 0;
        for (int i = 0; i < j; i++) {
            if (strcmp(ptn + j, ptn, i)) {
                back[j] = max(back[j], i);
            }
        }
        back[j] = max(back[j], len);
    }
}
bool strcmp(char *str1, char *str2, const int LEN) {
    for (int i = 0; i < LEN; i++) {
        if (str1[i] != str2[i]) {
            return false;
        }
    }
    return true;
}

// return the number of occurrences, which is the length of res array
int find_pattern(char *ptn, char *txt, int *back, int *res) {
    const int N = strlen(txt), M = strlen(ptn);
    for (int i = 0, j = 0; i < N; i++) {
        while (j && txt[i] != ptn[j]) j = back[max(0, j - 1)] + 1;
        if (txt[i] == ptn[j])	j++;
        if (j == M)	{
            cout << j - i + 1 << ' ';
            j = back[j - 1] + 1;
        }
    }
    cout << endl;
}
```

## 3. KMP算法

以上就是KMP算法的主要思路，两部分构成，首先求back数组，其次根据back数组进行优化后的匹配过程。但是单单一个back数组的求值过程就有`O(M^3)`的时间复杂度了，比之前还慢。我们需要对其进行一些优化。

依旧是观察back数组的性质，根据back数据的定义:

$$
back[j] = max(i),\ where\ i < j,\ pattern[0:i] == pattern[j-i, j].
$$

这里假设`back[j - 1]`存在，那么对于`j`来说，有两种可能：1. `pattern[j] == pattern[back[j - 1] + 1]`； 2. `pattern[j] != pattern[back[j - 1] + 1]`. 对于第一种情况而言，已知`pattern[0:i] == pattern[j-i-1 : j-1]`，而`back[j-1]`就是此处的`i`，那么也就是`pattern[j] == pattern[i + 1]`，那么进而可以推出`pattern[0:i+1] == pattern[j-i, j]`，此时要求的`back[j]=max(i)`就是`i+1`。

对于第二种，不相等的情况。我们就需要重新找到`back[j] = max(i), where pattern[0:i] == pattern[j - i:j]`。如果重新找又要浪费时间了，最好能够利用已有的信息。这里首先定义清楚名词，**最大前缀串**顾名思义，就是对于`j`的位置来说能够匹配到的最大前缀字符串，即`back[j]`的定义；**同字符最大前缀串**，即与`j`位置不相同，但是字符相同的最大前缀串`max(k), where pattern[k] == pattern[j], k < j, pattern[0:back[k]] == pattern[k - back[k], k]`。既然对于当前`j`的上一位来说的最大前缀串匹配失败了，那我们不妨匹配前一位即`j-1`的同字符最大前缀串。这样在上一位的最大前缀串匹配失败的情况下，如果同字符最大前缀串的后一位与当前位匹配成功，那么这对于当前位来说就是最大前缀串。问题在于如何找到同字符最大前缀串。

这里可以用反证法证明出对于`pattern[i]`的同字符最大前缀串就是`back[back[i]]`，具体证明可以参考李煜东大佬的算法竞赛进阶指南。因此对于`back[j]`的每个`j`而言，我们只需要不断比对`pattern[j - 1]`也就是前一个字符能匹配到的最大前缀串的后一位: `pattern[back[j-1] + 1`与当前位：`pattern[j]`。如果匹配失败，那么我们就寻找次大的前缀串：`pattern[back[back[j-1]]]`，直到比对到-1，说明对于当前位没有匹配的前缀串。

```cpp
void calc_back(const int M, char *ptn, int *back) {
    back[0] = -1;
    // -1 代表没有匹配的前缀串，只匹配成功第0位不存在前缀串
    for (int i = 1, j = back[0]; i < M; i++) {
        // i 代表back数组的下标，j代表上一个back数组的值
        while (j > -1 && back[i] != back[j + 1])	j = back[j];
        // 当j有退路且当前的i与当前的前一个字符的最大前缀串的后一位不匹配，回退寻找次大前缀串。
        if (ptn[i] == ptn[j + 1]) j++;
		back[i] = j;
    }
}
```

这个计算的过程主要取决于外部的for循环与内部的while循环，也就是这M次循环中，内部while训练的总循环次数。可以发现，while循环的次数取决于j的回退，而j的回退不会超过j的增加量，而j每次最多增加1。在这M次循环中，j最多增加M，因此j最多回退M。因此while循环的总循环次数不会超过j的总增加次数，即M。故最终的时间复杂度是`O(2M)=O(M)`的。

而求出了back数组，就可以进行`txt`与`ptn`的匹配过程了，这个过程与求`back`数组的过程类似。

```cpp
int ptn_search(const int M, const int N, char *ptn, char *txt, int *back) {
	int res = 0;
    for (int i = 0, j = back[0]; i < N; i++) {
        // i 代表当前txt匹配成功的下标，j 表示ptn匹配成功的下标
        while (j > -1 && txt[i] != ptn[j + 1])	j = back[j];
        // 如果当前位匹配失败，那么就利用back数组找到已匹配成功的位置继续比对，除非无路可退
        if (txt[i] == ptn[j + 1]) j++;
		if (j + 1 == M) {
            // 匹配成功
            cout << i - j << ' ';
            res++;
            j = back[j];
            // 需要考虑匹配可以从ptn数组的某一部分开始的情况
        }
    }
    return res;
}
```

搜索部分的时间复杂度与求back数组部分类似，都是外层for循环与内层的while循环控制。while循环与求back数组一样，`j`每次最多增长1，N次循环下最多增长N，因此`j`最多回退N次。因此时间复杂度为`O(2N)=O(N)`。整个KMP的时间复杂度为`O(N) + O(M) = O(N + M)`。
