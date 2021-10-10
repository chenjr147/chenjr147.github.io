---
author: "kevin"
title: "LeetCode 第 244 场周赛"
date: 2021-06-09T23:52:42+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
---

## Q1. 判断矩阵经轮转后是否一致

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/determine-whether-matrix-can-be-obtained-by-rotation/](https://leetcode-cn.com/problems/determine-whether-matrix-can-be-obtained-by-rotation/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

容易发现，旋转四次 `mat` 即回到了原始状态。

所以我们只需要至多旋转三次 `mat` ，每次判断是否和 `target` 一致即可。

### 代码

```c++
class Solution {
public:
    bool findRotation(vector<vector<int>>& mat, vector<vector<int>>& target) {
        // Algorithm: simulation
        // Time Complexity: O(n^2), n = mat.size()
        // Space Complexity: O(n^2), n = mat.size()
        // Corner Case:
        if (mat == target)
            return true;
        // Normal Case:
        int n = static_cast<int>(mat.size());
        auto rotate = [&]() {
            auto rotation = mat;
            for (int i = 0; i < n; ++i)
                for (int j = 0; j < n; ++j)
                    rotation[j][n - i - 1] = mat[i][j];
            mat = move(rotation);
        };
        for (int _ = 0; _ < 3; ++_) {
            rotate();
            if (mat == target)
                return true;
        }
        return false;
    }
};
```



## Q2. 使数组元素相等的减少操作次数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/reduction-operations-to-make-the-array-elements-equal/](https://leetcode-cn.com/problems/reduction-operations-to-make-the-array-elements-equal/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

使用 `map` 来模拟这一过程即可。

 `map` 可以带上自定义的排序规则，使用 `greater<>` 使得 `map` 逆序，更加方便。

### 代码

```c++
class Solution {
public:
    int reductionOperations(vector<int>& nums) {
        // Algorithm: simulation
        // Time Complexity: O(n log n), n = nums.size()
        // Space Complexity: O(n)
        map<int, int, greater<>> mp;
        for (auto&& num : nums)
            mp[num] += 1;
        int ans = 0, tot = 0;
        for (auto&& [num, cnt] : mp) {
            ans += tot;
            tot += cnt;
        }
        return ans;
    }
};
```


## Q3. 使二进制字符串字符交替的最少反转次数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/](https://leetcode-cn.com/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

遇到这种多种类型操作的问题，尽量地去把操作简化为一种。

对于类型 1 的操作，我们可以使用 `T = S + S` 的方法进行模拟。

对于类型 2 的操作，结合最终所需要的交替字符串，很容易想到只有两种交替情况，枚举即可。

### 代码

```c++
class Solution {
public:
    int minFlips(string s) {
        // Algorithm: sliding windows
        // Time Complexity: O(n), n = s.size()
        // Space Complexity: O(1)
        // Possible Cases:
        // 0101...
        // 1010...
        int n = static_cast<int>(s.size());
        int cnt0 = 0, cnt1 = 0, ans = n;
        s += s;
        for (int i = 0; i < 2 * n; ++i) {
            if (i >= n) {
                if (s[i - n] != ((i - n) % 2) + '0')
                    cnt0 -= 1;
                else
                    cnt1 -= 1;
            }
            if (s[i] != (i % 2) + '0')
                cnt0 += 1;
            else
                cnt1 += 1;
            if (i >= n - 1)
                ans = min({ans, cnt0, cnt1});
        }
        return ans;
    }
};
```


## Q4. 装包裹的最小浪费空间

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-space-wasted-from-packaging/](https://leetcode-cn.com/problems/minimum-space-wasted-from-packaging/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

遍历每个供应商，将其提供的箱子进行排序，贪心选择尽可能小的箱子装包裹。

在遍历之前，我们提前计算好 `packages` 的前缀和；遍历过程中，借助前缀和计算浪费的空间即可。

### 代码

```c++
using ull = unsigned long long;

class Solution {
public:
    int minWastedSpace(vector<int>& packages, vector<vector<int>>& boxes) {
        // Algorithm: greed
        // Time Complexity: O(m log m + m log n + n log n), n = packages.size(), m = sum(boxes[i].size())
        // Space Complexity: O(n), n = packages.size()
        int n = static_cast<int>(packages.size());
        sort(begin(packages), end(packages));
        vector<ull> prefix(n + 1);
        for (int i = 0; i < n; ++i)
            prefix[i + 1] = prefix[i] + packages[i];
        ull ans = numeric_limits<ull>::max();
        for (auto&& box : boxes) {
            ull last = 0, res = 0;
            sort(begin(box), end(box));
            if (box.back() < packages.back())
                continue;
            for (auto&& b : box) {
                auto curr = upper_bound(begin(packages), end(packages), b) - begin(packages);
                res += (curr - last) * b - (prefix[curr] - prefix[last]);
                last = curr;
                if (last >= n)
                    break;
            }
            ans = min(ans, res);
        }
        return ans == numeric_limits<ull>::max() ? -1 : ans % 1'000'000'007;
    }
};
```
