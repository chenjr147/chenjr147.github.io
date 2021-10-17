---
author: "kevin"
title: "LeetCode 第 263 场周赛"
date: 2021-10-17T21:16:58+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 检查句子中的数字是否递增

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/check-if-numbers-are-ascending-in-a-sentence/](https://leetcode-cn.com/problems/check-if-numbers-are-ascending-in-a-sentence/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟。

### 代码

```go
// Algorithm: simulation
// Time Complexity: O(n), n = len(s)
// Space Complexity: O(n)
func areNumbersAscending(s string) bool {
	last := -1
	for _, split := range strings.Split(s, " ") {
		if unicode.IsDigit(rune(split[0])) {
			if curr, _ := strconv.Atoi(split); last < curr {
				last = curr
			} else {
				return false
			}
		}
	}
	return true
}
```



## Q2. 简易银行系统

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/simple-bank-system/](https://leetcode-cn.com/problems/simple-bank-system/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

模拟。

### 代码

```go
// Algorithm: simulation
// Time Complexity: O(1)
// Space Complexity: O(1)
type Bank struct{ balance []int64 }

func Constructor(balance []int64) Bank { return Bank{balance: balance} }

func (this *Bank) Transfer(account1 int, account2 int, money int64) bool {
	if account1 <= 0 || account1 > len(this.balance) || account2 <= 0 || account2 > len(this.balance) || this.balance[account1-1] < money {
		return false
	}
	this.balance[account1-1] -= money
	this.balance[account2-1] += money
	return true
}

func (this *Bank) Deposit(account int, money int64) bool {
	if account <= 0 || account > len(this.balance) {
		return false
	}
	this.balance[account-1] += money
	return true
}

func (this *Bank) Withdraw(account int, money int64) bool {
	if account <= 0 || account > len(this.balance) || this.balance[account-1] < money {
		return false
	}
	this.balance[account-1] -= money
	return true
}
```


## Q3. 统计按位或能得到最大值的子集数目

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/count-number-of-maximum-bitwise-or-subsets/](https://leetcode-cn.com/problems/count-number-of-maximum-bitwise-or-subsets/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

由于数组的值都大于 $0$，所以显然异或的最大值是所有值的异或和；观察数据范围，位运算枚举即可。

### 代码

```go
// Algorithm: bitwise enumerate
// Time Complexity: O(n * 2^n), n = len(nums)
// Space Complexity: O(1)
func countMaxOrSubsets(nums []int) int {
	maximum := 0
	for _, num := range nums {
		maximum |= num
	}

	n := len(nums)
	ans := 0
	for mask := 1<<n - 1; mask >= 0; mask -= 1 {
		sum := 0
		for bit := 0; bit < n; bit += 1 {
			if mask&(1<<bit) > 0 {
				sum |= nums[bit]
			}
		}
		if sum == maximum {
			ans += 1
		}
	}

	return ans
}
```


## Q4. 到达目的地的第二短时间

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/second-minimum-time-to-reach-destination/](https://leetcode-cn.com/problems/second-minimum-time-to-reach-destination/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

存储两个最短时间的 `Dijkstra` 算法。

### 代码

```go
// Algorithm: dijkstra
// Time Complexity: O(E \log V)
// Space Complexity: O(E)
type Item struct {
	position int
	time     int
	// The index is needed by update and is maintained by the heap.Interface methods.
	index int // The index of the item in the heap.
}

type priority_queue []*Item

func (h priority_queue) Len() int            { return len(h) }
func (h priority_queue) Less(i, j int) bool  { return h[i].time < h[j].time } // > 为最大堆
func (h priority_queue) Swap(i, j int)       { h[i], h[j] = h[j], h[i]; h[i].index = i; h[j].index = j }
func (h *priority_queue) Push(v interface{}) { *h = append(*h, v.(*Item)) }
func (h *priority_queue) Pop() interface{}   { a := *h; v := a[len(a)-1]; *h = a[:len(a)-1]; return v }
func (h *priority_queue) push(item *Item)    { item.index = len(*h); heap.Push(h, item) }
func (h *priority_queue) pop() *Item         { return heap.Pop(h).(*Item) }

func secondMinimum(n int, edges [][]int, time int, change int) int {
	adj := make([][]int, n+1)
	for _, edge := range edges {
		u, v := edge[0], edge[1]
		adj[u] = append(adj[u], v)
		adj[v] = append(adj[v], u)
	}

	distance := make([][2]int, n+1)
	for i := 1; i <= n; i += 1 {
		distance[i][0], distance[i][1] = -1, -1
	}

	h := &priority_queue{&Item{position: 1, time: 0, index: 0}}
	for h.Len() > 0 {
		curr := h.pop()

		if curr.time/change%2 == 1 {
			curr.time = (curr.time/change + 1) * change
		}

		for _, next := range adj[curr.position] {
			if distance[next][0] == -1 {
				distance[next][0] = curr.time + time
			} else if distance[next][0] != curr.time+time && distance[next][1] == -1 {
				distance[next][1] = curr.time + time
			} else {
				continue
			}

			if distance[n][1] != -1 {
				return curr.time + time
			}

			h.push(&Item{position: next, time: curr.time + time})
		}
	}
	return 0
}

```
