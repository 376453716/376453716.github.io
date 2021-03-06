＃＃ 什么是机器学习

> 设计和分析一些让计算机可以自动“学习”的算法，从数据中自动分析获得规律，并利用规律对未知数据进行预测。

问题：通过程序区分图片中的水果是橘子还是苹果？
- 这需要大量的规则来完成分析，例如，将图片中的像素相加，比较绿色像素的数量，然后判断是哪种水果。
- 这种方式只能适用于简单的情况，但是真实的世界充满里不确定因素，你写的各种规则都可能被例外的情况打破，比如图片为黑白照片，或者图中是其他水果。需要成千上万种规则才可能满足各种不同的场景。如果换个新的问题，又需要重新去实现这些规则。
- 为了解决这些问题，我们需要一种算法，可以自动生成规则，这样就不必一条条重复去工作。

分类器 Classifier
可以将其当作一个方法，接受数据输入，然后给他们分配标签作为输出。
用来实现自动分类器的技术为监督学习。

监督学习：
1. 收集训练数据
 - 将描述水果的属性作为输入，根据重量，质感等特性对收集到的数据进行预测。
 - 特征 (Features)：通过观察不同样本，测量得到属性的值既。表格的每一行都是训练数据的一个样本
 - 好的特征数据能更好的区分水果的种类

重量  | 质感      | 标签
-----|-----------|---
150g | 凹凸不平   | 橘子
170g | 凹凸不平   | 橘子
140g | 光滑的     | 苹果
130g | 光滑的     | 苹果  

2. 训练分类器
```python
import sklearn
features = [[150,'bumpy'],[170,'bumpy'],[140,'smooth'],[130,'smooth']]
lables=['orange','orange','apple','apple']

```