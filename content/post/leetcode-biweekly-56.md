---
author: "kevin"
title: "LeetCode 第 56 场双周赛"
date: 2021-07-16T13:52:42+08:00
categories:
- 周赛
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 统计平方和三元组的数目

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/count-square-sum-triples/](https://leetcode-cn.com/problems/count-square-sum-triples/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

第一题无脑暴力 $O(n^3)$ 即可。

赛后补一份容易想到的 $O(n^2)$ 的双指针代码。评论区给出了复杂度更低的算法，但需要前置的数学知识，并不太容易在面试中想出来，在这里也就不再赘述了。

### 代码

```python3
# Algorithm: two pointers
# Time Complexity: O(n^2)
# Space Complexity: O(1)
class Solution:
    def countTriples(self, n: int) -> int:
        ans = 0
        for c in range(1, n + 1):
            target = c * c
            a, b = 1, c - 1
            while a <= b:
                result = a * a + b * b
                if result == target:
                    ans += 1 if a == b else 2
                    a += 1
                    b -= 1
                elif result < target:
                    a += 1
                else:
                    b -= 1
        return ans
```



## Q2. 迷宫中离入口最近的出口

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/nearest-exit-from-entrance-in-maze/](https://leetcode-cn.com/problems/nearest-exit-from-entrance-in-maze/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

一道比较无脑的搜索题。

### 代码

```c++
// Alogrithm: bfs
// Time Complexity: O(n * m), n = maze.size(), m = maze[0].size()
// Space Complexity: O(n * m)
class Solution {
public:
    int nearestExit(vector<vector<char>> &maze, vector<int> &entrance) {
        int row = maze.size(), col = maze[0].size();
        // in order to use `set` to find coordinate (x, y) that we have processed before,
        // we transform it into an unique value
        auto position = [&col](int x, int y) { return col * x + y; };
        unordered_set<int> seen;
        seen.emplace(position(entrance[0], entrance[1]));
        queue<pair<int, int>> q;
        q.emplace(entrance[0], entrance[1]);
        constexpr array<pair<int, int>, 4> dir{{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}};
        int step = 0;
        while (not q.empty()) {
            for (auto _ = q.size(); _ > 0; --_) {
                auto [x, y] = q.front();
                q.pop();
                for (auto &&[dx, dy] : dir) {
                    int nx = x + dx, ny = y + dy;
                    if (nx >= 0 and nx < row and ny >= 0 and ny < col and
                        maze[nx][ny] == '.' and seen.find(position(nx, ny)) == seen.end()) {
                        seen.emplace(position(nx, ny));
                        if (nx == 0 or nx == row - 1 or ny == 0 or ny == col - 1) {
                            return step + 1;
                        }
                        q.emplace(nx, ny);
                    }
                }
            }
            step += 1;
        }
        return -1;
    }
};
```


## Q3. 求和游戏

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/sum-game/](https://leetcode-cn.com/problems/sum-game/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

分类讨论的博弈论问题。

理清楚之后翻译成代码即可。

<div style="text-align:center"><img src="Q3.svg" /></div>

### 代码
```python3
# Algorithm: category discussion
# Time Complexity: O(n), n = len(num)
# Space Complexity: O(1)
class Solution:
    def sumGame(self, num: str) -> bool:
        # number of '?'
        leftQuestion = rightQuestion = 0
        # sum
        leftSum = rightSum = 0
        for idx, char in num:
            if char == '?':
                if idx < len(num) // 2:
                    leftQuestion += 1
                else:
                    rightQuestion += 1
            else:
                if idx < len(num) // 2:
                    leftSum += int(char)
                else:
                    rightSum += int(char)
        
        # discussion
        if (leftQuestion + rightQuestion) % 2 == 1:
            return True
        if leftQuestion == rightQuestion and leftSum == rightSum:
            return False
        if (leftQuestion > rightQuestion and leftSum < rightSum) or (
                leftQuestion < rightQuestion and leftSum > rightSum):
            if abs(leftQuestion - rightQuestion) // 2 * 9 == abs(leftSum - rightSum):
                return False
        return True

```


## Q4. 规定时间内到达终点的最小花费

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-cost-to-reach-destination-in-time/](https://leetcode-cn.com/problems/minimum-cost-to-reach-destination-in-time/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

二维的最短路径问题，观察到数据范围 $1 \le maxTime \le 1000$ 以及 $2 \le n \le 1000$，可以想到使用动态规划进行求解。

我们定义 $dp[t][x]$ 为在时刻 $t$ 到达城市 $x$ 的最小花费。那么对于城市 $x$ 的邻居 $y$，假设二者之间的耗时为 $time$，我们有以下状态转移方程： 

$$
dp[t + time][y] = \min (dp[t + time][y], dp[t][x] + passingFees[y])
$$

注意到 $n - 1 \le edges.length \le 1000$，故总体的计算量只会到 $10^6$ 量级，可以通过本题。

### 代码

```python3
# Algorithm: dynamic programming
# Time Complexity: O(maxTime * m), m = edges.length
# Space Complexity: O(maxTime * n)
class Solution:
    def minCost(self, maxTime: int, edges: List[List[int]],
                passingFees: List[int]) -> int:
        # process edges
        adj = defaultdict(lambda: defaultdict(lambda: inf))
        for x, y, time in edges:
            adj[x][y] = adj[y][x] = min(adj[x][y], time)
        
        n = len(passingFees)

        # dp[t][x]: at time t, arrive at city x, minimum cost
        dp = [[inf] * n for _ in range(maxTime + 1)]
        dp[0][0] = passingFees[0]
        for t in range(maxTime + 1):
            for x in range(n):
                if dp[t][x] != inf:
                    for y, time in adj[x].items():
                        if t + time <= maxTime:
                            dp[t + time][y] = min(dp[t + time][y],
                                                  dp[t][x] + passingFees[y])
        ans = min([dp[t][n - 1] for t in range(maxTime + 1)])
        return ans if ans != inf else -1

```