title: __type_traits是什么？
author: Meredith Ma
date: 2019-11-11 21:54:46
tags:
---
value_type(const iterator&)萃取迭代器的value type
```C++
template &lt;class iterator=""&gt;
inline typename iterator_traits&lt;iterator&gt;::value_type*
value_type(const Iterator&) {
    return static_cast&lt;typename iterator_traits=""&gt;::value_type*&gt;(0);
}
```
__type_traits是为了萃取类型(type)的特性，其中我们主要关注的是这个类型是否具备non-trivial(有意义) default/copy/assignment ctor or dtor，如果答案是否定的，即没有有意义的构造析构函数时，我们就可以使用更有效率的措施，采用内存方式(malloc, memcpy等)直接处理即可。

为了判断5个条件，内部需要定义一些typedefs，其值只能是__true_type or __false_type
```C++
template &lt;class type=""&gt;
struct __type_traits {
    typedef __false_type has_trivial_default_constructor;
    typedef __false_type has_trivial_copy_constructor;
    typedef __false_type has_trivial_assignment_constructor;
    typedef __false_type has_trivial_destructor;
    typedef __false_type is_POD_type;
};
```

即先对其定义为构造析构有意义的类型，之后对标量类型设计特化版本如：
```C++
template &lt;class t=""&gt;
struct __type_traits&lt;t*&gt; {
    typedef __true_type has_trivial_default_constructor;
    typedef __true_type has_trivial_copy_constructor;
    typedef __true_type has_trivial_assignment_constructor;
    typedef __true_type has_trivial_destructor;
    typedef __true_type is_POD_type;
};
```