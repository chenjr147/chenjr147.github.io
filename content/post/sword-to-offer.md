---
author: "kevin"
title: "剑指 offer"
date: 2021-06-10T23:52:42+08:00
tags: [
    "Algorithm"
]
---

## 剑指 Offer 03. [数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

### 思路

原地哈希。

### 代码
```Go
// Algorithm: hash in-place
// Time Complexity: O(n), n = len(nums)
// Space Complexity: O(1)
func findRepeatNumber(nums []int) int {
	n := len(nums)
	for i := range nums {
		if nums[nums[i]%n] >= n {
			return nums[i] % n
		}
		nums[nums[i]%n] += n
	}
	return -1
}
```

## 剑指 Offer 04. [二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

### 思路

从数组的右上或左下开始查找，可以有效地进行减治。

### 代码

```Go
class Solution {
public:
    bool Find(int target, vector<vector<int>> array) {
        // Corner Case:
        if (array.empty() or array.front().empty()) {
            return false;
        }
        // Normal Case:
        int n = static_cast<int>(array.size());
        int x = 0, y = n - 1;
        while (x < n and y >= 0) {
            if (array[x][y] == target) {
                return true;
            }
            if (array[x][y] < target) {
                x += 1;
            } else {
                y -= 1;
            }
        }
        return false;
    }
};
```

## 剑指 Offer 05. [替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

### 思路

用 `strings.Builder`。

### 代码
```Go
// Algorithm: copy
// Time Complexity: O(n), n = len(s)
// Space Complexity: O(n)
func replaceSpace(s string) string {
	// return strings.ReplaceAll(s, " ", "%20")
	sb := &strings.Builder{}
	sb.Grow(3 * len(s))
	for i := range s {
		if s[i] == ' ' {
			sb.WriteString("%20")
		} else {
			sb.WriteByte(s[i])
		}
	}
	return sb.String()
}

```


## 剑指 Offer 06. [从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

### 思路

正向遍历，反转数组。

### 代码

```Go
// Algorithm: traverse and reverse
// Time Complexity: O(n), n = len(List)
// Space Complexity: O(n)
func reversePrint(head *ListNode) []int {
	ans := []int{}

	// traverse
	for curr := head; curr != nil; curr = curr.Next {
		ans = append(ans, curr.Val)
	}

	// reverse
	for i, j := 0, len(ans)-1; i < j; i, j = i+1, j-1 {
		ans[i], ans[j] = ans[j], ans[i]
	}
	return ans
}
```

## 剑指 Offer 09. [用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

### 思路

使用两个栈，交替 `push` 及 `pop` 实现队列。

### 代码

```Go
// Algorithm: double stacks
// Time Complexity: O(1)
// Space Complexity: O(n), n = len(data)
type CQueue struct {
	stk1, stk2 []int
}

func Constructor() CQueue {
	return CQueue{}
}

func (this *CQueue) AppendTail(value int) {
	this.stk1 = append(this.stk1, value)
}

func (this *CQueue) DeleteHead() int {
	// Corner case
	if len(this.stk1) == 0 && len(this.stk2) == 0 {
		return -1
	}

	// Pop stack 1 into stack 2
	if len(this.stk2) == 0 {
		for len(this.stk1) > 0 {
			this.stk2 = append(this.stk2, this.stk1[len(this.stk1)-1])
			this.stk1 = this.stk1[:len(this.stk1)-1]
		}
	}

	// return result
	result := this.stk2[len(this.stk2)-1]
	this.stk2 = this.stk2[:len(this.stk2)-1]
	return result
}
```

## 剑指 Offer 11. [旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

### 思路

二分查找，分类讨论。

### 代码

```Go
// Algorithm: binary search
// Time Complexity: n = len(nums)
//		Average: O(log n)
//		Worst:   O(n)
// Space Complexity: n = len(nums)
//		Average: O(1)
//		Worst:   O(log n)
func minArray(nums []int) int {
	var search func(left, right int) int
	search = func(left, right int) int {
		// Corner Case
		if left > right {
			return math.MaxInt32
		}

		if left == right || nums[left] < nums[right] {
			// return first element
			return nums[left]
		}

		mid := left + (right-left)/2
		if nums[left] == nums[right] {
			if nums[mid] == nums[left] {
				// nums[left] == nums[mid] == nums[right]
				minimum := nums[mid]
				if lhs := search(left, mid-1); lhs < minimum {
					minimum = lhs
				}
				if rhs := search(mid+1, right); rhs < minimum {
					minimum = rhs
				}
				return minimum
			}

			if nums[mid] < nums[left] {
				// nums[mid] < nums[left] == nums[right]
				return search(left, mid)
			}
			// nums[left] == nums[right] < nums[mid]
			return search(mid+1, right)
		}

		// nums[left] > nums[right]
		if nums[mid] >= nums[left] {
			return search(mid+1, right)
		}
		return search(left, mid)
	}
	return search(0, len(nums)-1)
}
```

## 剑指 Offer 24. [反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

### 思路

使用辅助的指针，迭代反转。必要时可以画图，辅助理解。

### 代码

```Go
// Algorithm: iterate
// Time Complexity: O(n), n = len(List)
// Space Complexity: O(1)
func reverseList(head *ListNode) *ListNode {
	var prev *ListNode
	for head != nil {
		next := head.Next
		head.Next = prev
		prev = head
		head = next
	}
	return prev
}
```

## 剑指 Offer 30. [包含min函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

### 思路

双栈思路，保持栈中每个元素在 `min` 栈中对应当前最小值。

### 代码

```Go
// Algorithm: double stacks
// Time Complexity: O(1)
// Space Complexity: O(n), n = len(data)
type MinStack struct {
	stk, min []int
}

/** initialize your data structure here. */
func Constructor() MinStack {
	return MinStack{}
}

func (this *MinStack) Push(x int) {
	this.stk = append(this.stk, x)
	if len(this.min) == 0 || x <= this.min[len(this.min)-1] {
		this.min = append(this.min, x)
	}
}

func (this *MinStack) Pop() {
	if this.stk[len(this.stk)-1] == this.min[len(this.min)-1] {
		this.min = this.min[:len(this.min)-1]
	}
	this.stk = this.stk[:len(this.stk)-1]
}

func (this *MinStack) Top() int {
	return this.stk[len(this.stk)-1]
}

func (this *MinStack) Min() int {
	return this.min[len(this.min)-1]
}
```

## 剑指 Offer 35. [复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

### 思路

类似原地哈希。在并发的场景下，需要借助额外的空间。

### 代码

```Go
// Algorithm: hash in place
// Time Complexity: O(n), n = len(List)
// Space Complexity: O(1)
func copyRandomList(head *Node) *Node {
	// Copy each node(without `random`) in place
	for curr := head; curr != nil; curr = curr.Next.Next {
		duplicate := Node{Val: curr.Val, Next: curr.Next}
		curr.Next = &duplicate
	}

	// Copy `random`
	for curr := head; curr != nil; curr = curr.Next.Next {
		if curr.Random != nil {
			curr.Next.Random = curr.Random.Next
		}
	}

	// Separate
	dummy := Node{}
	curr := &dummy
	for head != nil {
		curr.Next = head.Next
		head.Next = head.Next.Next
		curr = curr.Next
		head = head.Next
	}

	return dummy.Next
}
```

## 剑指 Offer 50. [第一个只出现一次的字符](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

### 思路

哈希表，两次遍历。

### 代码
```Go
// Algorithm: hash table
// Time Complexity: O(n), n = len(s)
// Space Complexity: O(1)
func firstUniqChar(s string) byte {
	cnt := [26]int{}

	for i := range s {
		cnt[s[i]-'a'] += 1
	}

	for i := range s {
		if cnt[s[i]-'a'] == 1 {
			return s[i]
		}
	}

	return ' '
}
```

## 剑指 Offer 53 - I. [在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

### 思路

二分查找。

### 代码
```Go
// Algorithm: binary search
// Time Complexity: O(log n), n = len(nums)
// Space Complexity: O(1)
func search(nums []int, target int) int {
	binarySearch := func(lower bool) int {
		low, high := 0, len(nums)-1

		for low < high {
			mid := (low + high + 1) / 2
			if lower {
				mid = (low + high) / 2
			}
			if nums[mid] == target {
				if lower {
					high = mid
				} else {
					low = mid
				}
			} else if nums[mid] < target {
				low = mid + 1
			} else {
				high = mid - 1
			}
		}

		return low
	}

	left := binarySearch(true)
	if left >= len(nums) || nums[left] != target {
		return 0
	}

	right := binarySearch(false)
	return right - left + 1
}
```

## 剑指 Offer 53 - II. [0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

### 思路

通过数字交换实现原地哈希。

### 代码
```Go
// Algorithm: hash in-place
// Time Complexity: O(n), n = len(nums)
// Space Complexity: O(1)
func missingNumber(nums []int) int {
	for i := range nums {
		for nums[i] != i && nums[i] != len(nums) {
			nums[nums[i]], nums[i] = nums[i], nums[nums[i]]
		}
	}

	for i := range nums {
		if nums[i] != i {
			return i
		}
	}

	return len(nums)
}
```

## 剑指 Offer 58 - II. [左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

### 思路

切片。

### 代码
```Go
// Algorithm: slice
// Time Complexity: O(n), n = len(s)
// Space Complexity: O(n)
func reverseLeftWords(s string, n int) string {
	return s[n:] + s[:n]
}
```

## JZ16. 合并两个排序的链表 

### 思路

允许对原链表进行修改的话，由于 `next` 指针可以变化，那么对于已经排好序的节点我们可以不用考虑了。

### 代码

```c++
// Algorithm: decrease and conquer
// Time Complexity: O(n + m), n = len(pHead1), m = len(pHead2)
// Space Complexity: O(1)
class Solution {
public:
    ListNode *Merge(ListNode *pHead1, ListNode *pHead2) {
        ListNode dummy(0);
        auto curr = &dummy;
        while (pHead1 != nullptr and pHead2 != nullptr) {
            if (pHead1->val < pHead2->val) {
                curr->next = pHead1;
                pHead1 = pHead1->next;
            } else {
                curr->next = pHead2;
                pHead2 = pHead2->next;
            }
            curr = curr->next;
        }
        if (pHead1 != nullptr) {
            curr->next = pHead1;
        } else {
            curr->next = pHead2;
        }
        return dummy.next;
    }
};
```


## JZ53. 表示数值的字符串


### 思路

对于这种比较复杂的状态转移，硬编码 `if-else` 会使得逻辑复杂，难以扩展；更好的方式是使用确定有限状态自动机 <abbr title="Deterministic finite automaton">DFA</abbr>。

这里我们先定义可能出现的几种符号所属类型：

| 字符      | 类型 |
| --------- | ---- |
| ‘+’ / ‘-’ | 0    |
| ‘0’ ~ ‘9’ | 1    |
| ‘.’       | 2    |
| ‘e’ / ‘E’ | 3    |
| others    | -1   |

那么，我们可以根据题意得到以下状态转移图：

<div style="text-align:center"><img src="dfa.svg" /></div>

参考状态转移图，我们可以得出以下状态转移矩阵：

| 状态／字符类型      | type0  | type1  | type2  | type3  |
| ------------------- | ------ | ------ | ------ | ------ |
| state0              | state1 | state2 | state3 | state0 |
| state1              | state0 | state2 | state3 | state0 |
| <mark>state2</mark> | state0 | state2 | state3 | state5 |
| state3              | state0 | state4 | state0 | state5 |
| <mark>state4</mark> | state0 | state4 | state0 | state5 |
| state5              | state6 | state7 | state0 | state0 |
| state6              | state0 | state7 | state0 | state0 |
| <mark>state7</mark> | state0 | state7 | state0 | state0 |

**说明**：

* `0` 为初始状态，如果从其他任意状态又回到了初始状态，我们将其标记为不合法的字符串。
* `2`, `4`, `7` 为合法的结束状态。

### 代码

```c++
class Solution {
public:
    bool isNumeric(string str) {
        // Algorithm: dfa
        // Time Complexity: O(n), n = str.size()
        // Space Complexity: O(1)
        // [+/-, 0..9, ., e/E]
        constexpr static array<array<int, 4>, 8> transfer{
            {{1, 2, 3, 0},
             {0, 2, 3, 0},
             {0, 2, 3, 5},
             {0, 4, 0, 5},
             {0, 4, 0, 5},
             {6, 7, 0, 0},
             {0, 7, 0, 0},
             {0, 7, 0, 0}}};
        auto char2type = [](const char& ch) {
            if (ch == '+' or ch == '-') {
                return 0;
            }
            if (isdigit(ch)) {
                return 1;
            }
            if (ch == '.') {
                return 2;
            }
            if (ch == 'e' or ch == 'E') {
                return 3;
            }
            return -1;
        };
        int state = 0;
        for (auto&& ch : str) {
            int type = char2type(ch);
            if (type == -1) {
                return false;
            }
            state = transfer[state][type];
            if (state == 0) {
                return false;
            }
        }
        return state == 2 or state == 4 or state == 7;
    }
};
```