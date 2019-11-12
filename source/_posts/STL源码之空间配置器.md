title: STL源码之空间配置器
author: Meredith Ma
date: 2019-11-11 21:53:04
tags:
---
##<font face="隶书"> STL规范接口(allocator)</font>
<br/>
```
value_type /&nbsp;size_type /&nbsp;difference_type

pointer / const_pointer /reference / const_reference

rebind

allocator() / allocator(const allocator&amp;): default constructor / copy constructor

template <class U>allocator::allocator(const allocator<u>) //泛化的拷贝构造函数

~allocator()

pointer_address(reference x) const / const pointer address(const_reference x) const //返回对象地址, a.address(x)==x

pointer allocate(seize_type n, const void*=0)

void deallocate(pointer p, size_type n)

size_type max_size() const

void construct(pointer p, const T&amp; x)/destroy(pointer p)
```
<br/>
##<font face="隶书"> SGI空间配置器std：：alloc </font>
<br/>

###<font face="隶书" color='red'>sgi内存配置写法</font>

<font face="隶书">若要使用sgi的特殊空间配置器，写法为 </font>

	vector<int, std::alloc> iv;

<font face="隶书">sgi标准空间配置器std::allocator: 效率不佳，对new和delete做了一层简单包装</font>

###<font face="隶书" color='red'>sgi将配置划分为对象操作和内存操作</font>

<font face="隶书">sgi对内存配置及释放进行了细化，将对内存的操作和对对象的操作分开来操作</font>

	<stl::alloc.h> //内存分配和释放
		: : allocate() //内存空间配置
		: : deallocate() //内存空间释放
	<stl::construct.h> //对象构造和析构
		: : construct() //内存空间配置
		: : destroy() //内存空间释放

<p><u><img src="https://upload-images.jianshu.io/upload_images/3736230-3b3e69ae900b4f41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/967/format/webp" /></u></p>

###<font face="隶书" color='red'>对象的构造析构</font>

<font face="隶书">construct 只有一个版本，接受一个指针和一个初值，将初值付给指针指向的区域</font>

	construct(T1* p, const T2&amp; value): new (p) T1(value);

<font face="隶书">destroy有两个版本，第一个是将指针所指对象析构，调用对象的析构函数即可。第二个是接受first，last两个迭代器，将左闭右开区间对象析构。</font>

<font face="隶书">对于第二个版本，为了提升效率，避免不必要的析构操作，先判断其析构函数类型，即：</font>

	value_type() //获取迭代对象类型
	__type_traits //判断其析构函数
	if __true_type //pass
	else if __false_type //循环析构

###<font face="隶书" color='red'>内存的分配释放</font>

<font face="隶书" size='5'>
- 设计遵循以下原则：</font>
<font face="隶书">
<ul>
	<li>向system heap要求空间</li>
	<li>考虑多线程(multi-threads)状态</li>
	<li>考虑内存不足时应变措施</li>
	<li>考虑过多&ldquo;小型区块&rdquo;可能造成的内存碎片问题</li>
</ul>
</font>

<p><img src="https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2074971254,1170967547&amp;fm=15&amp;gp=0.jpg" /></p>

<br/><font face="隶书">
<strong><font size='5'>- new_handler机制：</font></strong><br/>
set_new_handler()允许指定一个函数，在new内存分配失败后被调用，声明与<new.h>中</new.h>
</font>

<pre>
1 namespace std
2 {
3     typedef void (*new_handler)();
4     new_handler set_new_handler(new handler p) throw();
5 }
</pre>
<font face="隶书">
<p>具体参考：博文[Effective_C++_条款四十九：了解new_handler的行为](https://www.cnblogs.com/jerry19880126/p/3722531.html)和 《Effective C++》2e条款7。<br/>另：若要在不同类里定制不同的new_handler机制，由于编译器要求set_new_handler是静态的，所以不能通过构造函数传入，只能将set_new_handler和operator new都写成静态的，同时定义一个静态的new_handler变量。</p>

<br/><font face="隶书" size=5 >- 为什么使用两级配置器？</font>

<font face="隶书" >当所需要分配的区块足够大时，直接使用第一级配置器。但当需要的区块比较小，数量有比较多的情况下，每次使用malloc申请的内存存在内存破碎的问题，且产生overhead的问题，故采用复杂的内存池memory pool来整理。所以是为了更高效的进行内存管理。</font>

<br/><font face="隶书" size=5 >- 第一级配置器</font>

<p><img alt="å¨è¿éæå¥å¾çæè¿°" src="http://www.pianshen.com/images/120/ac577e8ebbebdd5824adeaefedca2b80.png" /></p>
<font face="隶书">第一级配置器以malloc(),free(),realloc()等C函数来执行实际的内存配置、释放、重配置操作，并实现出类似C++ new_handler的机制。他不能直接使用C++的new_handler，是因为它并非使用::operator new来配置内存。<br/><br/>new handler机制是指可以要求再系统内存配置失败时调用指定的函数，即在抛出bad_alloc之前，先调用客户端指定的处理例程new_handler。<br/><br/>为什么SGI使用c中的malloc而不是c++中的operator new？<br/><br/>一是历史原因，二是c++并未提供相应的realloc()的内存配置操作<br/><br/>需要注意的是，第一级配置器allocate()和realloc()都是在调用malloc()和realloc()失败后，改调用oom_malloc()和oom_realloc()【即使用类似new_handler的机制】,之后后两者中都有内循环不断调用“内存不足处理例程”，直到满足任务，但若并未设置“内存不足处理程序”,那么就直接抛出__THROW_BAD_ALLOC丢出bac_alloc异常信息，或利用exit(1)终止程序。</font>
<br/><br/><font face="隶书" size=5>- 第二级配置器</font><br/>
```c++
//target:返回一个大小为n的对象，并可能加入大小为n的其他区块到free list
//调用chunk_alloc()缺省取得20个新节点
//if 只获得了一个节点：
//	直接返回客户
//else:
//	在chunk空间内建立free list
static void *refill(size_t n);
//配置一大块空间，可容纳nobjs个大小为size的区块
//若无法满足nobjs个，则会返回小于nobjs个
//if 内存池满足需求：
//	return；
//else if 不能完全满足需求：
//	返回剩余的这些 return；
//else: //此时bytes_left<size
//	将剩余的编入freelist中其他更小的区块链
//	malloc heap中的内存给内存池，malloc的大小为需求的两倍+常数（size*nobjs*2+C）
//	if mallo_heap 失败：
//		重新检视freelist，将比需求的size大的区块链中区块取一块放入内存池中，重新其调用自己 return；
//		若上行失败，调用第一级配置器，试图使用oom处理例程
//	调整内存池状态，修改nobjs return；
static char *chunk_alloc(size_t size, int &nobjs);
//if n>128 : 
//	调用第一级配置器
//else if freelist有可用区块: 
//	直接用
//else :
//	将n上调至8的倍数边界
//	refill()为freelist重新填充空间
static void* allocate(size_t n);

```
<font face="隶书">· refill(size_t n)的作用是<strong>补充freelist</strong>中大小为n的区块链，内部调用chunk_alloc(n,nobjs)来从内存池中获取nobjs个n大小的区块，然后调整freelist
<br/>
· chunk_alloc(n,nobjs)从内存中池返回区块，若内存池不够，<strong>补充内存池</strong>（heap，>n的freelist，第一级配置器）
</font>

<br/><br/><font face="隶书" size=5>- 内存基本处理工具</font><br/>
<font face="隶书">STL定义了5个全局函数，作用域未初始化空间上，前两个是construct()和destruct()，剩下三个uninitialized_copy(),uninitialized_fill(),uninitialized_fill_n()对应于高层次函数copy(),fill(),fill_n()这些STL算法</font>

<font face="隶书">· uninitialized_copy(InputIterator first, InputIterator last, ForwardIterator result)</font><br/>
<font face="隶书">construct(&*(result+(i-first)),i),使得内存的配置与对象的构造行为分离开来，使用copy constructor为输入来源每个位置在输出范围内产生响应对象。要求其具有“commit or rollback”，即要么构造出所有元素、要么不构造任何东西。</font>

<font face="隶书">· uninitialized_fill(ForwardIterator first, ForwardIterator last, const T& x)</font><br/>
<font face="隶书">construct(&*i,x),同上，若有任何一个发生异常，需析构之前已经构造的元素。</font>

<font face="隶书">· uninitialized_fill_n(ForwardIterator first, Size n, const T& x)</font><br/>
<font face="隶书">同上</font><br/><br/>
<font face="隶书">原理大致相同，即先将传入的参数使用value_type()萃取出value type，之后使用type_traits()萃取出POD性质，分别处理即可。</font>
<br/><br/>
<font face="隶书">关于malloc，参考[2](http://blog.codinglabs.org/articles/a-malloc-tutorial.html)



[1][Effective_C++_条款四十九：了解new_handler的行为](https://www.cnblogs.com/jerry19880126/p/3722531.html)<br/>
[2][如何实现一个malloc-张洋](http://blog.codinglabs.org/articles/a-malloc-tutorial.html)