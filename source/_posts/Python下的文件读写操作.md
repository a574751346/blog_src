title: Python下的文件读写操作
author: Meredith Ma
tags:
  - python
  - file operate
categories:
  - Python
date: 2018-07-20 16:04:00
---
读取一个文件分为三步
---
1.open一个文件返回一个fileobject文件对象	

2.调用fileObject对象的read(),readline(),readlines()方法读取文件内容	

3.调用fileObject的close()方法关闭打开的文件	

通常的方式是
>with open(filename, md) as f:
>>f.readlines()

文件打开方式的区别	
---
r，rb：【默认】只读，文件指针放在开头	
w，wb：只写，指针放在开头，若文件已存在则从开头覆盖；若不存在则创建新文件	
r+，rb+：读写，指针开头	
w+，wb+：读写，指针放在开头，若文件已存在则从开头覆盖；若不存在则创建新文件	
a，ab，a+，ab+：追加，若以存在从末尾开始写，若不存在则创建新文件	
读取内容的方法
---
1.read():一次读取整个文件，不适用于大文件	

2.readline():一次读取一行，占内存小，速度慢效率低	

3.readlines():一次性读取，将内容分析成一个行的列表，可以由for...in...处理
