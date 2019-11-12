title: k-近邻算法小结笔记
author: Meredith Ma
tags:
  - 机器学习实战
categories:
  - 机器学习
date: 2018-07-23 12:45:00
---
优点：精度高、对异常值不敏感、无数据输入假定	
缺点：计算复杂度高、空间复杂度高	
适用数据范围：数值型和标称型	
一般流程
-----
(1)收集数据：可使用任何方法	
(2)准备数据：距离计算所需要的数值，最好是结构化的数据格式	
(3)分析数据：可以使用任何方法	
(4)训练算法：此步骤不适用与k-近邻算法	
(5)测试算法：计算错误率	
(6)使用算法：首先需要输入样本数据和结构化的输出结果，然后运行k-近邻算法判定输入数据据分别属于哪个分类，最后应用对于计算出的分类执行后续的处理	
算法伪代码
---
对未知类别属性的数据集中的每个点一次执行以下操作：	
(1)计算已知类别数据集中的点与当前点之间的距离	
(2)按照距离递增次序排序	
(3)选取与当前距离最小的k个点	
(4)确定前k个点所在类别的出现频率	
(5)返回前k个点出现频率最高的类别作为当前点的预测分类	
Python实现程序
---
```python
def classify0(inX, dataSet, labels, k):
	dataSetSize = dataSet.shape[0]	
	diffMat = tile(inX, (dataSetSize, 1)) - dataSet	
	sqDiffMat = diffMat ** 2	
	sqDistances = sqDiffMat.sum(axis=1)	
	distances = sqDistances ** 0.5	
	sortedDistIndices = distances.argsort()	
	classCount = {}	
	for i in range(k):	
		voteIlabel = labels[sortedDistIndices[i]]	
		classCount[voteIlabel] = classCount.get(voteIlabel,0) + 1	
	sortedClassCount = sorted(classCount.iteritems(),	
	key=operator.itemgetter(1), reverse=True)	
	return sortedClassCount[0][0]

```