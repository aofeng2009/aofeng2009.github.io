---
layout: post
title: Tensorflow 常用函数介绍
tags: [technology, Machine Learning]
description: >
  本文是笔者学习过程中的笔记。刚学，会有纰漏和误解，只供个人翻阅备忘。
---
~~~js
tf.random_uniform(shape, minval=0, maxval=None, dtype, seed, name=None)
/*
随机的产生一组值，第一个参数输入矩阵，二三参数代表随机产生数值的范
围，满足[minval, maxval)逻辑，即包含最小值，不包括最大值。
例如浮点数,那么随机值的范围就是[0,1)。如果是int，maxval应该明确给出。
dtype指定数值类型包括float32, float64, int32, or int64.
seed指定随机数种子,name指操作的名字（可选）
*/

tf.reduce_mean(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None)

/*
求指定axis维度的平均值 官方的例子
# 'x' is [[1., 1.]
#         [2., 2.]]
tf.reduce_mean(x) ==> 1.5
tf.reduce_mean(x, 0) ==> [1.5, 1.5]
tf.reduce_mean(x, 1) ==> [1.,  2.]
axis=0的时候取的是列的维度；axis=1的时候取的是行的维度，
默认axis=None，即所有维度进行平均值计算。
*/
~~~
