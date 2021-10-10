---
author: "kevin"
title: "LeetCode 第 262 场周赛"
date: 2021-10-10T17:23:21+08:00
categories:
- 周赛
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 至少在两个数组中出现的值

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/two-out-of-three/](https://leetcode-cn.com/problems/two-out-of-three/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

集合。

### 代码

```python
# Algorithm: set
# Time Complexity: O(n), n = sum(len(nums{1..3}))
# Space Complexity: O(n)
class Solution:
    def twoOutOfThree(self, nums1: List[int], nums2: List[int], nums3: List[int]) -> List[int]:
        s1, s2, s3 = set(nums1), set(nums2), set(nums3)
        return list((s1 & s2) | (s2 & s3) | (s1 & s3))
```

## Q2. 获取单值网格的最小操作数

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/minimum-operations-to-make-a-uni-value-grid/](https://leetcode-cn.com/problems/minimum-operations-to-make-a-uni-value-grid/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

中位数。原型是 [462. 最少移动次数使数组元素相等 II](https://leetcode-cn.com/problems/minimum-moves-to-equal-array-elements-ii/)。


### 代码

```python
# Algorithm: midian
# Time Complexity: O(row * col), row = len(grid), col = len(grid[0])
# Space Complexity: O(row * col)
class Solution:
    def minOperations(self, grid: List[List[int]], x: int) -> int:
        nums = list()
        for row in grid:
            for num in row:
                if abs(grid[0][0] - num) % x != 0:
                    return -1
                nums.append(num)

        nums.sort()
        return sum([abs(num - nums[len(nums) // 2]) // x for num in nums])
```


## Q3. 股票价格波动

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/stock-price-fluctuation/](https://leetcode-cn.com/problems/stock-price-fluctuation/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟。


### 代码

```c++
// Algorithm: simulation
// Time Complexity: O(log n), n = prices.size()
// Space Complexity: O(n)
class StockPrice {
 private:
  // timestamp -> price
  map<int, int> time2price;
  // current prices
  multiset<int> prices;

 public:
  StockPrice() {}

  void update(int timestamp, int price) {
    if (time2price.find(timestamp) != time2price.end()) {
      int oldPrice = time2price[timestamp];
      prices.erase(prices.find(oldPrice));
    }
    time2price[timestamp] = price;
    prices.emplace(price);
  }

  int current() { return time2price.rbegin()->second; }

  int maximum() { return *prices.rbegin(); }

  int minimum() { return *prices.begin(); }
};
```


## Q4. 将数组分成两个数组并最小化数组和的差

> 来源：力扣（LeetCode）
>
> 链接：[https://leetcode-cn.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/](https://leetcode-cn.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

Meet in the middle.

### 代码

```Go
// Algorithm: meet in the middle
// Time Complexity: O(n * 2^n * log C(n, n/2)), n = len(nums) / 2
// Space Complexity: O(n * 2^n)
func minimumDifference(nums []int) int {
	construct := func(slice []int, all [][]int) {
		// unique
		seen := make([]map[int]struct{}, len(slice)+1)
		for i := range seen {
			seen[i] = make(map[int]struct{})
		}

		// enumerate
		for mask := 0; mask < 1<<len(slice); mask += 1 {
			ones, total := 0, 0
			for bit := 0; bit < len(slice); bit += 1 {
				if mask&(1<<bit) > 0 {
					total += slice[bit]
					ones += 1
				}
			}
			if _, ok := seen[ones][total]; !ok {
				all[ones] = append(all[ones], total)
				seen[ones][total] = struct{}{}
			}
		}

		for i := range all {
			sort.Ints(all[i])
		}
	}

	// reduce by half
	n := len(nums) / 2
	lhs, rhs := make([][]int, n+1), make([][]int, n+1)
	construct(nums[:n], lhs)
	construct(nums[n:], rhs)

	// sum(nums)
	sum := 0
	for _, num := range nums {
		sum += num
	}

	// meet in the middle
	ans := math.MaxInt32
	for i := 0; i <= n; i += 1 {
		for _, leftSum := range lhs[i] {
			j := sort.SearchInts(rhs[n-i], sum/2-leftSum)
			for k := -1; k <= 1; k += 1 {
				if j+k >= 0 && j+k < len(rhs[n-i]) {
					rightSum := rhs[n-i][j+k]
					minus := sum - 2*leftSum - 2*rightSum
					if minus < 0 {
						minus = -minus
					}
					if minus < ans {
						ans = minus
					}
				}
			}
		}
	}

	return ans
}

```
