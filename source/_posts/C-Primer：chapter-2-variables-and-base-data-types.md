title: C++ Primer：chapter 2 variables and base data types
author: Meredith Ma
date: 2019-11-11 21:56:00
tags:
---
列表初始化

定义一个int型变量并初始化0

int unit_sold = 0;
int unit_sold = {0);
int unit_sold(0);
int unit_sold{0}; //列表初始化
当列表初始化用于内置类型的变量，有一个重要特点：若初始值存在丢失信息的风险，则编译器将报错。

long double id= 3.1415926

int a{id};//错误，编译器报错

int a(id);//正确，丢失一定信息

默认初始化

内置类型：所处的定义位置决定

函数体外：0

函数体内：不被初始化（值未定义）（试图拷贝或访问引发错误）

非内置类型：由类决定

声明和定义

extern int i; //声明但不定义，用extern且不初始化

int i; //声明且定义

变量可声明多次但只可以定义一次

2.4 有毒的const

const对象一旦创建不可更改，故必须初始化

const int i = get_size() //运行时初始化

const int i = 1; //编译时初始化

const int i; //错误，未初始化

默认状态下，const对象仅在文件内有效（未include情况）

使用extern关键字，在一个文件里extern声明并定义变量，在其他要使用的文件extern声明一次即可使用。

//file.h

extern const int id = 1;

//fileuse.cpp

extern const int id;

extern也可用于非const的情况，其他变量文件之间的共享

const的引用（剧毒！）

引用一旦和一个对象绑定在一起，将一直在一起

普通引用和const引用区别：

普通引用的类型要和与之绑定的对象严格匹配，且引用只能绑定到对象身上，不能与某个字面值或计算式的结果绑定在一起。

const引用允许用任意表达式作为初始值，只要该表达式的结果能转换为引用类型即可。

原因？若类型不相同，会产生与引用类型相同的中间变量，将引用绑定此中间变量。若为常量的引用，不可改变其值，那可以使用引用去使用此值，此时有意义。若为非常量引用，即目的使改变原来的值，由于此时绑定的是中间变量，则无法更改，即无意义。c++将其归为非法行为。

const引用可能引用一个非const的对象

int i = 42; const int &ref = i; //ref绑定到i上，但不可通过ref来改变i的值

顶层const和底层const

顶层const是指 指针本身是常量

底层const是指 所指对象是常量

auto忽略顶层const，保留底层const

constexpr和常量表达式

值不会改变且在编译时就能得到计算结果的表达式

指针、常量和类型别名

若某个别名指代的是符合类型或常量，则需要注意

typedef char* pstring;

const pstring cstr = 0; //cstr是指向char的常量指针，即顶层指针,即 char* const pstring cstr = 0;

decltype类型指示符

当希望使用表达式的返回值来定义变量时，可使用

auto ret = val;

但当只希望使用返回值类型且不适用返回值的值时，可用decltype

decltype(val) ret = value_you_liked;  // :) and 保留顶层const以及引用

decltype((val)) ret; // ret == &val 引用必须初始化