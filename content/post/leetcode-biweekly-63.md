---
author: "kevin"
title: "LeetCode 第 63 场双周赛"
date: 2021-10-17T14:16:14+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 使每位学生都有座位的最少移动次数

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/minimum-number-of-moves-to-seat-everyone/](https://leetcode-cn.com/problems/minimum-number-of-moves-to-seat-everyone/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

直觉上，我们应该对两个数组进行排序，然后从左到右每个学生依次坐在从左到右的各个位置上。

下面我们采用反证法进行证明，也即需要证明任意调换位置，最终的移动次数只会不变或增多。

假设 $a \leqslant b$, $c \leqslant d$, 则需要证明 $|a-c| + |b-d| \leqslant |a-d| + |b-c|$。

方便起见，同时保证结果不受影响，我们让四个数同减去 $a$，证明如下

$$ \begin{aligned} |c|+|b-d| &\leqslant |d|+|b-c| \\ c^2+2|c||b-d| +(b-d)^2 &\leqslant d^2+2|d||b-c| +(b-c)^2 \\ |c||b-d|-bd &\leqslant |d||b-c|-bc \\ |c||b-d| - |d||b-c| &\leqslant b(d-c) \\ |bc-cd|-|bd-cd| &\leqslant b|d-c| \\ |bc-cd|-|bd-cd| &\leqslant|bd-bc| \end{aligned} $$

此外，我们知道 

$$
-|x| \leqslant x \leqslant |x| , -|y| \leqslant y \leqslant |y|
 $$
 
 相加可得 $-(|x|+|y|) \leqslant x + y \leqslant |x|+|y|$，可得 $x+y \leqslant |x+y| \leqslant ||x|+|y||$。故 $|x+y|\leqslant|x|+|y|$；令$x=x-y$，可得$|x|\leqslant|x-y|+|y|$，也即 $|x|-|y|\leqslant|x-y|$。

那么可以得到 $|bc-cd|-|bd-cd|\leqslant|bc-bd|$，得证。
 

### 代码

```go
// Algorithm: math
// Time Complexity: O(n \log n + m \log m), n = len(seats), m = len(students)
// Space Complexity: O(1)
func minMovesToSeat(seats []int, students []int) int {
	sort.Ints(seats)
	sort.Ints(students)

	abs := func(x int) int {
		if x < 0 {
			return -x
		}
		return x
	}

	ans := 0
	for i := range seats {
		ans += abs(seats[i] - students[i])
	}

	return ans
}
```



## Q2. 如果相邻两个颜色均相同则删除当前颜色

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/remove-colored-pieces-if-both-neighbors-are-the-same-color/](https://leetcode-cn.com/problems/remove-colored-pieces-if-both-neighbors-are-the-same-color/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

比较符合要求的颜色数量即可。

### 代码

```go
// Algorithm: traverse
// Time Complexity: O(n), n = len(colors)
// Space Complexity: O(1)
func winnerOfGame(colors string) bool {
	count := func(color byte) int {
		cnt := 0
		for i := 1; i+1 < len(colors); i += 1 {
			if colors[i-1] == color && colors[i] == color && colors[i+1] == color {
				cnt += 1
			}
		}
		return cnt
	}
	return count('A') > count('B')
}
```


## Q3. 网络空闲的时刻

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/the-time-when-the-network-becomes-idle/](https://leetcode-cn.com/problems/the-time-when-the-network-becomes-idle/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

由于图中的边权全为 $1$，故不需要进行松弛操作。我们可以通过广度优先搜索在 $\mathcal{O}(n)$ 时间内得到主服务器到各个服务器之间的最短距离。

另外还需要考虑重发的情况，假设服务器 $i$ 到主服务器的最短距离为 $distance[i]$，那么需要 $2*distance[i]$ 的时间得到回复。所以在 $2*distance[i]-1$ 的时间内，服务器 $i$ 会进行重试，简单计算可知，其最后一条发送的信息会在 $\lfloor \frac{2*distance[i]-1}{patient[i]} \rfloor * patient[i]$ 后收到，则总时间为$2*distance[i] + \lfloor \frac{2*distance[i]-1}{patient[i]} \rfloor * patient[i]$ 。

### 代码

```go
// Algorithm: bfs + math
// Time Complexity: O(n), n = len(patience)
// Space Complexity: O(n)
func networkBecomesIdle(edges [][]int, patience []int) int {
	n := len(patience)

	// construct graph
	adj := make([][]int, n)
	for _, edge := range edges {
		u, v := edge[0], edge[1]
		adj[u] = append(adj[u], v)
		adj[v] = append(adj[v], u)
	}

	// bfs
	distance := make([]int, n)
	q := make([]int, 0, n)
	q = append(q, 0)
	for len(q) > 0 {
		cur := q[0]
		q = q[1:]
		for _, next := range adj[cur] {
			if distance[next] == 0 {
				distance[next] = distance[cur] + 1
				q = append(q, next)
			}
		}
	}

	// get maximum cost
	ans := 0
	for i := 1; i < n; i += 1 {
		if cost := 2*distance[i] + (2*distance[i]-1)/patience[i]*patience[i]; cost > ans {
			ans = cost
		}
	}

	return ans + 1
}

```


## Q4. 两个有序数组的第 K 小乘积

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/kth-smallest-product-of-two-sorted-arrays/](https://leetcode-cn.com/problems/kth-smallest-product-of-two-sorted-arrays/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

二分套二分，注意细节。

### 代码

```go
// Algorithm: binary search
// Time Complexity: O(n \log C), n = len(nums2), C = 2e10
// Space Complexity: O(1)
func kthSmallestProduct(nums1 []int, nums2 []int, k int64) int64 {
	sort.Ints(nums1)
	sort.Ints(nums2)

	sort.SearchInts(nums1, 0)
	ok := func(mid int) bool {
		cnt := int64(0)
		for _, num := range nums1 {
			if num == 0 {
				if mid >= 0 {
					cnt += int64(len(nums2))
				}
			} else if num > 0 {
				cnt += int64(sort.Search(len(nums2), func(i int) bool { return nums2[i]*num > mid }))
			} else {
				cnt += int64(len(nums2) - sort.Search(len(nums2), func(i int) bool { return nums2[i]*num <= mid }))
			}
		}
		return cnt >= k
	}

	low, high := -10_000_000_000, 10_000_000_000
	for low+1 < high {
		mid := low + (high-low)/2
		if ok(mid) {
			high = mid
		} else {
			low = mid + 1
		}
	}

	if ok(low) {
		return int64(low)
	}
	return int64(high)
}

```

