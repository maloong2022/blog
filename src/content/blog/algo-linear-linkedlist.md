---
author: Maloong
pubDatetime: 2023-03-13T01:32:00Z
title: Algorithm About LinkedList
postSlug: algo-linear-linkedlist
featured: true
draft: false
tags:
  - algo
  - linear
  - linkedlist
ogImage: ""
description: It's about the data struct of linear -- linkedlist and the Algorithm on it.
---

It's about the data struct of linear -- linkedlist and the Algorithm on it.

## Table of contents

## 链表

### 性质

- 内存可以不连续
- 插入删除快，查询慢
- 占用空间单链表是数组的两倍(需要存放 next 指针)，双链表为三倍。

### 关键点

- 熟练离散结点的结构设计
- 单向/双向链表的插入，删除等操作模版
- 清楚保护结点的运用

### 时间复杂度

| 操作                                                                       | 复杂度 |
| -------------------------------------------------------------------------- | ------ |
| Lookup                                                                     | O(n)   |
| Insert                                                                     | O(1)   |
| Delete                                                                     | O(1)   |
| Append(push back)                                                          | O(1)   |
| Prepend(push front)                                                        | O(1)   |
| Note：表中都是值知道插入或删除地址，而不是仅仅只知道元素，需要查询得到地址 |

## 实战

206. Reverse Linked List

Given the head of a singly linked list, reverse the list, and return the reversed list.

Example 1:

![](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

Input: head = [1,2,3,4,5]

Output: [5,4,3,2,1]

Example 2:

![](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

Input: head = [1,2]

Output: [2,1]

Example 3:

Input: head = []

Output: []

Constraints:

The number of nodes in the list is the range [0, 5000].

-5000 <= Node.val <= 5000

Follow up: A linked list can be reversed either iteratively or recursively. Could you implement both?

```java
class Solution {
    public ListNode reverseList(ListNode head){
      // Set the last node
      ListNode last = null;
      while(head != null){
        // copy the head next node
        ListNode nextHead = head.next;

        // reverse
        head.next = last;

        // move
        last = head;
        head = nextHead;
      }
    return last;
    }
}
```

25. Reverse Nodes in k-Group

Given the head of a linked list, reverse the nodes of the list k at a time, and return the modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes, in the end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be changed.

Example 1:

![](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg)

Input: head = [1,2,3,4,5], k = 2

Output: [2,1,4,3,5]

Example 2:

![](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg)

Input: head = [1,2,3,4,5], k = 3

Output: [3,2,1,4,5]

Constraints:

The number of nodes in the list is n.

1 <= k <= n <= 5000

0 <= Node.val <= 1000

Follow-up: Can you solve the problem in O(1) extra memory space?

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k){

    // avoid the last.next rasie a NullException
    ListNode protect  = new ListNode(0,head);
    ListNode last = protect;

    // group iterator
    while(head != null){
      // 1. make group: walk back k-1 step, find the head and end of the group
      ListNode end = getEnd(head,k);
      // if the group size less than k, not change it
      if (end==null){
        break;
      }

      ListNode nextGroupHead = end.next;


      //2. reverse the group
      reverseGroup(head, nextGroupHead);

      //3.connect the small LinkList bond
      // after group
      head.next = nextGroupHead;
      //before group
      last.next = end;


      // move last
      last = head;
      head = nextGroupHead;
    }
    return protect.next;
  }

  void reverseGroup(ListNode head,ListNode stop){

    // because the first is not null, so deal first step
    ListNode last = head;
    head = head.next;
    while(head!=stop){
      ListNode nextHead = head.next;
      head.next = last;
      last = head;
      head = nextHead;
    }
  }

  ListNode getEnd(ListNode head,int k){
    while(head!=null){
      k--;
      if (k==0){
        return head;
      }
      head = head.next;
    }
    // head = null
    return null;
  }
}
```

AcWing 136. 邻值查找

给定一个长度为 n 的序列 A，A 中的数各不相同。

对于 A 中的每一个数 Ai，求：

```math
min1≤j<i |Ai−Aj|
```

以及令上式取到最小值的 j (记为 Pi)。若最小值点不唯一，则选择使 Aj 较小的那个。

输入格式

第一行输入整数 n，代表序列长度。

第二行输入 n 个整数 A1…An,代表序列的具体数值，数值之间用空格隔开。

输出格式

输出共 n−1 行，每行输出两个整数，数值之间用空格隔开。

分别表示当 i 取 2∼n 时，对应的 min1≤j<i|Ai−Aj| 和 Pi 的值。

数据范围

n≤105,|Ai|≤109

输入样例：

```
3
1 5 3
```

输出样例：

```
4 1
2 1
```

解题思路：

- 按数值排序，建立有序双链表
- 链表虽然不能随机访问，但可以纪录 A 数组每个下标对应的链表结点
- 倒序考虑每个下标，只需要在链表中查找前驱，后继，然后删除结点

关键点：

- “索引”的灵活性--按下标/按值
- 不同“索引”的数据结构之间建立“映射”关系
- 倒序考虑问题

```java
import java.util.*;

public class Solution {
    public static class Node {
        long val;
        int idx;
        Node pre;
        Node next;
    }

    static int SIZE = 100005;
    static int[] a = new int[SIZE];
    static int[] ans = new int[SIZE];
    static Integer[] rk = new Integer[SIZE];
    static Node[] pos = new Node[SIZE];
    static int n;

    /**
     * 双链表插入模版，在node后面插入新结点
     *
     * @param node
     * @param idx
     * @return
     */
    static Node addNode(Node node, int idx) {
        Node newNode = new Node();
        newNode.val = a[idx];
        newNode.idx = idx;
        node.next.pre = newNode;
        newNode.next = node.next;
        node.next = newNode;
        newNode.pre = node;
        return newNode;
    }

    /**
     * 双链表删除模版
     *
     * @param node
     */
    static void deleteNode(Node node) {
        node.pre.next = node.next;
        node.next.pre = node.pre;
    }

    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        int n = input.nextInt();
        for (int i = 1; i <= n; i++) {
            a[i] = input.nextInt();
            rk[i] = i;
        }

        Arrays.sort(rk, 1, n + 1, Comparator.comparingInt(rki -> a[rki]));
        // 保护结点
        Node head = new Node();
        Node tail = new Node();
        head.next = tail;
        tail.pre = head;
        head.val = (long) -1e10;
        tail.val = (long) 1e10;
        for (int i = 1; i <= n; i++) {
            // 数值：a[rk[i]],下标：rk[i]
            pos[rk[i]] = addNode(tail.pre, rk[i]);
        }
        for (int i = n; i > 1; i--) {
            Node curr = pos[i];
            if (a[i] - curr.pre.val <= curr.next.val - a[i]) {
                ans[i] = curr.pre.idx;
            } else {
                ans[i] = curr.next.idx;
            }
            deleteNode(curr);
        }
        StringBuilder sb = new StringBuilder();
        for (int i = 2; i <= n; i++) {
            sb.append(Math.abs(a[ans[i]] - a[i]));
            sb.append(" ");
            sb.append(ans[i]);
            sb.append("\n");
        }
        System.out.println(sb);
    }

}
```

141. Linked List Cycle

Given head, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return true if there is a cycle in the linked list. Otherwise, return false.

Example 1:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

Example 2:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

Example 3:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

Constraints:

The number of the nodes in the list is in the range [0, 104].

-105 <= Node.val <= 105

pos is -1 or a valid index in the linked-list.

Follow up: Can you solve it using O(1) (i.e. constant) memory?

解题思路：

- 快满指针法，O(length)时间， O(1)空间
- 有环比定发生套圈(快慢指针相遇)，无环不会发生套圈(快指针到达 null)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode fast = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            head = head.next;
            if (head == fast) return true;
        }
        return false;
    }
}
```

142. Linked List Cycle II

Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return null.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to (0-indexed). It is -1 if there is no cycle. Note that pos is not passed as a parameter.

Do not modify the linked list.

Example 1:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

Example 2:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

Example 3:

![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

Constraints:

The number of the nodes in the list is in the range [0, 104].

-105 <= Node.val <= 105

pos is -1 or a valid index in the linked-list.

Follow up: Can you solve it using O(1) (i.e. constant) memory?

解题思路：

- 相遇时，有 2(l+p)=l+p+k\*r， 其中 k 为整数(套的圈数)
- 即 l=k*r - p = (k-1)*r + (r-p)
- 含义：从 head 走到 st，等于从 meet 走到 st，然后再绕几圈
- 此时开始让快慢指针与 head 同时移动，必定在环的起始点相遇

![cycle](https://photos.google.com/search/_tra_/photo/AF1QipNrjr6j1haYiEPF4_uKas2fhTICSzEVRmMHD1Gz)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while(fast!=null&&fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
            if (fast==slow){
                while(head!=slow){
                    head = head.next;
                    slow = slow.next;
                }
                return head;
            }
        }
        return null;
    }
}
```

21. Merge Two Sorted Lists

You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists in a one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.

Example 1:

![](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

Example 2:

```
Input: list1 = [], list2 = []
Output: []
```

Example 3:

```
Input: list1 = [], list2 = [0]
Output: [0]
```

Constraints:

The number of nodes in both lists is in the range [0, 50].

-100 <= Node.val <= 100

Both list1 and list2 are sorted in non-decreasing order.

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode protect = new ListNode(-1);
        ListNode cur = protect;
        while(list1!=null && list2!=null){
            if(list1.val <= list2.val){
                cur.next = list1;
                list1 = list1.next;
            }else{
                cur.next = list2;
                list2 = list2.next;
            }
            cur = cur.next;
        }
        cur.next = list1==null?list2:list1;
        return protect.next;
    }
}
```

## Link List Move Model

```java
while(head != null) {
  head = head.next;
}
```

## Link List Reverse Model

```java
ListNode last = null;
while(head!=null){
    // make a copy
    ListNode nextHead = head.next;

    // some operation

    // move
    last = head;
    head = nextHead;
}
```
