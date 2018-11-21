# Neutu相关论文总结

## swc文件格式

包含七个参数 - **n T x y z R P**

- **n** is the number of the node
- **T** is the type of the node
  - 0 = undefined
  - 1 = soma 细胞核
  - 2 = axon 轴突
  - 3 = dendrite 树突
  - 4 = apical dendrite 顶端树突
  - 5 = fork point 分叉点
  - 6 = end point 终点
  - 7 = custom 自定义
- **x y z** are the coordinates of each node
- **R** is the radius of the node.
- **P** indicates the parent of the node or -1 to indicate an origin

For example:

```
1 1 14.566132 34.873772 7.857000 0.717830 -1
2 0 16.022520 33.760513 7.047000 0.463378 1
3 5 17.542000 32.604973 6.885001 0.638007 2
4 0 19.163984 32.022469 5.913000 0.602284 3
5 0 20.448090 30.822802 4.860000 0.436025 4
6 6 21.897903 28.881084 3.402000 0.471886 5
7 0 18.461960 30.289471 8.586000 0.447463 3
8 6 19.420759 28.730757 9.558000 0.496217 7
```

## Paper-1 Automated Reconstruction of Neuronal Morphology Based on Local Geometrical and Global Structural Models - 2011

### Introduction
#### 传统方法
- Local Tracing
   - 指定种子点，生成单支神经

- Global Skeletonization
   - 二值化复杂，计算成本高
   - Dijkstra，需要人为操作

#### 本文方法
先局部追踪，再使用最短路径优化

### Methods
#### Model-based Neurite Fiber Tracing in 3D
![1540985589567](/home/superme/Desktop/NeuTu_project/notes/resource/1540985589567.png)



$$
N(t,u)=C(t)+O(u,t)R(t)A
$$
* Parameters:
   * radius **r**
   * two Euler angles **φ** and **ψ**
   * the anisotropy calibration **a** (各向异性)

#### A 3D Cylinder Filter

$$
U(x,y,z)=(1-(x^2+y^2))e^{-(x^2+y^2)}
$$

![1540985805163](/home/superme/Desktop/NeuTu_project/notes/resource/1540985805163.png)

#### Tracing a Neurite Fiber

结合圆柱模型与“墨西哥帽子”，对神经进行匹配，根据“卷积”结果的分数对参数进行优化（共轭梯度下降）。匹配完成后，以h/2的长度移动模板进行下次匹配。

#### Tracing All Neurite Fibers
为了找到一个合适的初始模板种子点，有以下两个方法：
* 像素点亮度
* 根据几何特征Hessian matrix 

#### Reconstruction of Neuronal Morphology
当有大量分叉时，简单的像素距离不能实现，于是提出Geodesic distance:
$$
geo_g(x_0,x_1)=\min_{c\in\{all\ possible\ paths\}}\int_{c}g[I(c(t))]||dc(t)||
$$

||dc(t)||为空间直线距离，I(x)为图像亮度，g(x)为sigmoid函数：

$$
g(x)=\frac{1}{1+e^{\frac{x-\alpha}{\beta}}}
$$

之后发现，神经元交叉很近时存在问题crossover。采用Hungarian algorithm在最小生成树算法之前进行。
![1540991549590](/home/superme/Desktop/NeuTu_project/notes/resource/1540991549590.png)

对于噪声有很好的鲁棒性

### 亮点总结
* 将神经元模型化，用公式表达
* 通过优化公式中的参数，达到单支神经元的追踪
* 结合实际情况，针对组装过程中的难点设计合理方案

## Paper-2 neuTube 1.0: A New Design for Efficient Neuron Reconstruction Software Based on the SWC Format - 2015

### Introduction

当前软件的缺陷：
* FARSIGHT, Simple Neurite Tracer: 重建后，不提供神经元底层的细节编辑功能
* Neurolucida, Neuromantic, Neurostudio: 不支持3D模式
* Vaa3D: 3D中的功能在2D中不适用

### Materials and Methods
#### The SWC framework
**Q1**: 什么是redundancy与non-redundant（冗余度）?

定义结点n：
$$
n = (G(n), P(n), C(n), S(n))
$$

SWC的三层规则：
* 分别对于P，C，S的操作f(x)
* 某种集合运算F(x)，很复杂没看懂
* 基于形态的两种操作

加入新结点(x,y,z)到n表示为：
$$
f(\{n\}|(x, y, z))= \{ n, F_p(((x, y, z, r(n)), n_0, n_0, n_0)|n)\}
$$

#### Software implementation
* Interpolate 修改结点
* Set/Reset branch point
* Connect multiple nodes 根据最小生成树
* Remove turn
* Resolve crossover 重构交叉点

### Results

计算重建结果误差
$$
Error=\frac{T_d(F_p+F_n)+\sum^{M}_{m=1}d_m}{N}
$$

**Fp:** false positives	
**Fn:** false negatives	
**Td:** DIADEM metric 
**dm:** the distance between two nodes

![](/home/superme/Desktop/NeuTu_project/notes/resource/1540878221562.png)

### 亮点总结
* 要对当前研究有充分的了解，需查阅大量资料
* 建立修改结点的数学模型，给出修改过程的公式
* 对比软件优劣时，建立误差函数模型将数据量化


## Paper-3 Reconstructing Curvilinear Networks Using Path Classifiers and Integer Programming

### Introduction







