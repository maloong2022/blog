---
author: Maloong
pubDatetime: 2023-03-12T01:40:00Z
title: Algorithm About Stack
postSlug: algo-linear-stack
featured: false
draft: false
tags:
  - algo
  - linear
  - stack
ogImage: ""
description: It's about the data struct of linear -- stack and the Algorithm on it.
---

### 栈(stack)

栈的关键点

- 数据结构的栈定义和操作系统层面的栈的关系
- 栈本质是定义了一组先进后出**接口规范**，可以有不同的实现
- 在线性结构尾部进行操作的，要能联想到使用栈（尾部后入先出）

Last in - First out

主要操作复杂度

- 入栈 O(1) （栈尾部插入）
- 出栈 O(1)（栈尾部弹出）
- 访问 O(1) （访问栈顶）

### Java JDK

Stack

### 实战

20. Valid Parentheses

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

Example 1:

```
Input: s = "()"
Output: true
```

Example 2:

```
Input: s = "()[]{}"
Output: true
```

Example 3:

```
Input: s = "(]"
Output: false
```

Constraints:

- 1 <= s.length <= 104
- s consists of parentheses only '()[]{}'.

解题思路：

最近相关性，用栈。

```java
class Solution {
    private Stack<Character> a = new Stack();

    public boolean isValid(String s) {
        for(int i=0;i<s.length();i++){
            char ch = s.charAt(i);
            if (ch == '(' || ch == '[' || ch == '{'){
                a.push(ch);
            }else {
                if (a.isEmpty()) return false;
                if (ch == ')' && a.pop() != '(') return false;
                if (ch == ']' && a.pop() != '[') return false;
                if (ch == '}' && a.pop() != '{') return false;
            }
        }
        return a.isEmpty();
    }
}
```

678. Valid Parenthesis String

Given a string s containing only three types of characters: '(', ')' and '\*', return true if s is valid.

The following rules define a valid string:

- Any left parenthesis '(' must have a corresponding right parenthesis ')'.
- Any right parenthesis ')' must have a corresponding left parenthesis '('.
- Left parenthesis '(' must go before the corresponding right parenthesis ')'.
- '\*' could be treated as a single right parenthesis ')' or a single left parenthesis '(' or an empty string "".

Example 1:

```
Input: s = "()"
Output: true
```

Example 2:

```
Input: s = "(*)"
Output: true
```

Example 3:

```
Input: s = "(*))"
Output: true
```

Constraints:

- 1 <= s.length <= 100
- s[i] is '(', ')' or '\*'.

```java
class Solution {
    public boolean checkValidString(String s) {
        Stack<Integer> leftStack = new Stack();
        Stack<Integer> starStack = new Stack();
        int n = s.length();
        for(int i=0; i< n;i++){
            char ch = s.charAt(i);
            if (Character.isSpaceChar(ch)) continue;
            if(ch=='('){
                // push i not the char
                leftStack.push(i);
            }else if(ch=='*'){
                starStack.push(i);
            }else {
                if(!leftStack.isEmpty()){
                    leftStack.pop();
                }else if(!starStack.isEmpty()){
                    starStack.pop();
                }else {
                    return false;
                }
            }
        }
        while(!leftStack.isEmpty() && !starStack.isEmpty()){
            int leftIndex = leftStack.pop();
            int starIndex = starStack.pop();
            // If the '(' come after the '*', such as '*(' is not valid
            if(leftIndex>starIndex){
                return false;
            }
        }
        return leftStack.isEmpty();
    }
}
```

232. Implement Queue using Stacks

Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (push, peek, pop, and empty).

Implement the MyQueue class:

- void push(int x) Pushes element x to the back of the queue.
- int pop() Removes the element from the front of the queue and returns it.
- int peek() Returns the element at the front of the queue.
- boolean empty() Returns true if the queue is empty, false otherwise.

Notes:

- You must use only standard operations of a stack, which means only push to top, peek/pop from top, size, and is empty operations are valid.
- Depending on your language, the stack may not be supported natively. You may simulate a stack using a list or deque (double-ended queue) as long as you use only a stack's standard operations.

Example 1:

```
Input
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
Output
[null, null, null, 1, 1, false]

Explanation
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

Constraints:

- 1 <= x <= 9
- At most 100 calls will be made to push, pop, peek, and empty.
- All the calls to pop and peek are valid.

**Follow-up**: Can you implement the queue such that each operation is amortized O(1) time complexity? In other words, performing n operations will take overall O(n) time even if one of those operations may take longer.

```java
class MyQueue {

    Stack<Integer> inStack;
    Stack<Integer> outStack;

    public MyQueue() {
        inStack = new Stack();
        outStack = new Stack();
    }

    public void push(int x) {
        inStack.push(x);
    }

    public int pop() {
        if(outStack.isEmpty()){
            while(!inStack.isEmpty()){
                outStack.push(inStack.pop());
            }
        }
       return outStack.pop();
    }

    public int peek() {
        if(outStack.isEmpty()){
            while(!inStack.isEmpty()){
                outStack.push(inStack.pop());
            }
        }
       return outStack.peek();
    }

    public boolean empty() {
        return inStack.isEmpty()&&outStack.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */

```

思路

1. 定义入栈和出栈
2. 每次 push 都从入栈 push
3. 每次出栈的时候，先检查入栈是否是空的，如果不空，将入栈里面的元素先 pop 出去，压入出栈，再冲出栈进行 pop

lc155. Min Stack

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

Implement the MinStack class:

- MinStack() initializes the stack object.
- void push(int val) pushes the element val onto the stack.
- void pop() removes the element on the top of the stack.
- int top() gets the top element of the stack.
- int getMin() retrieves the minimum element in the stack.
- You must implement a solution with O(1) time complexity for each function.

Example 1:

```
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

Constraints:

- -231 <= val <= 231 - 1
- Methods pop, top and getMin operations will always be called on non-empty stacks.
- At most 3 \* 104 calls will be made to push, pop, top, and getMin.

```java
class MinStack {
    private Stack<Integer> values;
    private Stack<Integer> mins;

    public MinStack() {
        values = new Stack();
        mins = new Stack();
    }

    public void push(int val) {
        values.push(val);
        if(mins.isEmpty()) mins.push(val);
        else mins.push(Math.min(val,mins.peek()));
    }

    public void pop() {
        mins.pop();
        values.pop();
    }

    public int top() {
        return values.peek();
    }

    public int getMin() {
        return mins.peek();
    }
}
```
