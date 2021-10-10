---
author: "kevin"
title: "LeetCode 第 248 场周赛"
date: 2021-07-04T13:52:42+08:00
categories:
- 周赛
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

起晚了没赶上:(

## Q1. 基于排列构建数组

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/build-array-from-permutation/](https://leetcode-cn.com/problems/build-array-from-permutation/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

阅读理解。

### 代码

```c++
// Algorithm: simulation
// Time Complexity: O(n), n = nums.size()
// Space Complexity: O(n)
class Solution {
public:
    vector<int> buildArray(vector<int> &nums) {
        int n = nums.size();
        vector<int> ans(n);
        for (int i = 0; i < n; ++i) {
            ans[i] = nums[nums[i]];
        }
        return ans;
    }
};
```



## Q2. 消灭怪物的最大数量(https://leetcode-cn.com/problems/eliminate-maximum-number-of-monsters/)

> 来源：力扣（LeetCode）
>
> 链接：[]
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

在 $t_0(t_0 \in \mathbb{Z})$ 时刻，我们可以消灭到达时间为 $(t_0, \infty)$ 的怪物。

只需要将怪物的到达时间向上取整并排序，贪心消灭即可。

### 代码

```c++
// Algorithm: sort + greed
// Time Complexity: O(n), n = dist.size()
// Space Complexity: O(n)
class Solution {
public:
    int eliminateMaximum(vector<int> &dist, vector<int> &speed) {
        int n = dist.size();
        vector<double> time(n);
        for (int i = 0; i < n; ++i) {
            time[i] = ceil(1. * dist[i] / speed[i]);
        }
        sort(time.begin(), time.end());
        for (int i = 0; i < n; ++i) {
            if (time[i] <= i) {
                return i;
            }
        }
        return n;
    }
};
```


## Q3. 统计好数字的数目(https://leetcode-cn.com/problems/count-good-numbers/)

> 来源：力扣（LeetCode）
>
> 链接：[]
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

奇数位置有 `2, 3, 5, 7` 四种选择，偶数位置有 `0, 2, 4, 6, 8` 五种选择。

根据排列组合原理可知这是乘法关系。

题目中说明可以包括前导零，所以好数字的数目即

$$
 ans = 4^{\lfloor \dfrac{n}{2} \rfloor} * 5 ^{\lceil \dfrac{n}{2} \rceil}
$$

只需要简单地进行快速幂处理并取模即可。

### 代码

```c++
// Algorithm: math + quick pow
// Time Complexity: O(\log n)
// Space Complexity: O(1)
class Solution {
private:
    static constexpr int mod = 1'000'000'007;

public:
    int countGoodNumbers(long long n) {
        // quick pow
        auto qpow = [&](int64_t x, int64_t y) {
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

        // odd indices count and even indices count
        int64_t odd = floor(n / 2.0), even = ceil(n / 2.0);
        int64_t ans = (qpow(4, odd) * qpow(5, even)) % mod;
        return ans;
    }
};
```


## Q4. 最长公共子路径(https://leetcode-cn.com/problems/longest-common-subpath/)

> 来源：力扣（LeetCode）
>
> 链接：[]
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

滚动哈希，二分查找。但是可能会有碰撞，导致答案错误。

更好的解法是后缀数组(TODO)。


### 代码

```c++
// Algorithm: rolling hash + binary search
// Time Complexity: O(n \log n)
// Space Complexity: O(n)
class Solution {
public:
    int longestCommonSubpath(int n, vector<vector<int>> &paths) {
        uint64_t mod = numeric_limits<uint64_t>::max() / n / 10;

        // check if there is common subpath which length is `len`
        auto check = [&](int len) {
            unordered_set<uint64_t> seen;
            uint64_t base = 1;
            for (int _ = 1; _ < len; ++_) {
                base = (base * n) % mod;
            }
            for (int i = 0; i < static_cast<int>(paths.size()); ++i) {
                unordered_set<uint64_t> now;
                uint64_t curr = 0;
                for (int j = 0; j < static_cast<int>(paths[i].size()); ++j) {
                    curr = (curr * n + paths[i][j]) % mod;
                    if (j >= len - 1) {
                        if (i == 0 or seen.find(curr) != seen.end()) {
                            now.emplace(curr);
                        }
                        curr = (curr - (paths[i][j - len + 1] * base) % mod + mod) % mod;
                    }
                }
                if (now.empty()) {
                    return false;
                }
                seen = move(now);
            }
            return true;
        };

        // binary search
        int low = 0, high = numeric_limits<int>::max();
        for (auto &&path : paths) {
            high = min(high, static_cast<int>(path.size()));
        }
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
