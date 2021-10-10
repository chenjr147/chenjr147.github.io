---
author: "kevin"
title: "LeetCode 第 246 场周赛"
date: 2021-06-20T13:52:42+08:00
categories:
- 周赛
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 字符串中的最大奇数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/largest-odd-number-in-string/](https://leetcode-cn.com/problems/largest-odd-number-in-string/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

从后往前找到第一个奇数即可。

### 代码

```python
class Solution:
    def largestOddNumber(self, num: str) -> str:
        # Algorithm: tranform and conquer
        # Time Complexity: O(n), n = len(num)
        # Space Complexity: O(1)
        for i in range(len(num) - 1, -1, -1):
            if int(num[i]) % 2 == 1:
                return num[:i + 1]
        return ""
```

## Q2. 你完成的完整对局数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/the-number-of-full-rounds-you-have-played/](https://leetcode-cn.com/problems/the-number-of-full-rounds-you-have-played/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟即可。

### 代码

```python
class Solution:
    # Algorithm: simulation
    # Time Complexity: O(1)
    # Space Complexity: O(1)
    def isOver(self, startHour: int, startMinute: int, finishHour: int, finishMinute: int) -> int:
        return (startHour * 60 + startMinute) > (finishHour * 60 + finishMinute)

    def numberOfRounds(self, startTime: str, finishTime: str) -> int:
        startHour, startMinute = map(int, startTime.split(':'))
        finishHour, finishMinute = map(int, finishTime.split(':'))
        if self.isOver(startHour, startMinute, finishHour, finishMinute):
            finishHour += 24
        if startMinute % 15 != 0:
            if startMinute < 15:
                startMinute = 15
            elif startMinute < 30:
                startMinute = 30
            elif startMinute < 45:
                startMinute = 45
            else:
                startHour += 1
                startMinute = 0
        ans = 0
        while True:
            startMinute += 15
            if startMinute >= 60:
                startHour += 1
                startMinute = 0
            if self.isOver(startHour, startMinute, finishHour, finishMinute):
                break
            ans += 1
        return ans
```


## Q3. 统计子岛屿

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/count-sub-islands/](https://leetcode-cn.com/problems/count-sub-islands/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

用深度优先搜索可以找出 `grid2` 中所有的连通块。

搜索的同时检查 `grid2` 中的连通块在 `grid1` 中是否被完全包含即可。

### 代码

```c++
class Solution {
public:
    int countSubIslands(vector<vector<int>>& grid1, vector<vector<int>>& grid2) {
        // Algorithm: dfs
        // Time Complexity: O(n * m), n = grid2.size(), m = grid2[0].size()
        // Space Complexity: O(1)
        int row = grid1.size(), col = grid1[0].size();
        constexpr array<pair<int, int>, 4> dir{{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}};
        auto check = [&](int x, int y) {
            return x >= 0 and x < row and y >= 0 and y < col and grid2[x][y] == 1;
        };
        function<bool(int, int)> dfs = [&](int x, int y) {
            grid2[x][y] = 0;
            bool valid = grid1[x][y] == 1;
            for (auto&& [dx, dy] : dir) {
                if (check(x + dx, y + dy)) {
                    valid &= dfs(x + dx, y + dy);
                }
            }
            return valid;
        };
        int ans = 0;
        for (int x = 0; x < row; ++x) {
            for (int y = 0; y < col; ++y) {
                if (check(x, y) and dfs(x, y)) {
                    ans += 1;
                }
            }
        }
        return ans;
    }
};
```


## Q4. 查询差绝对值的最小值

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-absolute-difference-queries/](https://leetcode-cn.com/problems/minimum-absolute-difference-queries/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

前缀和 + 计数排序。


### 代码

```python
class Solution:
    def minDifference(self, nums: List[int], queries: List[List[int]]) -> List[int]:
        # Algorithm: prefix sum + counting sort
        # Time Complexity: O((n + q) * m), n = len(nums), q = len(queries), m = max(nums)
        # Space Complexity: O(n * m)
        prefix = [[0] * 101 for _ in range(len(nums) + 1)]
        for i in range(len(nums)):
            for j in range(1, 101):
                prefix[i + 1][j] = prefix[i][j]
            prefix[i + 1][nums[i]] += 1
        ans = list()
        for left, right in queries:
            cur = list()
            for i in range(1, 101):
                if prefix[right + 1][i] > prefix[left][i]:
                    cur.append(i)
            if len(cur) == 1:
                ans.append(-1)
            else:
                minimum = inf
                for i in range(1, len(cur)):
                    minimum = min(minimum, cur[i] - cur[i - 1])
                ans.append(minimum)
        return ans
```

### 注意

审题，注意数据范围！

