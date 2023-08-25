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
for iteration.

Divide a problem into sub-problems of the same type as the original. Commonly
used with advanced sorting algorithms and navigating trees.

### Advantages

1. easier to read/write
2. easier to debug

### Disadvantage

1. sometimes slower
2. uses more memory

|                  | **iteration**                   | **recursion**                    |
| ---------------- | ------------------------------- | -------------------------------- |
| **implemention** | uses loops                      | calls iteself                    |
| **state**        | control index (int steps = 0)   | parameter values walk(int steps) |
| **progression**  | moves toward value in condition | converge towards base case       |
| **termination**  | index satisfies condition       | base case = true                 |
| **size**         | more code less memory           | less code more momery            |
| **speed**        | faster                          | slower                           |

## Merge sort

<video src="https://drive.google.com/file/d/1frkE1AmPgeA-vxSQ0GiNn22HEvr0nqMR/view" controls="controls">
</video>

recursively divide array in 2, sort, re-combine.

- run-time complexity = O(nlog(n))
- space complexity = O(n);

![Space Used](https://s2.loli.net/2023/08/25/wbrqYjkdUJVmH8R.png)

```java
public void mergeSort(int[] array){
    int length = array.length;
    if(length<=1) return; // base case

    int middle = length / 2;
    int[] leftArray = new int[middle];
    int[] rightArray = new int[length-middle];

    int i=0,l=0,r=0// indices

    // copy elements to right and left array
    for(;i<length;i++){
        if(i<middle){
            leftArray[l] = array[i];
            l++
        }else{
            rightArray[r] = array[i];
            r++;
        }
    }

    mergeSort(leftArray):
    mergeSort(rightArray);
    // merge
    merge(leftArray,rightArray,array);


}

public void merge(int[] leftArray, int[] rightArray, int[] array){
    int leftSize = leftArray.length;
    int rightSize = rightArray.length;
    int i = 0, l = 0, r = 0; // indices

    //check the conditions for merging
    while(l < leftSize && r < rightSize){
        if(leftArray[l] < rightArray[r]){
            array[i] = leftArray[l];
            i++;
            l++;
        }else{
            array[i] = rightArray[r];
            i++;
            r++;
        }
    }
    while(l < leftSize){
        array[i] = leftArray[l];
        i++;
        l++;
    }
    while(r < rightSize){
        array[i] = rightArray[r];
        i++;
        r++;
    }
}
```

## Quick sort

<video src="https://drive.google.com/file/d/1Znkjws2-WZV_Uq3KzlDNUF9rsKBFMsYG/view" controls="controls">
</video>

Moves smaller elements to left of a pivot. recursively divide array in 2 partitions

- run-time complexity = Best case O(n(log(n))). Average case O(nlog(n)), worst
  case O(n^2) if already sorted
- space complexity = O(log(n)) due to recursion

```java
public void quickSort(int[] array, int start, int end) {
    if(end <= start) return;// base case

    int pivotIndex = partition(array, start, end);
    quickSort(array, start,pivotIndex-1);
    quickSort(array,pivotIndex+1,end);


}

public  int partition(int[] array, int start, int end) {
    int pivot = array[end];
    int i = start - 1;

    for(int j = start; j< end;j++){
        if(array[j] < pivot){
            i++;
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        }
    }
    // deal with the end element
    i++;
    int temp = array[i];
    array[i] = array[end];
    array[end] = temp;

    return i;
}
```

## Hashtable

A data structure that stores unique keys to values. Each key/value pair is known
as an Entry.

- FAST insertion, look up, deletion of key/value Pairs
- Not ideal for small data sets, great with large data sets

1. hashing = takes a key and computers an integer (formula will vary based on key&
   data type), In a Hashtable, we use the hash % capacity to calculate an index
   number `key.hashCode() % capcity = index`

2. bucket = an indexed storage location for one or more Entries can store multiple
   Entries in case of a collision(linked similarly a LinkedList)

3. collision = hash function generates the same index for more than one key
4. less collisions = more efficiency

- Runtime complexity: Best case O(1), Worst case O(n)

## Undirected Graph

![Undirected Graph](https://s2.loli.net/2023/08/25/5d9zXJHtYhaOA2K.png)

![Directed Graph](https://s2.loli.net/2023/08/25/vSX1f2YUd5LyA4q.png)

![Graph Store](https://s2.loli.net/2023/08/25/vRt1e9U6a2XcCMG.png)

### Adjacency Matrix

![Adjacency Matrix](https://s2.loli.net/2023/08/25/p8lT9tCXkQMruPI.png)

A 2D array to store 1's/0's to represent edges, # of rows = # of unique nodes; # of
columns = # of unique nodes

- runtime complexity to check an Edge: O(1)
- space complexity: O(v^2)

![Example](https://s2.loli.net/2023/08/25/ZHGxIPr5aLRqlMy.png)

```java
public class Node {
    char data;
    Node(char data){
        this.data = data;
    }
}

public class Graph{
    ArrayList<Node> nodes;
    int[][] matrix;

    Graph(int size){
        nodes = new ArrayList<>();
        matrix = new int[size][size];
    }

    public void addNode(Node node){
        nodes.add(node);
    }
    public void addEdge(int src, int dst){
        matrix[src][dst] = 1;

    }
    public boolean checkEdge(int src, int dst){
        return matrix[src][dst] == 1;
    }

    public void print(){
        System.out.print("  ");
        for(Node node: nodes){
            System.out.print(node.data+ " ");
        }
        System.out.println();
        for(int i =0;i<matrix.length;i++){
            System.out.print(nodes.get(i).data + " ");
            for (int j=0; j<matrix[i].length;j++){
                System.out.print(matrix[i][j] +" ");
            }
            System.out.println();
        }

    }
}

public class Main {
    public static void main(String[] arg){
        Graph graph = new Graph(5);

        graph.addNode(new Node('A'));
        graph.addNode(new Node('B'));
        graph.addNode(new Node('C'));
        graph.addNode(new Node('D'));
        graph.addNode(new Node('E'));

        graph.addEdge(0,1);
        graph.addEdge(1,2);
        graph.addEdge(1,4);
        graph.addEdge(2,3);
        graph.addEdge(2,4);
        graph.addEdge(4,2);
        graph.addEdge(4,0);

        graph.print();

        System.out.println(graph.checkEdge(1,4));

    }
}
```

### Adjacency List

![Adjacency List](https://s2.loli.net/2023/08/25/kzqxiIAr7Z6nRmT.png)

An array/arraylist of linkedlists. Each LinkedList has a unique node at the head.
All adjacent neighbors to that node are add to that node's linkedlist.

- runtime complexity to check an Edge: O(v)
- space complexity: O(v+e)

```java
public class Node {
    char data;
    Node(char data){
        this.data = data;
    }
}

public class Graph{
    ArrayList<LinkedList<Node>> alist;

    Graph() {
        alist = new ArrayList<>();
    }

    public void addNode(Node node) {
        LinkedList<Node> currentList = new LinkedList<Node>();
        currentList.add(node);
        alist.add(currentList);
    }

    public void addEdge(int src, int dst) {
        LinkedList<Node> currentList = alist.get(src);
        Node dstNode = alist.get(dst).get(0);
        currentList.add(dstNode);
    }

    public boolean checkEdge(int src, int dst) {
        LinkedList<Node> currentList = alist.get(src);
        Node dstNode = alist.get(dst).get(0);
        for (Node node : currentList) {
            if (node == dstNode) {
                return true;
            }
        }
        return false;
    }

    public void print() {
        for (LinkedList<Node> currentList : alist) {
            for (Node node : currentList) {
                System.out.print(node.data + "->");
            }
            System.out.println();
        }
    }
}

public class Main {
    public static void main(String[] arg){
        Graph graph = new Graph(5);

        graph.addNode(new Node('A'));
        graph.addNode(new Node('B'));
        graph.addNode(new Node('C'));
        graph.addNode(new Node('D'));
        graph.addNode(new Node('E'));

        graph.addEdge(0,1);
        graph.addEdge(1,2);
        graph.addEdge(1,4);
        graph.addEdge(2,3);
        graph.addEdge(2,4);
        graph.addEdge(4,2);
        graph.addEdge(4,0);

        graph.print();

        System.out.println(graph.checkEdge(1,4));

    }
}

```

## Depth First Search

<video src="https://drive.google.com/file/d/1UwEZuGeYMBS4ohQOkLFdA_7UqVu1Z0DF/view" controls="controls">
</video>

DFS = a search algorithm for traversing a tree or graph data structure.

1. Pick a route
2. Keep going until you reach a dead end,or a previously visited node
3. Backrtrack to last node that has unvisited adjacent neighbors

![Example](https://s2.loli.net/2023/08/26/sxnNCBMQOJDzp9d.png)

```java
public class Node {
    char data;
    boolean visited;
    Node(char data){
        this.data = data;
    }
}

public class Graph {
    ArrayList<Node> nodes;
    int[][] matrix;

    Graph(int size){
        nodes = new ArrayList<>();
        matrix = new int[size][size];
    }

    public void addNode(Node node){
        nodes.add(node);
    }
    public void addEdge(int src, int dst){
        matrix[src][dst] = 1;

    }
    public boolean checkEdge(int src, int dst){
        return matrix[src][dst] == 1;
    }

    public void print(){
        System.out.print("  ");
        for(Node node: nodes){
            System.out.print(node.data+ " ");
        }
        System.out.println();
        for(int i =0;i<matrix.length;i++){
            System.out.print(nodes.get(i).data + " ");
            for (int j=0; j<matrix[i].length;j++){
                System.out.print(matrix[i][j] +" ");
            }
            System.out.println();
        }

    }

    public void depthFirstSearch(int src){
        boolean[] visited = new boolean[matrix.length];
        dFSHelper(src,visited);
    }

    private void dFSHelper(int src, boolean[] visited) {
        if(visited[src]) {
            return;
        }else{
            visited[src] = true;
            System.out.println(nodes.get(src).data + " = visited");
        }
        for (int i = 0; i < matrix[src].length; i++) {
            if(matrix[src][i] == 1){
                dFSHelper(i,visited);
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Graph graph = new Graph(5);

        graph.addNode(new Node('A'));
        graph.addNode(new Node('B'));
        graph.addNode(new Node('C'));
        graph.addNode(new Node('D'));
        graph.addNode(new Node('E'));

        graph.addEdge(0,1);
        graph.addEdge(1,2);
        graph.addEdge(1,4);
        graph.addEdge(2,3);
        graph.addEdge(2,4);
        graph.addEdge(4,2);
        graph.addEdge(4,0);

        graph.print();

        System.out.println(graph.checkEdge(1,2));
        graph.depthFirstSearch(3);

    }
}
```

## Breadth First Search

<video src="https://drive.google.com/file/d/1EEOWh4jrWgxm_Oqt9j6QfH8MmdmWmEFS/view" controls="controls">
</video>

BFS = a search algorithm for traversing a tree or graph data structure. This is
done one "level" at a time, rather than one "branch" at a time.

```java
public class Node {
    char data;
    boolean visited;
    Node(char data){
        this.data = data;
    }
}

public class Graph {
    ArrayList<Node> nodes;
    int[][] matrix;

    Graph(int size){
        nodes = new ArrayList<>();
        matrix = new int[size][size];
    }

    public void addNode(Node node){
        nodes.add(node);
    }
    public void addEdge(int src, int dst){
        matrix[src][dst] = 1;

    }
    public boolean checkEdge(int src, int dst){
        return matrix[src][dst] == 1;
    }

    public void print(){
        System.out.print("  ");
        for(Node node: nodes){
            System.out.print(node.data+ " ");
        }
        System.out.println();
        for(int i =0;i<matrix.length;i++){
            System.out.print(nodes.get(i).data + " ");
            for (int j=0; j<matrix[i].length;j++){
                System.out.print(matrix[i][j] +" ");
            }
            System.out.println();
        }

    }

    public void breadthFirstSearch(int src){
        Queue<Integer> queue = new LinkedList<>();
        boolean[] visited = new boolean[matrix.length];

        queue.offer(src);
        visited[src] = true;
        while(!queue.isEmpty()){
            src = queue.poll();
            System.out.println(nodes.get(src).data + " =  visited");

            for (int i = 0; i < matrix[src].length; i++) {
                if(matrix[src][i] == 1 && !visited[i]){
                    queue.offer(i);
                    visited[i] = true;
                }

            }
        }

    }
}

public class Main {
    public static void main(String[] args) {
        Graph graph = new Graph(5);

        graph.addNode(new Node('A'));
        graph.addNode(new Node('B'));
        graph.addNode(new Node('C'));
        graph.addNode(new Node('D'));
        graph.addNode(new Node('E'));

        graph.addEdge(0,1);
        graph.addEdge(1,2);
        graph.addEdge(1,4);
        graph.addEdge(2,3);
        graph.addEdge(2,4);
        graph.addEdge(4,2);
        graph.addEdge(4,0);

        graph.print();

        System.out.println(graph.checkEdge(1,2));
        graph.breadthFirstSearch(2);

    }
}

```

Breadth FS = Traverse a graph level by level, Utilizes a Queue, Better if destination
is on average close to start, Siblings are visited before children

Depth FS = Traverse a graph branch by branch, Utilizes a Stack, Better if destination
is on average far from the start. children are visited before siblings, More popular
for games/puzzles

## Tree

<video src="https://drive.google.com/file/d/1KwKmHsTyRvb75fl-6weVhI_ueY8dVCM9/view" controls="controls">
</video>

tree = a non-linear data structure where nodes are organized in a hierarchy.

![tree](https://s2.loli.net/2023/08/26/N4qJUiYAVCQpIa6.png)
