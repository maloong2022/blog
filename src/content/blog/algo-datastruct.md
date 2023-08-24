---
author: Maloong
pubDatetime: 2023-08-25T00:18:00Z
title: Algorithm and Datastruct
postSlug: algo-datastruct
featured: false
draft: false
tags:
  - algo
  - datastruct
ogImage: ""
description: It's all about datastruct and algorithm.
---

It's all about datastruct and algorithm.

## Table of contents

## Stack

LIFO data structure. Last-In First-Out, stores objects into a sort of "vertical
tower" push() to add to the top; pop() to remove from the top.

### Using document

```java
    Stack<String> stack = new Stack<String>();
    // add a element to top
    stack.push("Hello");
    stack.push("Minecraft");
    // remove the top element
    stack.pop();
    // see the top element
    stack.peek();
    // search a element in the stack, return 1 to stack's size position
    // if not found return -1.
    stack.search("Minecraft");
    stack.isEmpty();

```

### Uses of stacks?

1. undo/redo features in text editors
2. moving back/forward through browser history
3. backtracking algorithms (maze, file directories)
4. calling functions (call stack)

## Queue

FIFO data structure. First-In First-Out, A collection designed for holding
elements prior to processing. Linear data structure. add() = enqueue, offer();
remove = dequeue, poll().

```java
Queue<String> queue = new LinkedList<String>();

// Inserts element into this queue
queue.offer("Karen");
queue.offer("Chad");
queue.peek();
queue.poll();
queue.isEmpty();
queue.size();
queue.contains("Karen")

```

### Uses of queue?

1. Keyboard Buffer (letters should appear on the screen in the order they are
   pressed)
2. Printer Queue (Print jobs should be completed in order)
3. Used in LinkedLists, PriorityQueues, Breadth-first search

## Priority Queue

A FIFO data structure that serves elements with the highest priorities first
before elements with lower priority.

```java
// 2.5, 3.0, 4.0
Queue<Double> queue = new PriorityQueue<>();
// 4.0, 3.0, 2.5
//Queue<Double> queue = new PriorityQueue<>(Collections.reverseOrder());
queue.offer(3.0);
queue.offer(2.5);
queue.offer(4.0);
while(!queue.isEmpty())){
    System.out.println(queue.poll());
}

```

## LinkedList

stores Nodes in 2 parts(data + address), Nodes are in non-consecutive memory
locations, Elements are linked using pointers.

### Advantages?

1. Dynamic Data Structure (allocates needed memory while running)
2. Insertion and Deletion of Nodes is easy. O(1)
3. No/Low memory waste

### Disadvantage?

1. Greater memory usage (additional pointer)
2. No random access of elements (no index [i])
3. Accessing/Searching elements is more time consuming. O(n)

### Uses?

1. implement Stacks/Queues
2. GPS navigation
3. music playlist

```java
LinkedList<String> linkedList = new LinkedList<String>();

// as a stack
linkedList.push("A");
linkedList.push("B");
linkedList.push("C");
linkedList.push("D");
linkedList.pop();

// as a queue
linkedList.offer("A");
linkedList.offer("B");
linkedList.offer("C");
linkedList.offer("D");
linkedList.poll();

// as link list
linkedList.add(4,"E");
linkedList.remove("E");

linkedList.peekFirst();
linkedList.peekLast();
linkedList.addFirst("0");
linkedList.addLast("G");
```

## Dynamic Array

Java = ArrayList;
C++ = Vector;
JavaScript = Array;
Python = List;

### Advantages

1. Random access of elements O(1)
2. Good locality of reference and data cache utilization
3. Easy to insert/delete at the end

### Disadvantages

1. Wastes more memory
2. Shifting elements is time consuming O(n)
3. Expanding/Shrinking the array is time consuming O(n)

```java
ArrayList<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");
list.add("D");
```

## Big O notation

"How code slows as data grows."

1. Describes the performance of an algorithm as the amount of data increases
2. Machine independent (# of steps to completion)
3. Ignore smaller operations O(n+1) -> O(n)

![Big O Notation](https://s2.loli.net/2023/08/24/6Lvykn8u9TC4gVo.png)

## Linear search

Iterate through a collection one element at a time

- runtime complexity: O(n)

### Disadvantage

Slow for large data sets

### Advantages

1. Fast for searches of small to medium data sets
2. Does not need to sorted
3. Useful for data structures that do not have random access (Linked List)

## Binary search

<video src="https://drive.google.com/file/d/1vNyfL_yit6OjUilijhVG7q9aoumhTLvl/view" controls="controls">
</video>

Search algorithm that finds the position of a target value within a **sorted array**.
Half of the array is eliminated during each "step";

```java
int array[] = new int[100];
int target = 42;

for(int i=0; i< 100;i++){
    array[i] = i;
}
int index = Arrays.binarySearch(array,tartget);
```

```java
public int binarySearch(int[] array, int target){
    int low =0;
    int high = array.length  - 1;

    while(low <= high){
        int middle = low + (high - low)/2;
        int value = array[middle];

        if(value < target){
            low = middle + 1;
        }else if(value > target){
            high = middle - 1;
        }else{
            return middle;// target found
        }

    }
    return -1;// target not found
}
```

## Interpolation search

Improvement over binary search best used for "uniformly" distributed data
"guesses" where a value might be based on calculated probe results, if probe
is incorrect, search area is narrowed, and a new probe is calculated

- average case: O(log(log(n)))
- worst case: O(n) [values increase exponentially]

```java

int [] array = {1,2,3,4,5,6,7,8,9};

int index  = interpolationSearch(array, 8);

```

```java
public int interpolationSearch(int[] array, int value){
    int high = array.length - 1;
    int low = 0;
    while(value >= array[low] && value <= array[high] && low <= high){
       int probe =low + (high - low) * (value - array[low]) /(array[high] - array[low]);
        if(array[probe] == value){
            return probe;
        }esle if(array[probe] < value){
            low = probe + 1;
        }else{
            high = probe -1;
        }
    }

    return -1;
}
```

## Bubble sort

<video src="https://drive.google.com/file/d/1DI1hhoAJcxeufizAyEPiHh8Wzfe484Rw/view" controls="controls">
</video>

Pairs of adjacent elements are compared, and the elements swapped if they are
not in order.

- Quadratic time O(n^2)
- small data set = okay-ish
- large data set = bad

```java
public void bubbleSort(int[] array){
    for(int i =0 ;i< array.length - 1; i++){
        for(int j=0;j < array.length - 1 - i;j++){
            if(array[j] > array[j+1]){
                int temp = array[j];
                array[j] = array[j+1]
                array[j+1] = temp;
            }
        }
    }
}
```

## Selection sort

<video src="https://drive.google.com/file/d/1Jhtp0Hz7vSi7uC9qGjXc51HRSHZKqITm/view" controls="controls">
</video>

search throgh an array and keep track of the minimum value during each interation,
At the end of each iteration, we swap variables.

- Quadratic time O(n^2)
- small data set = okay
- large data set = BAD

```java
public void selectionSort(int[] array){
    for(int i = 0;i< array.length - 1;i++){
        int min = i;
        for(int j=i+1;j< array.length;j++){
           if(array[min] > array[j]) {
                min = j;
            }
        }

        int temp = array[i];
        array[i] = array[min];
        array[min] = temp;
    }
}
```

## Insertion sort

<video src="https://drive.google.com/file/d/1ilD3t83Znove_Pe4ho-xmpHMdd1BLkQ5/view" controls="controls">
</video>

After comaring elements to the left shift elements to the right to make room to
insert a value

- Quadratic time O(n^2)
- small data set = decent
- large data set = BAD

Less step than Bubble sort, Best case is O(n) compared to Selection Sort O(n^2)

```java
public void insertionSort(int[] array){
    for(int i=1;i<array.lenght;i++){
        int temp = array[i];
        int j = i-1;
        while(j >=0 && array[j]>temp){
            array[j+1] = array[j];
            j--;
        }
        array[j+1] = temp;
    }
}
```

## Recursion

When a thing is defined in terms of itself. - Wikipedia. Apply the result of a
procedure, to a procedure. A recursive method calls itself. Can be a substitute
for iterat
