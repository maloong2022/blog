---
author: Maloong
pubDatetime: 2023-03-12T15:22:00Z
title: Algorithm About Array
postSlug: algo-linear-array
featured: false
draft: false
tags:
  - algo
  - linear
  - array
ogImage: ""
description: It's about the data struct of linear -- array and the Algorithm on it.
---

It's about the data struct of linear -- array and the Algorithm on it.

## Table of contents

## 数组

### 性质

基本特点：支持随机访问
关键：索引与寻址
内存形式：一段连续的存储空间
查询快插入删除慢，需要移动元素(除非在末尾删除和插入)。

### 时间复杂度

| 操作                | 时间复杂度 | 备注         |
| ------------------- | ---------- | ------------ |
| Lookup              | O(1)       |              |
| Insert              | O(n)       | 元素需要移动 |
| Delete              | O(n)       | 元素需要移动 |
| Append(push back)   | O(1)       |              |
| Prepend(push front) | O(n)       |              |

## 实战

26. Remove Duplicates from Sorted Array

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the first part of the array nums. More formally, if there are k elements after removing the duplicates, then the first k elements of nums should hold the final result. It does not matter what you leave beyond the first k elements.

Return k after placing the final result in the first k slots of nums.

Do not allocate extra space for another array. You must do this by modifying the input array in-place with O(1) extra memory.

Custom Judge:

The judge will test your solution with the following code:

```
int[] nums = [...]; // Input array
int[] expectedNums = [...]; // The expected answer with correct length

int k = removeDuplicates(nums); // Calls your implementation

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```

If all assertions pass, then your solution will be accepted.

```java
class Solution {
    public int removeDuplicates(int[] nums) {
      int n = 0;
      for (int i=0; i<nums.length; i++) {
        if (i==0 ||  (nums[i] != nums[i-1])) {
          nums[n] = nums[i];
          n++;
        }
      }
      return n;
    }
}
```

283. Move Zeroes

Given an integer array nums, move all 0's to the end of it while maintaining the relative order of the non-zero elements.

Note that you must do this in-place without making a copy of the array.

Example 1:

```
Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

Example 2:

```
Input: nums = [0]
Output: [0]
```

Constraints:

1 <= nums.length <= 104

-231 <= nums[i] <= 231 - 1

Follow up: Could you minimize the total number of operations done?

```java
class Solution {
  public void moveZeroes(int[] nums) {
    int n = 0;
    for(int i=0;i<nums.length;i++){
      if(nums[i] != 0){
        nums[n] = nums[i];
        n++;
      }
    }
    while(n<nums.length){
      nums[n]=0;
      n++;
    }
  }
}
```

88. Merge Sorted array

You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.

Example 1:

```
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.
```

Example 2:

```
Input: nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
Explanation: The arrays we are merging are [1] and [].
The result of the merge is [1].
```

Example 3:

```
Input: nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
Explanation: The arrays we are merging are [] and [1].
The result of the merge is [1].
Note that because m = 0, there are no elements in nums1. The 0 is only there to ensure the merge result can fit in nums1.
```

Constraints:

nums1.length == m + n

nums2.length == n

0 <= m, n <= 200

1 <= m + n <= 200

-109 <= nums1[i], nums2[j] <= 109

Follow up: Can you come up with an algorithm that runs in O(m + n) time?

```java
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m-1,j = n-1;
    for (int k = (m+n)-1;k>=0;k--){
      if(j<0 || i>=0 && nums1[i] > nums2[j]){
        nums1[k] = nums1[i];
        i--;
       }else {
        nums1[k] = nums2[j];
        j--;
      }
    }
  }
}
```

## Fliter modle

apply for: The relative order of the elements should be kept the same.

```java
int n;
for(int i=0; i < arr.size(); i++){
    if (filter) {
        arr[n] = arr[i];
        n++;
    }
}
return n;
```

## Merge modle

```java
for (int k = (m+n)-1;k>=0;k--){
  // base on one array
  if(j<0 || i>=0 && nums1[i] > nums2[j]){
    nums1[k] = nums1[i];
    i--;
  }else{
    nums1[k] = nums2[j];
    j--;
  }
}
```

66. Plus One

You are given a large integer represented as an integer array digits, where each digits[i] is the ith digit of the integer. The digits are ordered from most significant to least significant in left-to-right order. The large integer does not contain any leading 0's.

Increment the large integer by one and return the resulting array of digits.

Example 1:

```
Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
Incrementing by one gives 123 + 1 = 124.
Thus, the result should be [1,2,4].
```

Example 2:

```
Input: digits = [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
Incrementing by one gives 4321 + 1 = 4322.
Thus, the result should be [4,3,2,2].
```

Example 3:

```
Input: digits = [9]
Output: [1,0]
Explanation: The array represents the integer 9.
Incrementing by one gives 9 + 1 = 10.
Thus, the result should be [1,0].
```

Constraints:

1 <= digits.length <= 100

0 <= digits[i] <= 9

digits does not contain any leading 0's.

```java
class Solution {
    public int[] plusOne(int[] digits) {
       int size = digits.length;
       for (int i = size-1;i>=0;i--){
          int temp = digits[i]+1;
          if(temp <= 9){
            digits[i] = temp;
            return digits;
          }else {
            digits[i] = 0;
        }
       }
       int[] newDigits = new int[size+1];
       newDigits[0] = 1;
       return newDigits;
    }
}
```

## resizable array

如何实现一个变长数组

- 支持索引和随机访问(连续内存空间)
- 分配多长的连续空间？
- 空间不够用了怎么办？
- 空间剩余很多如何回收？

变长数组的关键点

- 基础数组的特性：支持索引与随机访问，连续存储空间
- 空间的弹性管理

实现要点

- 维护实际长度 size 和容量 capacity 来管理
- 插入元素的时候，如果当前空间不够，重新申请 2 倍连续空间，拷贝到新空间
- 删除元素的时候，如果当前 size/capacity 比例不到 25%，释放空间

👀JDK： ArrayList

## Java 实现自定义动态数组

### 数组基础回顾

1、数组是一种常见的数据结构，用来存储同一类型值的集合

2、数组就是存储数据长度固定的容器，保证多个数据的数据类型要一致

3、数组是一种顺序存储的线性表，所有元素的内存地址是连续的

4、例如：new 一个 int 基本类型的数组 array

```java
int[] array = new int[]{11,22,33};
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcbfe1c525c546fea421d43b9b42ca6c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

5、数组的优势与劣势

- 数组具有很高的随机访问能力，通过数组下标就可以读取对应的值
- 数组在插入与删除元素时，会导致大量的元素移动
- 数组的长度是固定的，无法动态扩容，在实际开发中，我们更希望数组的容量是可以动态改变的

**总结——数组适用于读操作多，写操作少的场景**

### 动态数组的设计

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6301410643de4324bb8a059b29dfc942~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.image)

1. 定义接口

```java

public interface List<E> {
    // the element not found return flag
    int ELEMENT_NOT_FOUND = -1;

    /**
     * the number of element
     *
     * @return
     */
    int size();

    /**
     * the list is empty or not
     *
     * @return
     */
    boolean isEmpty();

    /**
     * set a element in the index position
     *
     * @param index
     * @param element
     * @return return the old element
     */
    E set(int index, E element);

    /**
     * get the element in index
     *
     * @param index
     * @return
     */
    E get(int index);

    /**
     * the list contain the element or not
     *
     * @param element
     * @return
     */
    boolean contains(E element);

    /**
     * get the index of element
     *
     * @param element
     * @return
     */
    int indexOf(E element);

    /**
     * add the element at the end of list
     *
     * @param element
     */
    void add(E element);

    /**
     * add the element at the index
     *
     * @param index
     * @param element
     */
    void add(int index, E element);

    /**
     * remove the element at the end index
     *
     * @return
     */
    E remove();

    /**
     * remove the element at the index
     *
     * @param index
     * @return
     */
    E remove(int index);

    /**
     * remove the figure out element
     *
     * @param element
     * @return
     */
    E remove(E element);

    /**
     * clear all the element
     */
    void clear();
}
```

2. 实现抽象类

```java

public abstract class AbstractList<E> implements List<E> {
    /**
     * the size of element in the list
     */
    protected int size;

    @Override
    public int size() {
        return size;
    }

    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    @Override
    public boolean contains(E element) {
        return indexOf(element) != ELEMENT_NOT_FOUND;
    }

    @Override
    public void add(E element) {
        add(size, element);
    }

    @Override
    public E remove() {
        return remove(size - 1);
    }

    /**
     * illegal index exception
     *
     * @param index
     */
    protected void outOfBounds(int index) {
        throw new IndexOutOfBoundsException("Index:" + index + ",Size:" + size);
    }

    /**
     * check the index
     *
     * @param index
     */
    protected void rangeCheck(int index) {
        if (index < 0 || index >= size) {
            outOfBounds(index);
        }
    }

    /**
     * check the index when add element to list
     *
     * @param index
     */
    protected void rangeCheckForAdd(int index) {
        // index > size, the add element can at the index of size
        if (index < 0 || index > size) {
            outOfBounds(index);
        }
    }
}
```

3. 实现动态数组

```java
package xyz.zerotone.array;

public class DynamicArray<E> extends AbstractList<E> {
    /**
     * the all element
     */
    private E[] elements;
    // the default size of array
    private static final int DEFAULT_CAPACITY = 10;

    // the capacity of the array
    private int capacity;


    public DynamicArray(int capacity) {
        this.capacity = Math.max(capacity, DEFAULT_CAPACITY);
        this.elements = (E[]) new Object[capacity];
    }

    public DynamicArray() {
        this(DEFAULT_CAPACITY);
    }

    @Override
    public E set(int index, E element) {
        rangeCheck(index);
        E old = elements[index];
        elements[index] = element;
        return old;
    }

    @Override
    public E get(int index) {
        rangeCheck(index);
        return elements[index];
    }

    @Override
    public int indexOf(E element) {
        // if element is null, check it avoid NPE
        if (element == null) {
            for (int i = 0; i < size; i++) {
                if (elements[i] == null) return i;
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (element.equals(elements[i])) return i;
            }
        }
        return ELEMENT_NOT_FOUND;
    }

    @Override
    public void add(int index, E element) {
        rangeCheckForAdd(index);
        ensureCapacity(size + 1);
        for (int i = size; i < index; i--) {
            elements[i] = elements[i - 1];
        }
        elements[index] = element;
        size++;
    }

    @Override
    public E remove(int index) {
        rangeCheck(index);
        E old = elements[index];
        for (int i = index + 1; i < size; i++) {
            elements[i - 1] = elements[i];
        }
        elements[--size] = null;
        trim();
        return old;
    }

    @Override
    public E remove(E element) {
        return remove(indexOf(element));
    }

    @Override
    public void clear() {
        if (elements != null) {
            elements = (E[]) new Object[DEFAULT_CAPACITY];
            this.capacity = DEFAULT_CAPACITY;
            size = 0;
        }
    }

    @Override
    public String toString() {
        StringBuilder str = new StringBuilder();
        str.append("capacity:").append(capacity).append(",size:").append(size).append(",[");
        for (int i = 0; i < size; i++) {
            if (i != 0) str.append(",");
            str.append(elements[i]);
        }
        str.append("]");
        return str.toString();
    }

    private void ensureCapacity(int capacity) {
        int oldCapacity = elements.length;
        if (oldCapacity >= capacity) return;
        int newCapacity = oldCapacity << 1;
        E[] newElements = (E[]) new Object[newCapacity];
        this.capacity = newCapacity;
        for (int i = 0; i < size; i++) {
            newElements[i] = elements[i];
        }
        elements = newElements;
        System.out.println("Trigger expend the capacity: from capacity:" + oldCapacity + ",to capacity:" + newCapacity);
    }

    private void trim() {
        int oldCapacity = elements.length;
        // if the capacity is the default capacity then not trim
        if (oldCapacity == DEFAULT_CAPACITY) return;

        if ((size * 100f / oldCapacity) < 25.0f) {
            int newCapacity = oldCapacity >> 1;
            E[] newElements = (E[]) new Object[newCapacity];
            this.capacity = newCapacity;
            for (int i = 0; i < size; i++) {
                newElements[i] = elements[i];
            }
            elements = newElements;
            System.out.println("Trigger trim from capactiy :" + oldCapacity + ",to capacity:" + newCapacity);
        }
    }

}
```
