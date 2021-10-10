---
author: "kevin"
title: "LeetCode 第 247 场周赛"
date: 2021-07-01T13:52:42+08:00
categories:
- 周赛
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 两个数对之间的最大乘积差

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/maximum-product-difference-between-two-pairs/](https://leetcode-cn.com/problems/maximum-product-difference-between-two-pairs/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

注意到数据范围 `1 <= nums[i] <= 10^4`，可以想到使用排序解决本问题。

### 代码

```python
# Algorithm: sort + greed
# Time Complexity: O(n), n = len(nums)
# Space Complexity: O(1)
class Solution:
    def maxProductDifference(self, nums: List[int]) -> int:
        nums.sort()
        return nums[-1] * nums[-2] - nums[0] * nums[1]
```



## Q2. 循环轮转矩阵

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/cyclically-rotating-a-grid/](https://leetcode-cn.com/problems/cyclically-rotating-a-grid/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟即可，注意细节。

### 代码

```python
# Algorithm: simulation
# Time Complexity: O(n * m), n = len(grid), m = len(grid[0])
# Space Complexity: O(n + m)
class Solution:
    def rotateGrid(self, grid: List[List[int]], k: int) -> List[List[int]]:
        n, m = len(grid), len(grid[0])
        for i in range(ceil(min(n, m) / 2)):
            nums = list()
            x = y = i
            # x, y = i, i
            while y < m - i - 1:
                nums.append((x, y, grid[x][y]))
                y += 1
            # x, y = i, m - i - 1
            while x < n - i - 1:
                nums.append((x, y, grid[x][y]))
                x += 1
            # x, y = n - i - 1, m - i - 1
            while y > i:
                nums.append((x, y, grid[x][y]))
                y -= 1
            # x, y = n - i - 1, i
            while x > i:
                nums.append((x, y, grid[x][y]))
                x -= 1
            # x, y = i, i
            for idx, (x, y, _) in enumerate(nums):
                _, _, val = nums[(idx + k) % len(nums)]
                grid[x][y] = val
        return grid
```


## Q3. 最美子字符串的数目

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/number-of-wonderful-substrings/](https://leetcode-cn.com/problems/number-of-wonderful-substrings/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

子字符串问题往前缀和方向思考；子序列问题往动态规划方向思考。

显然本题中只需要记录前缀字符串的奇偶情况即可，由于数据量很小，考虑使用状态压缩进行优化。

### 代码
```c++
// Algorithm: prefix cnt + state compression
// Time Complexity: O(n), n = len(word)
// Space Complexity: O(2^m), m = \Sigma
class Solution {
public:
    long long wonderfulSubstrings(string word) {
        long long ans = 0;
        int state = 0;
        array<int, 1 << 10> cnt{};
        cnt[0] += 1;
        for (auto &&ch : word) {
            state ^= 1 << (ch - 'a');
            // no letter appears an odd number of times
            ans += cnt[state];
            // at most one letter appears an odd number of times
            for (int i = 0; i < 10; ++i) {
                ans += cnt[state ^ (1 << i)];
            }
            cnt[state] += 1;
        }
        return ans;
    }
};
```


## Q4. 统计为蚁群构筑房间的不同顺序

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/count-ways-to-build-rooms-in-an-ant-colony/](https://leetcode-cn.com/problems/count-ways-to-build-rooms-in-an-ant-colony/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

<div style="text-align:center"><img src="test.svg" /></div>

如上图所示，假设我们已经得到了所有子节点的 $dp[child]$ 以及 $sz[child]$，那么我们可以得出以下计算公式：

$$
dp[root] = \left ( \prod_{i = 0}^{childCnt} dp[child_i] \right ) \times \frac{(sz[root] - 1)!}{\left( \prod_{i = 0}^{childCnt} sz[child_i]! \right)}
$$

其中乘号左边的部分是各子节点的方案数，是乘法关系。乘号右边是将各子节点归并排列的组合方式，其中分子即子节点的全排列方式，由于每个子树有且仅有一种排列方式，所以还需要除去分母部分。

由于除法不满足模运算的分配律，所以除去分母部分需要用到乘法逆元。

### 代码

```c++
// Algorithm: tree type dynamic programming
// Time Complexity: O(n), n = prevRoom.size()
// Space Complexity: O(n)
class Solution {
private:
    static constexpr int mod = 1'000'000'007;

public:
    int waysToBuildRooms(vector<int> &prevRoom) {
        int n = prevRoom.size();

        // factorial
        vector<int64_t> factorial(n, 1);
        for (int i = 2; i < n; ++i) {
            factorial[i] = (factorial[i - 1] * i) % mod;
        }

        // (x ^ y) % mod
        auto qpow = [&](int64_t x, int y) {
            int64_t ans = 1;
            while (y) {
                if (y & 1) {
                    ans = (ans * x) % mod;
                }
                x = (x * x) % mod;
                y >>= 1;
            }
            return ans;
        };

        // construct map
        vector<vector<int>> children(n);
        for (int i = 1; i < n; i++) {
            children[prevRoom[i]].emplace_back(i);
        }

        // dfs
        vector<int64_t> dp(n, 1), sz(n, 1);
        function<void(int)> dfs = [&](int root) {
            for (auto &&child : children[root]) {
                dfs(child);
                sz[root] += sz[child];
                dp[root] = (dp[root] * dp[child]) % mod;
                dp[root] = (dp[root] * qpow(factorial[sz[child]], mod - 2)) % mod;
            }
            dp[root] = (dp[root] * factorial[sz[root] - 1]) % mod;
        };
        dfs(0);

        return dp[0];
    }
};
```