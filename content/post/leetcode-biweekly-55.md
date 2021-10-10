---
author: "kevin"
title: "LeetCode 第 55 场双周赛"
date: 2021-06-27T13:52:42+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 删除一个元素使数组严格递增

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/remove-one-element-to-make-the-array-strictly-increasing/](https://leetcode-cn.com/problems/remove-one-element-to-make-the-array-strictly-increasing/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

观察并分析，可以发现仅存在两种合法情况，逐一考虑即可。

### 代码

```c++
// Algorithm: two pointer
// Time Complexity: O(n), n = nums.size()
// Space Complexity: O(1)
class Solution {
public:
    bool canBeIncreasing(vector<int> &nums) {
        int n = nums.size();
        
        // if we remove nums[x], whether array is increasing
        auto check = [&](int x) {
            int last = 0;
            for (int i = 0; i < n; ++i) {
                if (i != x) {
                    if (nums[i] <= last) {
                        return false;
                    }
                    last = nums[i];
                }
            }
            return true;
        };

        // find invalid index
        for (int i = 1; i < n; ++i) {
            if (nums[i - 1] >= nums[i]) {
                return check(i - 1) or check(i);
            }
        }
        return true;
    }
};
```



## Q2. 删除一个字符串中所有出现的给定子字符串

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/remove-all-occurrences-of-a-substring/](https://leetcode-cn.com/problems/remove-all-occurrences-of-a-substring/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

数据量较小，暴力即可。

(TODO: KMP)

### 代码

```c++
// Algorithm: brute-force
// Time Complexity: O(n^2), n = s.size()
// Space Complexity: O(n)
class Solution {
public:
    string removeOccurrences(string s, string part) {
        string ans;
        for (auto &&c : s) {
            ans.push_back(c);
            if (ans.size() >= part.size() and ans.substr(ans.size() - part.size()) == part) {
                ans.erase(ans.size() - part.size());
            }
        }
        return ans;
    }
};
```


## Q3. 最大子序列交替和

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/maximum-alternating-subsequence-sum/](https://leetcode-cn.com/problems/maximum-alternating-subsequence-sum/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

子序列问题可以考虑用动态规划来进行求解。

考虑前 `i - 1` 个数字，维护两个值：

* `odd` : 长度为奇数时的最大子序列交替和
* `even` : 长度为偶数时的最大子序列交替和

那么对第 `i` 个数字做决策时，可以得到前 `i` 个数字对应两个值。

最终返回 `max(odd, even)` 即可。

### 代码

```python
# Algorithm: dynamic programming
# Time Complexity: O(n), n = len(nums)
# Space Complexity: O(1)
class Solution:
    def maxAlternatingSum(self, nums: List[int]) -> int:
        odd, even = 0, nums[0]
        for i in range(1, len(nums)):
            odd, even = max(odd, even - nums[i]), max(even, odd + nums[i])
        return max(odd, even)
```


## Q4. 设计电影租借系统

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/design-movie-rental-system/](https://leetcode-cn.com/problems/design-movie-rental-system/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

明确题意，模拟即可。


### 代码

```c++
// Algorithm: simulation
// Time Complexity:
//      MovieRentingSystem: O(n \log n), n = entries.size()
//      search: O(1)
//      rent: O(n \log n)
//      drop: O(n \log n)
//      report: O(1)
// Space Complexity: O(n)
class MovieRentingSystem {
private:
    //price, shop, movie
    set<tuple<int, int, int>> rented;
    //movie, price, shop
    unordered_map<int, set<pair<int, int>>> movies;
    // shop, movie -> price
    unordered_map<int, unordered_map<int, int>> shopMovie2price;

public:
    MovieRentingSystem(int n, vector<vector<int>> &entries) {
        for (auto &&entry : entries) {
            int shop = entry[0], movie = entry[1], price = entry[2];
            movies[movie].emplace(price, shop);
            shopMovie2price[shop][movie] = price;
        }
    }

    vector<int> search(int movie) {
        vector<int> ans;
        for (auto it = movies[movie].begin(); it != movies[movie].end() and ans.size() < 5; ++it) {
            ans.emplace_back(it->second);
        }
        return ans;
    }

    void rent(int shop, int movie) {
        int price = shopMovie2price[shop][movie];
        rented.emplace(price, shop, movie);
        movies[movie].erase(make_pair(price, shop));
    }

    void drop(int shop, int movie) {
        int price = shopMovie2price[shop][movie];
        rented.erase(make_tuple(price, shop, movie));
        movies[movie].emplace(price, shop);
    }

    vector<vector<int>> report() {
        vector<vector<int>> ans;
        for (auto it = rented.begin(); it != rented.end() and ans.size() < 5; ++it) {
            auto [price, shop, movie] = *it;
            ans.push_back({shop, movie});
        }
        return ans;
    }
};
```

### 注意

合适的变量名以及适当的注释可以让代码写起来清晰很多，思路也不容易出错。
