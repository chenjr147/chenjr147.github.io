---
author: "kevin"
title: "LeetCode 第 245 场周赛"
date: 2021-06-15T11:52:42+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 重新分配字符使所有字符串都相等

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/redistribute-characters-to-make-all-strings-equal/](https://leetcode-cn.com/problems/redistribute-characters-to-make-all-strings-equal/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

字符串间的移动是任意的，那么我们只需要统计所有字符出现的次数，检查其是否为 $\texttt{words.size()}$ 的整数倍即可。

### 代码

```c++
class Solution {
public:
    bool makeEqual(vector<string>& words) {
        // Algorithm: transform and conquer
        // Time Complexity: O(n), n = sum(len(words[i]))
        // Space Complexity: O(1)
        array<int, 26> cnt{};
        for (auto&& word : words) {
            for (auto&& ch : word) {
                cnt[ch - 'a'] += 1;
            }
        }
        int n = static_cast<int>(words.size());
        for (auto&& c : cnt) {
            if (c % n != 0) {
                return false;
            }
        }
        return true;
    }
};
```



## Q2. 可移除字符的最大数目

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/maximum-number-of-removable-characters/](https://leetcode-cn.com/problems/maximum-number-of-removable-characters/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

很容易想到暴力的做法，即直接使用双指针进行模拟，时间复杂度是 $\texttt{O((s.size() + p.size()) * removable.size())}$，其计算量级会达到 $10^{10}$，显然无法通过本题。

观察到对于 $\texttt{removable}$ 的选择具有单调性，当我们移除前 $\texttt{x(x < k)}$ 个字符时，$\texttt{p}$ 一定仍然是 $\texttt{s}$ 的一个子序列；当我们移除前 $\texttt{y(y > k)}$ 个字符时，$\texttt{p}$ 一定不是 $\texttt{s}$ 的一个子序列。

那么我们可以使用二分的方法，使得时间复杂度降为 $\texttt{O((s.size() + p.size()) * log removable.size())}$，可以通过本题。

### 代码

```c++
class Solution {
public:
    int maximumRemovals(string s, string p, vector<int>& removable) {
        // Algorithm: binary search + two pointer
        // Time Complexity: O((n + m) * log r), n = len(s), m = len(p), r = len(removable)
        // Space Complexity: O(n)
        int n = static_cast<int>(s.size()), m = static_cast<int>(p.size());
        auto check = [&](int k) {
            vector<bool> removed(n);
            for (int i = 0; i < k; ++i) {
                removed[removable[i]] = true;
            }
            int i = 0, j = 0;
            while (i < n and j < m) {
                if (not removed[i] and s[i] == p[j]) {
                    j += 1;
                }
                i += 1;
            }
            return j == m;
        };
        int low = 0, high = static_cast<int>(removable.size());
        while (low < high) {
            int mid = (low + high + 1) / 2;
            if (check(mid)) {
                low = mid;
            } else {
                high = mid - 1;
            }
        }
        return low;
    }
};
```


## Q3. 合并若干三元组以形成目标三元组

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/merge-triplets-to-form-target-triplet/](https://leetcode-cn.com/problems/merge-triplets-to-form-target-triplet/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

观察到 $triplets[i] = [a_i, b_i, c_i]$ 中若 $a_i > x$ 或 $b_i > y$ 或 $c_i > z$，那么 $triplets[i]$ 将是无效的，也即我们不需要使用到 $triplets[i]$ 以得到最终的结果。

而在 $\texttt{triplets}$ 剩余的三元组中，我们只需要找到三个位置的最大值，检查他们是否与 $\texttt{target}$ 一一对应即可。

### 代码

```python
class Solution:
    def mergeTriplets(self, triplets: List[List[int]], target: List[int]) -> bool:
        # Algorithm: transform and conquer
        # Time Complexity: O(n), n = len(triplets)
        # Space Complexity: O(1)
        x, y, z = target
        u = v = w = -inf
        for a, b, c in triplets:
            if a <= x and b <= y and c <= z:
                u = max(u, a)
                v = max(v, b)
                w = max(w, c)
        return (u, v, w) == (x, y, z)
```


## Q4. 最佳运动员的比拼回合

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/the-earliest-and-latest-rounds-where-players-compete/](https://leetcode-cn.com/problems/the-earliest-and-latest-rounds-where-players-compete/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

> 参考 [@吴自华](https://cp-wiki.vercel.app/tutorial/leetcode/WC245/#problem-d-%E6%9C%80%E4%BD%B3%E8%BF%90%E5%8A%A8%E5%91%98%E7%9A%84%E6%AF%94%E6%8B%BC%E5%9B%9E%E5%90%88)

观察到数据量很小，考虑状压DP，枚举每场比赛的胜负，同时用一个 $\texttt{pair}$ 标记当前两个运动员的位置作为状态。

但是直接进行状压DP将会是 $2^{28}$ 的计算量级，显然不符合要求。

观察到第一轮之后就只会剩下一半（$28 / 2 = 14$）的运动员，那么我们状压DP的计算量级只会达到 $2^{14} < 2 * 10^4$。

到第二轮时，两个运动员可能的位置只有（$ 14 * 13 = 182$）种情况，此时状压DP的计算量级为 $182 * 2^7 < 3 * 10^4$。

到第三轮，此时仅有（$14 / 2 = 7$）名运动员。两个运动员可能的位置只有（$ 7 * 6 = 42$）种情况，此时状压DP的计算量级为 $42 * 2^3 < 1 * 10^3$。

到第四轮，此时仅有（$7 / 2 = 4$）名运动员。两个运动员可能的位置只有（$ 4 * 3 = 12$）种情况，此时状压DP的计算量级为 $12 * 2^2 < 1 * 10^2$。

到第五轮，此时仅有（$4 / 2 = 2$）名运动员。两个运动员可能的位置只有两种情况，此时状压DP的计算量级为 $2 * 2^1 < 1 * 10^1$。

由此可见，暴力枚举的计算量级在 $10^5$ 级别，可以通过本题。

### 代码

```c++
class Solution {
public:
    vector<int> earliestAndLatest(int n, int firstPlayer, int secondPlayer) {
        // Algorithm: dynamic programming
        // Time Complexity: O(n * 2^(n / 2))
        // Space Complexity: O(n^2)
        set<pair<int, int>> states{{firstPlayer, secondPlayer}};
        int earliest = numeric_limits<int>::max(), latest = 0, round = 1;
        while (not states.empty()) {
            set<pair<int, int>> newStates;
            for (auto&& [first, second] : states) {
                if (first + second == n + 1) {
                    earliest = min(earliest, round);
                    latest = max(latest, round);
                } else {
                    int player = n / 2;
                    for (int mask = 0; mask < (1 << player); ++mask) {
                        set<int> winner;
                        for (int i = 0; i < player; ++i) {
                            if ((mask >> i) & 1) {
                                winner.emplace(i + 1);
                            } else {
                                winner.emplace(n - i);
                            }
                        }
                        if (n % 2 == 1) {
                            winner.emplace((n + 1) / 2);
                        }
                        if (winner.find(first) != winner.end() and winner.find(second) != winner.end()) {
                            int firstPlace = -1, secondPlace = -1, i = 1;
                            auto it = winner.begin();
                            while (firstPlace == -1 or secondPlace == -1) {
                                if (*it == first) {
                                    firstPlace = i;
                                } else if (*it == second) {
                                    secondPlace = i;
                                }
                                ++i;
                                ++it;
                            }
                            newStates.emplace(firstPlace, secondPlace);
                        }
                    }
                }
            }
            states = move(newStates);
            n = (n + 1) / 2;
            round += 1;
        }
        return {earliest, latest};
    }
};
```

### 注意

运动员的编号不要弄错了
