---
title: 【补档】用 JavaScript 生成符合正态分布的随机样本
date: 2023-01-08 16:30:29
tags: JavaScript, 数学
categories: 技术
---
> 该博文为补档，原文作于 2022 年，现已遗失。由站主根据回忆补档并予以删改，或与原文略有出入。

在大多数的计算机语言中，随机数函数生成的都是均匀分布的随机数。然而在现实生活中，几乎所有的事物的属性分布都符合一种平均值近处密集，平均值远处稀疏的分布，也就是**正态分布**。那么，当我们要用随机生成模拟现实中某个事物的属性时（如随机生成 NPC 的身高），我们就需要一种方法，生成符合正态分布的随机变量。

关于正态分布，此处不再赘述，可以参考这篇文章：[数学基础 | 什么是正态分布？](https://zhuanlan.zhihu.com/p/537208406)

Javascript 中的 `Math.random()` 随机数函数生成的是均匀分布于 $[0, 1)$ 的随机变量，转化成正态分布变量有两个方法。

## Box-Muller 算法

### 算法概述
Box-Muller 算法可以将两个 $[0, 1]$ 上的均匀分布随机变量转化成服从 $N(0,1^2)$ 的标准正态分布随机变量，公式如：
$$X=\sqrt{-2\ln{u}}\cos{2\pi v}$$
$$Y=\sqrt{-2\ln{u}}\sin{2\pi v}$$
其中 $u,v$ 是 $(0,1]$ 上的两个均匀分布随机数。

### 证明
Box-Muller 算法本质上是生成二维正态分布样本点，因此它的入参和出参都是成对出现的。二维正态分布可以看作在两个维度上独立的两个正态分布，其概率密度函数可以写成两个一维正态分布的乘积。

设有相互独立的随机变量 $X,Y\sim N(0,1^2)$，其概率密度函数为
$$p(X)=\frac{1}{\sqrt{2\pi}}e^{-\frac{X^2}{2}},p(Y)=\frac{1}{\sqrt{2\pi}}e^{-\frac{Y^2}{2}}$$

由于 $X,Y$ 相互独立，所以它们的联合概率密度满足
$$p(X,Y) = p(X)p(Y) = \frac{1}{2\pi}e^{-\frac{X^2+Y^2}{2}}$$

将 $X,Y$ 变换为极坐标，即令 $X=R\cos\theta,Y=R\sin\theta$，则有
$$\iint_{-\infty}^{\infty}\frac{1}{2\pi}e^{-\frac{X^2+Y^2}{2}}\, {\rm d}X{\rm d}Y = \iint_{-\infty}^{\infty}\frac{1}{2\pi}e^{-\frac{R^2}{2}}\, R{\rm d}R{\rm d}\theta = 1$$

也即
$$\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}\frac{1}{2\pi}e^{-\frac{R^2}{2}}R{\rm d}R{\rm d}\theta = 1$$

角度分量 $\theta$ 是在 $[0,2\pi]$ 上均匀分布，这一点比较好理解。再看半径分量 $R$，设 $R$ 的分布函数为 $P_R$，则
$$P_R(R<t)=\int_{0}^{2\pi}\int_{0}^{t}\frac{1}{2\pi}e^{-\frac{R^2}{2}}R{\rm d}R{\rm d}\theta = 1-e^{-\frac{t^2}{2}}$$

令
$$F_R(t) = 1-e^{-\frac{t^2}{2}}$$

其反函数
$$R = F_R^{-1}(t) = \sqrt{-2\ln(1-t)}$$

也即当 $t$（也就是 $1-t$）服从 $[0,1]$ 上的均匀分布时，$R$ 的概率密度函数为 $F_R(t)$，因此可以选取两个服从 $[0,1]$ 上均匀分布的随机变量 $u,v$，使得
$$\theta=2\pi v, R=\sqrt{-2\ln u}$$

代入 $X=R\cos\theta,Y=R\sin\theta$ 即可得到原公式
$$X=\sqrt{-2\ln{u}}\cos{2\pi v}$$
$$Y=\sqrt{-2\ln{u}}\sin{2\pi v}$$

### 代码实现
依据公式直接模拟即可
```js
/**
 * 生成一对服从正态分布的随机数
 * @param {Number} mean 均值
 * @param {Number} dev 标准差
 * @return {*} 生成的随机数组
 */
function NormalDistbRandom(mean, stdDev){
	let u, v, std;
	// 转换成 (0, 1] 防止出现 ln(0)
	u = 1 - Math.random();
	v = 1 - Math.random();
	// 生成标准正态分布
	std[0] = Math.sqrt(-2 * (Math.log(u)/Math.log(Math.E))) * Math.cos(2 * Math.PI * v);
	std[1] = Math.sqrt(-2 * (Math.log(u)/Math.log(Math.E))) * Math.sin(2 * Math.PI * v);
	// 转换成所需的正态分布并返回
	return [std[0] * stdDev + mean, std[1] * stdDev + mean];
}
```