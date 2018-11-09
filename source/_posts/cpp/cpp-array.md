---
title: c++数组相关
date: 2018-11-09 10:44:31
tags: c++
category: c++
---

**问题从获取数组的长度开始**

## 数组长度的获取
```cpp
int arr[] = {1, 2, 3, 4, 5};
cout << sizeof(arr) / sizeof(arr[0]) << endl; // 5
```
- 数组元素的类型是确定的，所以每个元素的大小都是一样的
- 所以数组长度 = 数组总大小 / 单个元素大小

## 于是写个函数获取数组长度
```cpp
int arrSize(int[] arr) {
    return sizeof(arr) / sizeof(arr[0]);
}

int arr[] = {1, 2, 3, 4, 5};
cout << arrSize(arr) << endl; // 2 ???
```
- 数组作为参数在函数间传递时，传递的只是第一个元素的地址
  ```cpp
  int arrSize(int[] arr) {
      cout << *arr << endl;
      return sizeof(arr) / sizeof(arr[0]);
  }
  
  int arr[] = {1, 2, 3, 4, 5};
  arrSize(arr) // 1
  ```

## 然后想起了引用
- 然后发现不能声明素组的引用
- 翻阅资料得到解释
  > 数组名arr只代表数组首元素的地址，本身不是一个占有存储空间的变量，所以不能建立数组的引用

## 这时想起了刚刚百度时看到的template
```cpp
template<typename T>
int arrSize(T& arr) {
    return sizeof(arr) / sizeof(arr[0]);
}

int arr[] = {1, 2, 3, 4, 5};
cout << arrSize(arr) << endl; // 5 ???
```
- `int arrSize(T& arr)` 使用函数模板可以获取数组的引用?
- 貌似只有这种解释 - - !

