---
title: 数学分析一
date: 2017-01-18 16:01:31
tags:
---
### 概述
本次分享为一些基础知识，介绍一些数学中广泛采用的术语与符号，以及几个基本的概念，为今后的学习做铺垫。
### 概念
1. 集合
  * 由确定的一些对象汇集的总体
2. 子集
  * 组成集合的这些对象称为集合的元素
  * x是集合E的元素记作:
  {% math %}x \in E{% endmath %}(读作: x属于E)
  * y不是集合E的元素记作:
  {% math %}y \notin E{% endmath %}(读作: y不属于E)
  <!-- more -->
3. 集合的关系
  * E的任何元素都是集合F的元素，E是F的子集，记作
  {% math %}E \subset F{% endmath %}(读作: E包含于F)
  * 两个集合相等记作 E = F
  * 一个特殊的集合，空集：不包含任何元素的集合{% math %}\phi{% endmath %}
4. 集合的运算
  * {% math %} E \bigcup F {% endmath %}  由E的所有元素与F的所有元素合在一起组成的集合（并集）
  * {% math %} E \bigcap F {% endmath %}  由E和F共同的元素组成的集合 （交集）
  * {% math %} E \setminus F{% endmath %}  由属于E但不属于F的元素组成的集合 （差集）
5. 区间
  * 开区间
  (a,b) 表示 a < x < b
  * 左开右闭区间
  (a,b] 表示 a < x ≤ b
  * 左闭右开区间
  [a,b) 表示 a ≤ x < b
  * 闭区间
  [a,b] 表示 a ≤ x ≤ b
6. 逻辑符号
  * α 和 β是两个判断，当α成立的时候，β也成立，我们说α 能推导出 β，记作
  {% math %} \alpha \Rightarrow \beta {% endmath %} 
  * 如果 {% math %} \alpha \Rightarrow \beta ,{% endmath %}  并且 {% math %} \beta \Rightarrow \alpha {% endmath %}  那 {% math %} \alpha {% endmath %}  与 {% math %} \beta{% endmath %} 等价, 记作
  {% math %} \alpha \Leftrightarrow  \beta {% endmath %} 
7. 函数
  * 变量y随着变量x的变化而变化
8. 映射
  * 对变量x的任何一个值，有变量y的唯一确定的值与之对应
  * 如果D中的任何一个元素，有集合E中的唯一一个元素与之对应，那么f就是从D到E的一个映射，记为  
{% math %}
\begin{aligned}
f : D \rightarrow  E 
\end{aligned}
{% endmath %} 
  * 元素之间的对应关系如下表示 f : x ↦ {% math %}{x}^{2} {% endmath %} 
假设 {% math %} f : D \rightarrow E {% endmath %} 是一个映射，{% math %}g : G \rightarrow H{% endmath %}也是一个映射，如果{% math %}f(D)\subset G, {% endmath %}
那么从{% math %}\xi \in D{% endmath %}开始，相继经过f和g的作用，得到{% math %} g(f(\xi)) {% endmath %}这样的关系
{% math %}\xi \mapsto g(f(\xi)){% endmath %}
对于映射 {% math %}f\cdot g {% endmath %} 和 {% math %}g\cdot f{% endmath %} 不一定都有意义，有意义也不一定相同
比如 {% math %}f(x)={x}^{2},g(x)=sinx {% endmath %} 
{% math %}
\begin{aligned}
g\cdot f(x) = sin{x}^{2},  f\cdot g(x) = {(sinx)}^{2}
\end{aligned}
{% endmath %} 

### 计算
1. 计算下面的式子{% math %}\sum_{k=1}^{n}({k}^{p}-{(k-1)^{p}}){% endmath %} 
{% math %}
\begin{aligned}
=({1}^{p} - {0}^{p})+({2}^{p}-{1}^{p})+ \cdot \cdot \cdot + ({n}^{p}-{(n-1)}^{p}) \\
\sum_{k=1}^{n}({k}^{p}-{(k-1)^{p}}) = {n}^{p}
\end{aligned}
{% endmath %}

2. 计算1+2+3...+n
由恒等式{% math %}{k}^{2}-{(k-1)}^{2} = 2k-1{% endmath %}
取k=1,2,3,4...n,相加得到
{% math %}
\begin{aligned}
\sum_{k=1}^{n}({k}^{2}-{(k-1)}^{2}) &=2\sum_{k=1}^{n}k-\sum_{k=1}^n1 \\
{n}^{2} &= 2\sum_{k=1}^{n}k-n,\\
\sum_{k=1}^{n}k &= \frac{1}{2}{n}^{2}+\frac{1}{2}n \\
& = \frac{n(n+1)}{2}
\end{aligned}
{% endmath %}

3. 计算不规则图形的面积
假设坐标轴上有一条曲线，计算这条曲线与x轴y轴组成得面积，假设这个图形由y={% math %}{x}^{p}{% endmath %}, OX轴和直线x=b围城，求这个图形得面积
我们把[0,b]分成n等份，那第k个等分时
{% math %}
\begin{aligned}
[\frac{k-1}{n}b, \frac{k}{n}]
\frac{k-1}{n}b\leq x \leq \frac{k}{n}b,  0\leq y \leq {x}^{p}, k=1,2,3...,n
\end{aligned}
{% endmath %}
那每一个条形得面积{% math %}{S}_{k}{% endmath %}满足 
{% math %}{(\frac{k-1}{n}b)}^{p}\cdot \frac{b}{n}\leq{S}_{k}\leq{(\frac{k}{n}b)}^{p}\cdot\frac{b}{n}{% endmath %}
那么曲线的面积S满足
{% math %}\sum_{k=1}^{n}{(\frac{k-1}{n}b)}^{p}\cdot \frac{b}{n}\leq{S}_{k}\leq\sum_{k=1}^{n}{(\frac{k}{n}b)}^{p}\cdot\frac{b}{n} {% endmath %}
当n无限大时，近似值的精确度就越高，那么两个数的极限可以看作面积S 
求的它们共同的极限为 {% math %}\frac{b^{p+1}}{p+1} {% endmath %}
得到 {% math %}S=\frac{b^{p+1}}{p+1}{% endmath %}
