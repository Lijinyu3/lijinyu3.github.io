# 0x13 KMP

[toc]

---

```cpp
// s[]是长文本，p[]是模式串，n是s的长度，m是p的长度
// 求模式串的Next数组：
for (int i = 2, j = 0; i <= m; i ++ )
{
    while (j && p[i] != p[j + 1]) j = ne[j];
    if (p[i] == p[j + 1]) j ++ ;
    ne[i] = j;
}

// 匹配
for (int i = 1, j = 0; i <= n; i ++ )
{
    while (j && s[i] != p[j + 1]) j = ne[j];
    if (s[i] == p[j + 1]) j ++ ;
    if (j == m)
    {
        j = ne[j];
        // 匹配成功后的逻辑
    }
}
```

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

那到底怎么样知道什么时候该回退什么时候可以安全地跳过呢，可以发现跟`pattern`自身的性质有关系，能回退的都是因为已匹配的字符串可以重新作为`pattern`中前部分重新匹配（'1112'中的'111'虽然匹配失败，但是后两个'11'可以重新匹配）。而已匹配成功的部分为`pattern`的前部分，因此如果`pattern`字符串具备这样的性质，就需要回退：$pattern[0:i] == pattern[j-i:j],\ j <i$。其中，`j`是本次匹配过程中的最后一位下标，也就是`pattern`中有一部分和自身的前缀字串匹配。那对于这样的情况，就说明已匹配的部分中存在一部分可能重新匹配能够匹配成功的可能性。

因此，每次匹配失败时，只需要查看当前匹配失败前的最后一位下标，是否满足上述的式子的性质。如果满足，那么就说明有必要回退重新匹配（但是不需要真的回退，因为已经知道后半部分匹配成功了，直接从匹配成功的后一位开始匹配）；否则对下一位字符重新开始匹配。这样我们的搜索下标就永远不会回退，在搜索阶段是`O(n)`的。

但是想要知道每个`j`满足不满足性质还需要计算量，或许我们可以使用一个数组$$back[j] = max(i), where\ j<i\ and \ pattern[0:i]==pattern[j-i:j]$$，记录了对于每个`j`是否需要回退，如果能回退，最多回退到多少。朴素的计算这个数组需要计算`M`个`j`的位置，对于每个`j`的位置，都有`M - j - 1`种可能的`i`的位置，因此最终的时间复杂度可能是`O(M^2)`的。

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
        while (j && txt[i] != ptn[j]) j = back[max(0, j - 1)];
        if (txt[i] == ptn[j])	j++;
        if (j == M)	cout << j - i + 1 << ' ';
    }
    cout << endl;
}
```

