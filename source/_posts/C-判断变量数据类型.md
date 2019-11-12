title: C++ 判断变量数据类型
author: Meredith Ma
date: 2019-11-11 21:56:25
tags:
---
参考stackflow上一个回答，能够保存变量的constant，inference等特性
><font size=2><https://stackoverflow.com/questions/81870/is-it-possible-to-print-a-variables-type-in-standard-c></font>

```C++
#include<iostream>
#include<cctype>
#include<typeinfo>
#include<memory>
#include<cstdlib>
#ifndef _MSC_VER
#   include <cxxabi.h>
#endif
using namespace std;

template <class T>
string
type_name()
{
    typedef typename std::remove_reference<T>::type TR;
    std::unique_ptr<char, void(*)(void*)> own
        (
#ifndef _MSC_VER
            abi::__cxa_demangle(typeid(TR).name(), nullptr, nullptr, nullptr),
#else
            nullptr,
#endif // _MSC_VER
            std::free
        );
    string r = own!=nullptr?own.get():typeid(TR).name();
    if(std::is_const<TR>::value)
        r+=" const";
    if(std::is_volatile<TR>::value)
        r+=" volatile";
    if(std::is_lvalue_reference<T>::value)
        r+="&&";
    return r;
}
int main()
{
    const string str = "abc";
    for(auto c: str)
        cout<<type_name<decltype(c)>()>>endl;
}
```