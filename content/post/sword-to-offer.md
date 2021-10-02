---
author: "kevin"
title: "剑指 offer"
date: 2021-06-10T23:52:42+08:00
tags: [
    "Algorithm"
]
---

顺序参考[牛客网 - 剑指 offer](https://www.nowcoder.com/ta/coding-interviews)

## JZ1. 二维数组中的查找

### 思路

从数组的右上或左下开始查找，可以有效地进行减治。

### 代码

```c++
class Solution {
public:
    bool Find(int target, vector<vector<int>> array) {
        // Algorithm: decrease and conquer
        // Time Complexity: O(n), n = array.size()
        // Space Complexity: O(1)
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