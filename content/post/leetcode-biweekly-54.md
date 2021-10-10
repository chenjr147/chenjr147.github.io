---
author: "kevin"
title: "LeetCode 第 54 场双周赛"
date: 2021-06-13T23:52:42+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
---

## Q1. 检查是否区域内所有整数都被覆盖

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/check-if-all-the-integers-in-a-range-are-covered/](https://leetcode-cn.com/problems/check-if-all-the-integers-in-a-range-are-covered/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟

### 代码

```python
class Solution:
    def isCovered(self, ranges: List[List[int]], left: int, right: int) -> bool:
        # Algorithm: simulation
        # Time Complexity: O(n * m), n = right - left + 1, m = len(ranges)
        # Space Complexity: O(1)
        for cur in range(left, right + 1):
            ok = False
            for begin, end in ranges:
                if begin <= cur <= end:
                    ok = True
                    break
            if not ok:
                return False
        return True
```



## Q2. 找到需要补充粉笔的学生编号

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/find-the-student-that-will-replace-the-chalk/](https://leetcode-cn.com/problems/find-the-student-that-will-replace-the-chalk/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟可以发现，重复的循环其实可以通过取模来简化。

### 代码

```python
class Solution:
    def chalkReplacer(self, chalk: List[int], k: int) -> int:
        # Algorithm: simulation
        # Time Complexity: O(n), n = len(chalk)
        # Space Complexity: O(1)
        k %= sum(chalk)
        for i, c in enumerate(chalk):
            k -= c
            if k < 0:
                return i
        return 0
```


## Q3. 最大的幻方

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/largest-magic-square/](https://leetcode-cn.com/problems/largest-magic-square/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

前缀和。

### 代码

```c++
class Solution {
public:
    int largestMagicSquare(vector<vector<int>>& grid) {
        // Algorithm: prefix sum
        // Time Complexity: O(n * m * p * q), n = grid.size(), m = grid[0].size(), p = min(n, m), q = max(n, m)
        // Space Complexity: O(n * m)
        int row = static_cast<int>(grid.size()), col = static_cast<int>(grid[0].size());
        vector<vector<int>> rowSum(row, vector<int>(col + 1)), colSum(row + 1, vector<int>(col));
        for (int x = 0; x < row; ++x) {
            for (int y = 0; y < col; ++y) {
                rowSum[x][y + 1] = rowSum[x][y] + grid[x][y];
                colSum[x + 1][y] = colSum[x][y] + grid[x][y];
            }
        }
        for (int i = min(row, col); i >= 2; --i) {
            for (int x = 0; x + i <= row; ++x) {
                for (int y = 0; y + i <= col; ++y) {
                    // [x, y] => [x + i - 1, y + i - 1]
                    int diagSum = 0, subSum = 0;
                    for (int t = 0; t < i; ++t) {
                        diagSum += grid[x + t][y + t];
                        subSum += grid[x + t][y + i - 1 - t];
                    }
                    if (diagSum != subSum) {
                        continue;
                    }
                    bool ok = true;
                    for (int t = x; t < x + i and ok; ++t) {
                        if (rowSum[t][y + i] - rowSum[t][y] != diagSum) {
                            ok = false;
                        }
                    }
                    for (int t = y; t < y + i and ok; ++t) {
                        if (colSum[x + i][t] - colSum[x][t] != diagSum) {
                            ok = false;
                        }
                    }
                    if (ok) {
                        return i;
                    }
                }
            }
        }
        return 1;
    }
};
```


## Q4. 反转表达式值的最少操作次数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-cost-to-change-the-final-value-of-expression/](https://leetcode-cn.com/problems/minimum-cost-to-change-the-final-value-of-expression/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

> 参考 [@吴自华](https://cp-wiki.vercel.app/tutorial/leetcode/BC54/#problem-d-%E5%8F%8D%E8%BD%AC%E8%A1%A8%E8%BE%BE%E5%BC%8F%E5%80%BC%E7%9A%84%E6%9C%80%E5%B0%91%E6%93%8D%E4%BD%9C%E6%AC%A1%E6%95%B0)

栈 + 动态规划

### 代码

```c++
class Solution {
public:
    int minOperationsToFlip(string expression) {
        // Algorithm: stack + dynamic programming
        // Time Complexity: O(n), n = expression.size()
        // Space Complexity: O(n), n = expression.size()
        stack<pair<int, int>> states;
        stack<char> ops;
        // possible ch : '()10|&'
        for (auto&& ch : expression) {
            if (ch == '0' or ch == '1' or ch == ')') {
                if (ch == ')') {
                    ops.pop();
                } else {
                    states.emplace(ch - '0', '1' - ch);
                }
                if (not ops.empty() and (ops.top() == '|' or ops.top() == '&')) {
                    auto op = ops.top();
                    ops.pop();
                    auto [right0, right1] = states.top();
                    states.pop();
                    auto [left0, left1] = states.top();
                    states.pop();
                    int ans0 = 0, ans1 = 0;
                    if (op == '|') {
                        states.emplace(min(left0 + right0, min(left0, right0) + 1), min(left1, right1));
                    } else {
                        states.emplace(min(left0, right0), min(left1 + right1, min(left1, right1) + 1));
                    }
                }
            } else {
                ops.emplace(ch);
            }
        }
        auto [ans0, ans1] = states.top();
        return ans0 == 0 ? ans1 : ans0;
    }
};
```
