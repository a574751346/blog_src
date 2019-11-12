title: 泛型编程(generic programming)与模板(template)编程
author: Meredith Ma
tags:
  - C++
categories:
  - C++
date: 2019-11-11 21:57:00
---
### 泛型编程Generic Programming泛型编程Generic Programming
##### GP：将datas与methods分开放
```C++
//Data structures
template<class T, class Alloc=alloc>
class vector{...};
template<class T, class Alloc=alloc, size_t=0>
class deque{...};
//Algorithm
template<typename _RandomAccessIterator>
inline void
sort(_RandomAccessIterator __first,
	_RandomAccessIterator __last)
{...}
template<typename _RandomAccessIterator>
inline void
sort(_RandomAccessIterator __first,
	_RandomAccessIterator __last,
	_Compare __comp)
{...}
```
##### OOP：将datas与methods放在一起
```c++
template<class T, class Alloc=alloc>
class list
{
...
void sort();
}
```

##### 为什么list不可以用::sort()？
::sort()需要对象是random access container，list不符合。

### 模板Template
C++中template主要分为三类，类模板(class template)、函数模板(function template)、成员模板(member template)。
##### 类模板
保留class或struct的成员数据类别，使用的时候显示告诉template
类模板分为泛化和特化，特化又分为全特化和偏特化
偏特化又分为参数个数的偏特化和范围的特化
```c++
//泛化
template<class T>
class __type_traits{
	typedef __false_type has_trivial_default_constructor;
	...
}
//特化
template<>
class __type_traits<int>{
	typedef __true_type has_trivial_default_constructor;
	...
}
//参数个数偏特化
template<class T, class Alloc=alloc>
class vector{}
template<class Alloc>
class vector<bool, Alloc>
//参数范围偏特化
template<class Iterator>
struct iterator_traits{...}
template<class T>
struct iterator_traits<T*>{}
template<class T>
struct iterator_traits<const T*>{}
```
##### 函数模板
```c++
template <class T>
inline
const T& min(const T& a, const T&)
{
	return a < b ? a : b; //T类去决定T之间是如何比较的，即去重载operator<()
}

stone r1(2,3), r2(3,3), r3;
r3 = min(r1, r2);

class stone
{
public:
	bool operator<(const stone& rhs) const{
		return _weight < rhs.weight;
	}
private:
	int weight, _w, _h;
}
```
##### 成员模板
本文暂时不表