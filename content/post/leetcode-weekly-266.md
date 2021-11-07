---
author: "kevin"
title: "LeetCode 第 266 场周赛"
date: 2021-11-07T22:28:57+08:00
tags: [
    "LeetCode",
    "Competitive Programming",
    "Algorithm"
]
mathjax: true
---

## Q1. 统计字符串中的元音子字符串

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/count-vowel-substrings-of-a-string/](https://leetcode-cn.com/problems/count-vowel-substrings-of-a-string/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

双指针。

### 代码

```golang
// Algorithm: two pointers
// Time Complexity: O(n), n = len(word)
// Space Complexity: O(n)
func countVowelSubstrings(word string) int {
	ans := 0

	for _, s := range strings.FieldsFunc(word, func(r rune) bool { return !strings.ContainsRune("aeiou", r) }) {
		cnt := map[byte]int{}
		left := 0
		for right := range s {
			cnt[s[right]] += 1
			for cnt[s[left]] > 1 {
				cnt[s[left]] -= 1
				left += 1
			}
			if cnt['a']*cnt['e']*cnt['i']*cnt['o']*cnt['u'] > 0 {
				ans += left + 1
			}
		}
	}

	return ans
}
```



## Q2. 所有子字符串中的元音

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/vowels-of-all-substrings/](https://leetcode-cn.com/problems/vowels-of-all-substrings/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

数学分析。

### 代码

```golang
// Algorithm: math
// Time Complexity: O(n), n = len(word)
// Space Complexity: O(1)
func countVowels(word string) int64 {
	ans := int64(0)

	vowels := map[rune]struct{}{'a': {}, 'e': {}, 'i': {}, 'o': {}, 'u': {}}
	for i, c := range word {
		if _, ok := vowels[c]; ok {
			ans += int64(i+1) * int64(len(word)-i)
		}
	}

	return ans
}
```


## Q3. 分配给商店的最多商品的最小值

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/minimized-maximum-of-products-distributed-to-any-store/](https://leetcode-cn.com/problems/minimized-maximum-of-products-distributed-to-any-store/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

极小化极大/极大化极小，基本都可以用二分来做。

### 代码

```golang
// Algorithm: binary search
// Time Complexity: O(m * \log k), m = len(quantities), k = max(quantities)
// Space Complexity: O(1)
func minimizedMaximum(n int, quantities []int) int {
	return sort.Search(100_000, func(x int) bool {
		if x == 0 {
			return false
		}
		cnt := 0
		for _, q := range quantities {
			cnt += q / x
			if q%x > 0 {
				cnt += 1
			}
			if cnt > n {
				return false
			}
		}
		return true
	})
}
```


## Q4. 最大化一张图中的路径价值

> 来源：力扣（LeetCode）
>
> 链接： [https://leetcode-cn.com/problems/maximum-path-quality-of-a-graph/](https://leetcode-cn.com/problems/maximum-path-quality-of-a-graph/)
>
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

观察数据范围，暴搜的复杂度最高只会到 $4^{10}=1048576$，直接 `dfs` 即可。

### 代码

```golang
// Algorithm: dfs
// Time Complexity: O(n^m), n = max(edge num of nodes), m = count of reachable nodes
// Space Complexity: O(max(k, \log m)), k = len(values)
func maximalPathQuality(values []int, edges [][]int, maxTime int) int {
	type adj struct {
		next int
		time int
	}

	n := len(values)
	graph := make([][]adj, n)
	for _, edge := range edges {
		x, y, t := edge[0], edge[1], edge[2]
		graph[x] = append(graph[x], adj{y, t})
		graph[y] = append(graph[y], adj{x, t})
	}

	ans := 0
	visited := make([]int, n)

	var dfs func(cur, value, time int)
	dfs = func(cur, value, time int) {
		if cur == 0 && value > ans {
			ans = value
		}
		for _, edge := range graph[cur] {
			if total := time + edge.time; total <= maxTime {
				if visited[edge.next] == 0 {
					value += values[edge.next]
				}
				visited[edge.next] += 1
				dfs(edge.next, value, total)
				visited[edge.next] -= 1
				if visited[edge.next] == 0 {
					value -= values[edge.next]
				}
			}
		}
	}

	visited[0] += 1
	dfs(0, values[0], 0)

	return ans
}
```
