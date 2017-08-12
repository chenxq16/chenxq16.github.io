---
layout: post
title:  Numpy基础
subtitle:   ""
author: "MakerChen"
# header-img: ""
date:   2017-08-11 23:26:00
tags:
    - python
---



### 简单介绍
NumPy（Numerical Python）是高性能科学计算和数据分析的基础包。
* ndarray，一个具有矢量算数运算和复杂广播能力的快速且节省空间的多维数组。
* 用于对整组数据进行快速运算的标准数学函数（无须编写循环）
* 用于读写磁盘数据的工具以及用于操作内存映射文件的工具
* 线性代数、随机数生成以及傅里叶变换功能
* 用于集成由C，C++，Fortran等语言编写的代码的工具

由于NumPy提供了一个简单易用的C API，因此很容易将数据传递给由低级语言编写的外部库，外部库也能以NumPy数组的形式将数据返回给Python。

### ndarray：一种多维数组对象
通用的同构数据多维容器，即所有元素必须是相同类型的。
每个数组都有一个shape（各维度大小的元组）和一个dtype（用于说明数据类型的对象）

#### 创建ndarray
* array函数，接受一切序列型的对象。例


```python
import numpy as np

data1 = [6,7.5,8,0,1]
arr1=np.array(data1)

arr1
```




    array([ 6. ,  7.5,  8. ,  0. ,  1. ])




```python
data2 = [[1,2,3,4],[5,6,7,8]]
arr2 = np.array(data2)

arr2,arr2.shape
```




    (array([[1, 2, 3, 4],
            [5, 6, 7, 8]]), (2, 4))



* zeros&ones函数，创建制定长度或形状全0或全1的数组


```python
np.zeros(10,dtype='int32')
```




    array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0], dtype=int32)




```python
np.ones((3,6),dtype='int32')
```




    array([[1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1]], dtype=int32)



* empty可以创建一个没有任何具体值的数组，其中的value都是未初始化的垃圾值


```python
np.empty((5,2,3))
```




    array([[[  0.00000000e+000,   0.00000000e+000,   7.41098469e-323],
            [  0.00000000e+000,   0.00000000e+000,   0.00000000e+000]],

           [[  0.00000000e+000,   0.00000000e+000,   0.00000000e+000],
            [  0.00000000e+000,   0.00000000e+000,   0.00000000e+000]],

           [[  0.00000000e+000,   1.27319747e-313,   1.34582706e-315],
            [  0.00000000e+000,   3.39519327e-313,   4.44659081e-323]],

           [[  2.54639495e-313,   6.42285340e-323,   3.39558181e-313],
            [  2.20059502e-314,   0.00000000e+000,   0.00000000e+000]],

           [[  2.12345285e-314,   1.87109396e-308,   2.12409004e-314],
            [  2.12408964e-314,   2.12408996e-314,   2.08600887e-308]]])



* arange是python内置函数range的数组版


```python
np.arange(10)
```




    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])



* eye、identity创建N*N单位矩阵


```python
np.eye(4)
```




    array([[ 1.,  0.,  0.,  0.],
           [ 0.,  1.,  0.,  0.],
           [ 0.,  0.,  1.,  0.],
           [ 0.,  0.,  0.,  1.]])



#### ndarray的数据类型
数值型dtype的命名方式相同：一个类型名（如float、int），后面跟一个用于表示各元素位长的数字。

| 类型        | 类型代码           | 说明  |
| ------------- |:-------------:| :-----:|
| int8(16,32,64),uint8(16,32,64)| i1(2,4,8),u1(2,4,8) | 有符号和无符号的8(16,32,64)位（1,2,3,4个字节）整型 |
| float16     | f2|   半精度浮点数 |
| float32 | f4或f      |    单精度浮点数，与c的float兼容 |
| float64 | f8或d      |    双精度浮点数，与c的double兼容和python的float对象 |
| float128 | f16或g      |    扩展进度浮点数 |
|complex64（128、256）|c8（16，32）|复数|
| bool | ? | 布尔类型|
| object | O | python对象类型|
| string\_ | S | 固定长度的字符串类型，如S10|
| unicode\_ | U | 固定长度的unicode类型，如U10|

可以通过astype方法转换其dtype。


```python
arr = np.array([1,2,3,4,5])

arr.dtype
```




    dtype('int64')




```python
float_arr = arr.astype(np.float64)

float_arr.dtype
```




    dtype('float64')



调用astype无论如何都会创建一个新的数组，即使dtype一样。

### 数组和标量之间的运算

大小相等的数组之间的任何算术运算都会将运算应用到元素级。矢量化运算。


```python
arr = np.array([[1,2,3],[4,5,6]],dtype='f4')

arr
```




    array([[ 1.,  2.,  3.],
           [ 4.,  5.,  6.]], dtype=float32)




```python
arr * arr
```




    array([[  1.,   4.,   9.],
           [ 16.,  25.,  36.]], dtype=float32)




```python
arr - arr
```




    array([[ 0.,  0.,  0.],
           [ 0.,  0.,  0.]], dtype=float32)



数组与标量的算术运算也将会那个标量值传播到各个元素：


```python
1 / arr
```




    array([[ 1.        ,  0.5       ,  0.33333334],
           [ 0.25      ,  0.2       ,  0.16666667]], dtype=float32)



### 基本的索引和切片


```python
arr = np.arange(10)

arr
```




    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])




```python
arr[5],arr[5:8]
```




    (5, array([5, 6, 7]))




```python
arr[5:8]=12

arr
```




    array([ 0,  1,  2,  3,  4, 12, 12, 12,  8,  9])




```python
arr2d = np.array([[1,2,3],[4,5,6],[7,8,9]])

arr2d
```




    array([[1, 2, 3],
           [4, 5, 6],
           [7, 8, 9]])




```python
arr2d[:2,1:]
```




    array([[2, 3],
           [5, 6]])



#### 布尔型索引


```python
names = np.array(['Bob','Joe','Will','Bob','Will','Joe','Joe'])

data=np.random.randn(7,4)

names,data
```




    (array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'],
           dtype='|S4'),
     array([[ 0.58431783, -0.32250172,  0.93220636, -0.69585169],
            [-0.47121541,  0.02595873, -0.75045035, -0.0552076 ],
            [ 0.80959893,  0.17534421,  0.44307633,  1.18982735],
            [ 0.10955761,  0.39152736, -1.44976446,  0.20109315],
            [ 0.58365514, -0.16300416, -0.28480392, -0.23690772],
            [-0.13318635, -1.38864722,  0.61943625, -0.19675401],
            [-0.47054207, -0.458264  ,  0.49366592,  0.46391352]]))




```python
names == 'Bob'
```




    array([ True, False, False,  True, False, False, False], dtype=bool)




```python
data[names == 'Bob']
```




    array([[ 0.58431783, -0.32250172,  0.93220636, -0.69585169],
           [ 0.10955761,  0.39152736, -1.44976446,  0.20109315]])




```python
data[names=='Bob',3]
```




    array([-0.69585169,  0.20109315])



* != 和 - 可以对条件进行否定
* 支持&，|布尔算术运算符。关键字and、or在布尔型数组中无效


```python
mask = (names == 'Bob') | (names == 'Will')

mask
```




    array([ True, False,  True,  True,  True, False, False], dtype=bool)




```python
data[mask]
```




    array([[ 0.58431783, -0.32250172,  0.93220636, -0.69585169],
           [ 0.80959893,  0.17534421,  0.44307633,  1.18982735],
           [ 0.10955761,  0.39152736, -1.44976446,  0.20109315],
           [ 0.58365514, -0.16300416, -0.28480392, -0.23690772]])




```python
data[data<0] = 0

data
```




    array([[ 0.58431783,  0.        ,  0.93220636,  0.        ],
           [ 0.        ,  0.02595873,  0.        ,  0.        ],
           [ 0.80959893,  0.17534421,  0.44307633,  1.18982735],
           [ 0.10955761,  0.39152736,  0.        ,  0.20109315],
           [ 0.58365514,  0.        ,  0.        ,  0.        ],
           [ 0.        ,  0.        ,  0.61943625,  0.        ],
           [ 0.        ,  0.        ,  0.49366592,  0.46391352]])



#### 花式索引
* 利用整数数组进行索引


```python
arr = np.empty((8,4))

for i in range(8):
    arr[i] = i

arr
```




    array([[ 0.,  0.,  0.,  0.],
           [ 1.,  1.,  1.,  1.],
           [ 2.,  2.,  2.,  2.],
           [ 3.,  3.,  3.,  3.],
           [ 4.,  4.,  4.,  4.],
           [ 5.,  5.,  5.,  5.],
           [ 6.,  6.,  6.,  6.],
           [ 7.,  7.,  7.,  7.]])




```python
arr[[4,3,0,6]]
```




    array([[ 4.,  4.,  4.,  4.],
           [ 3.,  3.,  3.,  3.],
           [ 0.,  0.,  0.,  0.],
           [ 6.,  6.,  6.,  6.]])



* 一次传入多个索引数组会有一点特别。他返回的是一个以为数组。


```python
arr = np.arange(32).reshape((8,4))
arr
```




    array([[ 0,  1,  2,  3],
           [ 4,  5,  6,  7],
           [ 8,  9, 10, 11],
           [12, 13, 14, 15],
           [16, 17, 18, 19],
           [20, 21, 22, 23],
           [24, 25, 26, 27],
           [28, 29, 30, 31]])




```python
arr[[1,5,7,2],[0,3,1,2]]
```




    array([ 4, 23, 29, 10])



选取矩阵的行列子集是矩形区域。


```python
arr[[1,5,7,2]][:,[0,3,1,2]]
```




    array([[ 4,  7,  5,  6],
           [20, 23, 21, 22],
           [28, 31, 29, 30],
           [ 8, 11,  9, 10]])




```python
arr[np.ix_([1,5,7,2],[0,3,1,2])]
```




    array([[ 4,  7,  5,  6],
           [20, 23, 21, 22],
           [28, 31, 29, 30],
           [ 8, 11,  9, 10]])



注意：花式索引根切片不一样，它总是将数据复制到新数组中。

#### 数组转置和轴对换

1. T属性
2. transpose
3. swapaxes

### 通用函数：快速的元素级数组函数
一种对ndarray中的数据执行元素级运算的函数

* 一元函数：abs、fabs、sqrt、square、exp、log、sign、ceil、floor、rint、modf、isnan、isfinite、cos、tan、arccos、logical_not
* 二元函数：add、subtract、multiply、divide、power、maximum、minimum、mod、copysign、greater、less、equal、logical_and



```python
%matplotlib inline

points = np.arange(-5,5,0.01)
xs,ys=np.meshgrid(points,points)

import matplotlib.pyplot as plt

z = np.sqrt(xs**2+ys**2)
plt.imshow(z,cmap=plt.cm.gray)
plt.colorbar()
plt.title('Image plot of $\sqrt{x^2+y^2}$ for a grid of values')
```




    <matplotlib.text.Text at 0x11d7162d0>




![png](/media/python/output_46_1.png)


#### 将条件逻辑表述为数组运算

numpy.where函数是三元表达式x if condation else y 的矢量化版本。


```python
xarr=np.array([1.1,1.2,1.3,1.4,1.5])
yarr=np.array([2.1,2.2,2.3,2.4,2.5])
cond=np.array([True,False,True,True,False])
np.where(cond,xarr,yarr)
```




    array([ 1.1,  2.2,  1.3,  1.4,  2.5])



#### 数学和统计方法

| 方法        | 说明           |
| ------------- |:-------------:|
| sum     | 对数组中全部或某轴向的元素求和 |
| mean     | 算术平均数      |
| std、var | 标准差、方差      |
| min、max     | 最小最大值 |
| argmin、argmax     | 最小最大的索引      |
| cumsum | 所有元素的累计和      |
| cumprod | 所有元素的累计积      |

对于布尔类型数组，True会被转为1，False转为0
any方法测试数组中是否存在一个或多个True，all则检查数组中所有值是否都为True

#### 排序
sort函数

#### 唯一化以及其他的集合逻辑
| 方法        | 说明           |
| ------------- |:-------------:|
| unique(x)     | 数组中的唯一元素，并返回有序结果 |
| intersect1d(x,y)     | x和y中的公共元素，并返回有序结果      |
| union1d(x,y) | 计算x和y的并集，并返回有序结果      |
| in1d(x,y)     | 得到一个表示“x的元素是否包含于y”的布尔型数组 |
| setdiffid(x,y)    | 集合的差，元素在x中且不在y中      |
| setor1d(x,y) | 集合的对称差，即存在与一个数组中但不同时存在于两个数组中的元素     |


### 线性代数

| 方法        | 说明           |
| ------------- |:-------------:|
| diag     | 以一维数组形式返回方针的对角线元素，或将一维数组转换为方阵 |
| dot     | 矩阵乘法      |
| trace | 计算对角线元素的和      |
| det     | 计算矩阵行列式 |
| eig    | 计算方阵的本征值和本征向量      |
| inv | 计算方针的逆     |
| pinv     | 计算矩阵的moore-Penrose伪逆 |
| qr    | 计算QR分解      |
| svd | 计算奇异值分解     |
| solve    |解线性方程组Ax=b，其中A为一个方阵      |
| svd | 计算Ax=b的最小二乘解     |

### 随机数生成

| 方法        | 说明           |
| ------------- |:-------------:|
| seed| 确定随机数生成器的种子 |
| permutation     | 返回一个序列的随机排列或返回一个随机排列的范围      |
| shuffle | 对一个序列就地随机排列      |
| rand     | 产生均匀分布的样本值 |
| randint    | 从给定的上下限范围内随机选取整数      |
| randn | 产生正态分布     |
| binomial     | 产生二项分布的样本值 |
| normal    | 产生正态分布的样本值      |
| beta | 产生beta分布的样本值     |
| chisquare    |产生卡方分布的样本值      |
| gamma | 产生Gamma分布的样本值     |
| uniform | 产生在  [0,1)中均匀分布的样本值     |


```python

```
