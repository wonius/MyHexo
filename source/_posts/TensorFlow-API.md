---
title: TensorFlow-API
copyright: true
date: 2018-05-11 15:36:01
tags: TensorFlow
categories: TensorFlow
keywords: TensorFlow
description: TensorFlow常用API
---

## tf.constant

**根据value的值，生成一个shape维度的张量常量**

### Define

```python
constant(value, dtype=None, shape=None, name='Const', verify_shape=False)
```

### Parameter
| 参数名          | 必须   | 类型           | 说明                                       |
| ------------ | ---- | ------------ | ---------------------------------------- |
| value        | Y    | 常量、list      | 张量的值                                     |
| dtype        | N    | dtype        | 张量的元素类型                                  |
| shape        | N    | 1维整型张量、array | 张量的维度                                    |
| name         | N    | string       | 张量的名称                                    |
| verify_shape | N    | boolean      | 检测shape是否和value的shape一致。<br />若值为False，当不一致时，会用最后一个元素将shape补全 |
### Demo

```python
import tensorflow as tf
import numpy as np

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: constant.py
@time: 2018/5/11 10:00
"""

a = tf.constant([1,2,3,4,5,6], shape=[2,3])
b = tf.constant(-1, shape=[3,2])
c = tf.matmul(a,b)

e = tf.constant(np.arange(1,13,dtype=np.int32), shape=[2,2,3])
f = tf.constant(np.arange(13,25, dtype=np.int32), shape=[2,3,2])
g = tf.matmul(e,f)

with tf.Session() as sess:
    print ("-----------------")
    print (a.get_shape())
    print (sess.run(a))
    print ("-----------------")
    print (b.get_shape())
    print (sess.run(b))
    print ("-----------------")
    print (c.get_shape())
    print (sess.run(c))
    print ("-----------------")
    print (e.get_shape())
    print (sess.run(e))
    print ("-----------------")
    print (f.get_shape())
    print (sess.run(f))
    print ("-----------------")
    print (g.get_shape())
    print (sess.run(g))
    
# 执行结果
# -----------------
# (2, 3)
# [[1 2 3]
#  [4 5 6]]
# -----------------
# (3, 2)
# [[-1 -1]
#  [-1 -1]
#  [-1 -1]]
# -----------------
# (2, 2)
# [[ -6  -6]
#  [-15 -15]]
# -----------------
# (2, 2, 3)
# [[[ 1  2  3]
#   [ 4  5  6]]
#
#  [[ 7  8  9]
#   [10 11 12]]]
# -----------------
# (2, 3, 2)
# [[[13 14]
#   [15 16]
#   [17 18]]
#
#  [[19 20]
#   [21 22]
#   [23 24]]]
# -----------------
# (2, 2, 2)
# [[[ 94 100]
#   [229 244]]
#
#  [[508 532]
#   [697 730]]]
```

## tf.placeholder

**一种占位符，在执行时填入具体值**

###Define

```python
placeholder(dtype, shape=None, name=None)
```

### Parameter

| 参数名   | 必选   | 类型           | 说明       |
| ----- | ---- | ------------ | -------- |
| dtype | Y    | dtype        | 占位符的数据类型 |
| shape | N    | 1维整型张量、array | 占位符的维度   |
| name  | N    | string       | 占位符的名称   |

### Demo

```python
import tensorflow as tf
import numpy as np

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: placeholder.py
@time: 2018/5/11 10:57
"""

x = tf.placeholder(tf.float32, [None, 3])
y = tf.matmul(x, x)
with tf.Session() as sess:
    rand_array = np.random.rand(3,3)
    print (sess.run(y, feed_dict={x:rand_array}))

# 执行结果
# [[ 0.88505751  0.79640609  0.46087515]
#  [ 0.36539319  0.41709256  0.16832483]
#  [ 0.75206953  0.90277183  0.37584797]]
```



## tf.nn.conv2d

**卷积运算**

### Define

```python
conv2d(input, filter, strides, padding, 
		use_cudnn_on_gpu=True, data_format='NHWC', name=None)
```

### Parameter

| 参数名              | 必选   | 类型      | 说明                                       |
| ---------------- | ---- | ------- | ---------------------------------------- |
| input            | Y    | tensor  | 4维的tensor，即[batch, in_height, in_width, in_channels]<br />(若input是图像，即[训练时一个batch的图片数量，图片高度，图片宽度，图片的通道数])<br />类型需为float32、float64 |
| filter           | Y    | tensor  | 4维的tensor，即[filter_height, filter_width, in_channels, out_channels]<br />(若input是图像，即[卷积核的高度，卷积核的宽度，图像通道数，卷积核个数])<br />filter的in_channels必须与input的in_channels相等 |
| strides          | Y    | list    | 长度为4的list，卷积时在input上每一维的步长，一般strides[0]=strides[3]=1 |
| padding          | Y    | string  | 卷积方式，只能为"SAME"、"VALID"其中之一。SAME：补全方式；VALID：丢弃方式 |
| use_cudnn_on_gpu | N    | boolean | 是否使用cudnn加速，默认为true                      |
| data_format      | N    | string  | 只能为"NHWC"、"NCHW"，默认"NHWC"                |
| Name             | N    | string  | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: conv2d.py
@time: 2018/5/9 20:48
"""

a = tf.constant([1,1,1,0,0,0,1,1,1,0,0,0,1,1,1,0,0,1,1,0,0,1,1,0,0],dtype=tf.float32,shape=[1,5,5,1])
b = tf.constant([1,0,1,0,1,0,1,0,1],dtype=tf.float32,shape=[3,3,1,1])
c = tf.nn.conv2d(a,b,strides=[1, 2, 2, 1],padding='VALID')
d = tf.nn.conv2d(a,b,strides=[1, 2, 2, 1],padding='SAME')
with tf.Session() as sess:
    print ("c shape:")
    print (c.get_shape())
    print ("c value:")
    print (sess.run(c))
    print ("d shape:")
    print (d.get_shape())
    print ("d value:")
    print (sess.run(d))

# 运算结果
# c shape:
# (1, 2, 2, 1)
# c value:
# [[[[ 4.]
#    [ 4.]]
#
#   [[ 2.]
#    [ 4.]]]]
# d shape:
# (1, 3, 3, 1)
# d value:
# [[[[ 2.]
#    [ 3.]
#    [ 1.]]
#
#   [[ 1.]
#    [ 4.]
#    [ 3.]]
#
#   [[ 0.]
#    [ 2.]
#    [ 1.]]]]
```

## tf.nn.relu

**激活函数**

### Define

```python
relu(features, name=None)
```

### Parameter

| 参数名      | 必选   | 类型     | 说明                                       |
| -------- | ---- | ------ | ---------------------------------------- |
| features | Y    | tensor | float32、float64、int8、int16、int32、int64、uint8、unit16、half |
| name     | N    | string | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: relu.py
@time: 2018/5/9 21:30
"""

a = tf.constant([1,-2,0,4,-5,6])
b = tf.nn.relu(a)
with tf.Session() as sess:
    print (sess.run(b))

# 运算结果
# [1 0 0 4 0 6]
```

## tf.nn.max_pool

**池化**

### Define

```python
max_pool(value, ksize, strides, padding,
		data_format='NHWC', name=None)
```

### Parameter

| 参数名         | 必选   | 类型     | 说明                                       |
| ----------- | ---- | ------ | ---------------------------------------- |
| value       | Y    | tensor | 4维的张量，即[batch, height, width, channels]，类型为tf.float32 |
| ksize       | Y    | list   | 池化窗口的大小，长度为4的list，一般是[1，height，width，1]，因为不在batch和channels上做池化，所以第一个和最后一个维度为1 |
| strides     | Y    | list   | 池化窗口在每一个维度上的步长。一般strides[0]=strides[3]=1 |
| padding     | Y    | string | 只能为"VALID"、"SAME"其中之一，这个值决定了不同的池化方式。VALID：丢弃方式；SAME：补全方式 |
| data_format | N    | string | 只能为"NHWC"、"NCHW"。默认"NHWC"                |
| name        | N    | string | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: max_pool.py
@time: 2018/5/10 20:38
"""

a = tf.constant([1,3,2,1,2,9,1,1,1,3,2,3,5,6,1,2], dtype=tf.float32, shape=[1,4,4,1])
b = tf.nn.max_pool(a, ksize=[1,2,2,1], strides=[1, 2, 2, 1], padding='SAME')
c = tf.nn.max_pool(a, ksize=[1,2,2,1], strides=[1,2,2,1], padding='VALID')
with tf.Session() as sess:
    print ("b.shape")
    print (b.get_shape())
    print ("b.value")
    print (sess.run(b))

    print ("c.shape")
    print (c.get_shape())
    print ("c.value")
    print (sess.run(c))

# 运算结果
# b.shape
# (1, 2, 2, 1)
# b.value
# [[[[ 9.]
#    [ 2.]]
#
#   [[ 6.]
#    [ 3.]]]]
# c.shape
# (1, 2, 2, 1)
# c.value
# [[[[ 9.]
#    [ 2.]]
#
#   [[ 6.]
#    [ 3.]]]]

```

## tf.nn.dropout

**丢弃函数。在训练过程中，按照一定概率将一部分神经网络单元暂时从网络中丢弃，相当于从原始网络中找到一个更瘦的网络**

### Define

```python
dropout(x, keep_prob, noise_shape=None, seed=None, name=None)
```

### Parameter

| 参数名         | 必选   | 类型     | 说明                                       |
| ----------- | ---- | ------ | ---------------------------------------- |
| x           | Y    | tensor | x中keep_prob概率的元素，除以keep_prob。或者为0        |
| keep_prob   | Y    | scalar | dropout的概率，一般先用占位符                       |
| noise_shape | N    | tensor | 默认情况下，每个元素是否dropout是相互独立的。<br />如果noise_shape[i]==shape(x)[i]，该维度元素相互独立；<br />如果noise_shape[i]!=shape(x)[i]，该维度元素不相互独立，即要么一起dropout，要么一起保留 |
| seed        | N    | 数值     | 如果指定该值，每次dropout结果相同                     |
| name        | N    | string | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: dropout.py
@time: 2018/5/10 20:58
"""

a = tf.constant([1,2,3,4,5,6], shape=[2,3], dtype=tf.float32)
b = tf.placeholder(tf.float32)
c = tf.nn.dropout(a, b, [2,1], 1)
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print (sess.run(c, feed_dict={b:0.75}))

# 运算结果
# [[ 0.          0.          0.        ]
#  [ 5.33333349  6.66666651  8.        ]]
```

## tf.nn.sigmoid_cross_entropy_with_logits

**计算交叉熵函数。先对 logits 进行 sigmoid 计算，再计算交叉熵**

交叉熵：Loss函数的一种。常见的Loss函数为均方平方差；

sigmoid：激活函数的一种；

另外除了tf.nn.sigmoid_cross_entropy_with_logits以外，还有

tf.nn.softmax_cross_entropy_with_logits（参数logits必须为未经缩放的，函数中会对logits进行sigmoid运算）

tf.nn.sparse_softmax_cross_entropy_with_logits

tf.nn.weighted_cross_entropy_with_logits三种交叉熵函数

### Define

```python
sigmoid_cross_entropy_with_logits(_sentinel=None, labels=None, logits=None, name=None)
```

### Parameter

| 参数名       | 必选   | 类型     | 说明              |
| --------- | ---- | ------ | --------------- |
| _sentinel | N    | None   | 一般不使用           |
| logits    | N    | tensor | float32、float64 |
| labels    | N    | tensor | shape与logits相同  |
| name      | N    | string | 运算名称            |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: sigmod_cross_entropy_with_logits.py.py
@time: 2018/5/11 09:27
"""

x = tf.constant([1,2,3,4,5,6,7], dtype=tf.float64)
y = tf.constant([1,1,1,0,0,1,0], dtype=tf.float64)
loss = tf.nn.sigmoid_cross_entropy_with_logits(logits=x, labels=y)

with tf.Session() as sess:
    print (sess.run(loss))

# 运算结果
# [3.13261688e-01   1.26928011e-01   4.85873516e-02   4.01814993e+00
#  5.00671535e+00   2.47568514e-03   7.00091147e+00]
```
**该函数返回值是一个向量，并不是一个数。**

**如果要求交叉熵，需要进一步做tf.reduce_sum操作，即对向量里所有元素求和；**

**如果要求Loss（常见），需要进一步做tf.reduce_mean操作，对向量求平均值；**


## tf.nn.bias_add

**将偏差项 bias 加到 value 上面，可以看做是 tf.add 的一个特例。其中 bias 必须是一维的，并且维度和 value 的最后一维相同，数据类型必须和 value 相同**

### Define

```python
bias_add(value, bias, data_format=None, name=None)
```

### Parameter

| 参数名         | 必选   | 类型       | 说明                                       |
| ----------- | ---- | -------- | ---------------------------------------- |
| value       | Y    | tensor   | Float、double、int64、int32、int16、int8、uint8、complex64、complex128 |
| bias        | Y    | 1维tensor | 维度必须和value最后一维维度相等                       |
| data_format | N    | string   | 支持"NHWC"、"NCHW"                          |
| name        | N    | string   | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: bias_add.py
@time: 2018/5/11 11:45
"""

a = tf.constant([[1.0, 2.0],[1.0, 2.0],[1.0, 2.0]])
b = tf.constant([2.0,1.0])
c = tf.constant([1.0])
with tf.Session() as sess:
    print ("bias_add(a, b)")
    print (sess.run(tf.nn.bias_add(a, b)))
    # print (sess.run(tf.nn.bias_add(a, c))) error
    print ("-----------------")
    print ("tf.add(a, b)")
    print (sess.run(tf.add(a, b)))
    print ("-----------------")
    print ("tf.add(a, c")
    print (sess.run(tf.add(a, c)))


# bias_add(a, b)
# [[ 3.  3.]
#  [ 3.  3.]
#  [ 3.  3.]]
# -----------------
# tf.add(a, b)
# [[ 3.  3.]
#  [ 3.  3.]
#  [ 3.  3.]]
# -----------------
# tf.add(a, c
# [[ 2.  3.]
#  [ 2.  3.]
#  [ 2.  3.]]
```

## tf.truncated_normal

**在截断的正态分布中输出随机数，取值范围[ mean - 2 * stddev, mean + 2 * stddev ]**

### Define

```python
truncated_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)
```

### Parameter

| 参数名    | 必选   | 类型           | 说明                     |
| ------ | ---- | ------------ | ---------------------- |
| shape  | Y    | 1维整型张量、array | 输出张量的维度                |
| mean   | N    | 0维张量、数值      | 均值                     |
| stddev | N    | 0维张量、数值      | 标准差                    |
| dtype  | N    | dtype        | 输出类型                   |
| seed   | N    | 数值           | 随机种子，若seed复制，每次产生相同随机数 |
| name   | N    | string       | 运算名称                   |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: truncated_nomal.py
@time: 2018/5/11 09:52
"""

initial = tf.truncated_normal(shape=[3,3], mean=0, stddev=1)
print (tf.Session().run(initial))

# 运算结果
# [[-0.49039015  0.67874366  1.00580561]
#  [ 0.97545707  0.15845388 -0.73228759]
#  [-0.60446167 -0.87802863  0.24452373]]
```

## tf.reduce_mean

**计算张量input_tensor的平均值**

### Define

```python
reduce_mean(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None)
```

### Parameter

| 参数名               | 必选   | 类型       | 说明                                       |
| ----------------- | ---- | -------- | ---------------------------------------- |
| input_tensor      | Y    | 张量       | 待求平均值的张量                                 |
| axis              | N    | None、0、1 | None：全局球平均值<br />0：求每一列平均值<br />1：求每一行平均值 |
| keep_dims         | N    | boolean  | 保留原来的维度（例如不会从二维矩阵降为一维）                   |
| name              | N    | string   | 运算名称                                     |
| reduction_indices | N    | None     | 和axis等价，已弃用                              |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: reduce_mean.py
@time: 2018/5/11 13:46
"""

initial = [[1.,1.],[2.,2.]]
x = tf.Variable(initial, dtype=tf.float32)
init_op = tf.global_variables_initializer()

with tf.Session() as sess:
    sess.run(init_op)
    print (sess.run(tf.reduce_mean(x)))
    print (sess.run(tf.reduce_mean(x, 0)))  #column
    print (sess.run(tf.reduce_mean(x, 1)))  #row

# 运算结果
# 1.5
# [ 1.5  1.5]
# [ 1.  2.]
```

## tf.square

**计算张量对应元素的平方**

### Define

```python
square(x, name=None)
```

### Parameter

| 参数名  | 必选   | 类型     | 说明                                       |
| ---- | ---- | ------ | ---------------------------------------- |
| x    | Y    | tensor | half、float32、float64、int32、int64、complex64、complex128 |
| name | N    | string | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: square.py
@time: 2018/5/11 13:59
"""

initial_x = [[1.,1.], [2.,2.]]
x = tf.Variable(initial_x, dtype=tf.float32)
x_2 = tf.square(initial_x)

init_op = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init_op)
    print (sess.run(x_2))

# 运算结果
# [[ 1.  1.]
#  [ 4.  4.]]
```

## tf.squared_difference

**计算张量x、y对应元素差平方**

### Define

```python
squared_difference(x, y, name=None)
```

### Parameter

| 参数名  | 必选   | 类型     | 说明                                       |
| ---- | ---- | ------ | ---------------------------------------- |
| x    | Y    | tensor | half、float32、float64、int32、int64、complex64、complex128 |
| y    | Y    | tensor | half、float32、float64、int32、int64、complex64、complex128 |
| name | N    | string | 运算名称                                     |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: squared_difference.py
@time: 2018/5/11 13:54
"""

initial_x = [[1.,1.], [2.,2.]]
x = tf.Variable(initial_x, dtype=tf.float32)
initial_y = [[3.,3.], [4.,4.]]
y = tf.Variable(initial_y, dtype=tf.float32)

diff = tf.squared_difference(x, y)
init_op = tf.global_variables_initializer()

with tf.Session() as sess:
    sess.run(init_op)
    print (sess.run(diff))

# 运算名称
# [[ 4.  4.]
#  [ 4.  4.]]
```

## tf.Variable

**类，维护图在执行过程中的状态信息，例如神经网络权重值的变化**

### Define

```python
__init__(initial_value=None, trainable=True, collections=None, validate_shape=True,
		caching_device=None, name=None, variable_def=None, dtype=None, expected_shape=None,
		import_scope=None)
```

### Parameter

| 参数名            | 类型                | 说明                                       |
| -------------- | ----------------- | ---------------------------------------- |
| initial_value  | tensor            | Variable类的初始值。这个张量必须制定shape信息，否则后面validate_shape需设为False |
| trainable      | boolean           | 是否把变量添加到 GraphKeys.TRAINABLE_VARIABLES中。<br />（ collection 是一种全局存储，不受变量名生存空间影响，一处保存，到处可取） |
| collections    | Graph collections | 全局存储，默认是 GraphKeys.GLOBAL_VARIABLES      |
| validate_shape | boolean           | 是否允许被位置维度的initial_value初始化               |
| caching_device | string            | 指明哪个device用来缓存变量                         |
| name           | string            | 变量名                                      |
| dtype          | dtype             | 如果设置，初始化的值按照该类型初始化                       |
| expected_shape | tensorShape       | 要是设置了，那么初始的值会是这种维度                       |

### Demo

```python
import tensorflow as tf

"""
@version: ??
@author: Gavin
@license: Apache Licence 
@contact: woniuyf@gmail.com
@site: https://wonius.top/
@software: PyCharm
@file: Variable.py
@time: 2018/5/11 14:46
"""


initial = tf.truncated_normal(shape=[10, 10], mean=0, stddev=1)
W = tf.Variable(initial)

list = [[1.,1.], [2.,2.]]
X = tf.Variable(list, dtype=tf.float32)

init_op = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init_op)
    print ("------------------------------------------------------------")
    print (sess.run(W))

    print ("------------------------------------------------------------")
    print (sess.run(W[:2,:2]))
    op = W[:2,:2].assign(22.*tf.ones((2,2)))

    print ("------------------------------------------------------------")
    print (sess.run(op))

    print ("------------------------------------------------------------")
    print (W.eval(sess)) # computes and returns the value of this variable
    print (W.eval())    # user with the default session

    print ("------------------------------------------------------------")
    print (W.dtype)
    print (sess.run(W.initial_value))
    print (sess.run(W.op))
    print (W.shape)

    print ("------------------------------------------------------------")
    print (sess.run(X))

# 运算结果
# ------------------------------------------------------------
# [[ -1.36460924e+00  -8.78410161e-01   1.87626636e+00   1.27253577e-01
#     1.77748889e-01  -2.79777735e-01  -1.29324961e+00   1.18464601e+00
#     5.91014000e-03   3.91639054e-01]
#  [  1.09924674e+00  -1.08868659e+00  -1.12289786e+00  -6.78496242e-01
#     4.25694019e-01   1.06920172e-02   8.05350840e-01  -7.63205171e-01
#     3.23375136e-01   4.49576497e-01]
#  [ -1.05841234e-01  -2.85442650e-01   1.01552248e+00  -1.69130695e+00
#    -1.15725782e-03   1.47837210e+00   6.69513494e-02   4.84152675e-01
#    -3.82478178e-01  -1.20722222e+00]
#  [  3.09620090e-02  -7.98057988e-02   5.60789108e-01  -1.78828037e+00
#     1.95968688e-01  -2.27404922e-01  -1.72476351e+00  -1.23355138e+00
#    -9.63081241e-01  -1.60457373e+00]
#  [  3.23699638e-02  -5.90677261e-01  -1.92470342e-01  -4.10615206e-01
#     3.35628651e-02  -1.41288012e-01  -3.40104014e-01   1.13289797e+00
#     6.52421772e-01   9.17639136e-01]
#  [  5.11528432e-01   1.64260656e-01  -7.84879684e-01  -2.26418376e-01
#    -8.90385628e-01  -3.38358462e-01   1.96581781e+00  -7.17314780e-02
#     1.23814851e-01   7.64766455e-01]
#  [  2.22003132e-01  -1.70102561e+00  -1.60155982e-01   1.10927892e+00
#    -1.14349496e+00  -6.86953589e-02  -3.56495827e-01  -5.14335275e-01
#     1.98457405e-01  -2.56361932e-01]
#  [ -6.98417068e-01  -1.91254735e-01   2.43045017e-01   1.62485635e+00
#     1.34399071e-01   4.48576242e-01   9.65317804e-03   2.63324082e-01
#     1.71964037e+00   1.81242809e-01]
#  [ -3.06579232e-01   6.82186067e-01  -1.34259194e-01   8.21067274e-01
#     2.75264233e-01   2.36507237e-01  -5.18465579e-01   2.66540438e-01
#    -4.08049405e-01   4.00008321e-01]
#  [  6.39303386e-01  -3.73077720e-01  -3.93441260e-01   1.44391823e+00
#    -4.43163812e-01  -2.28796184e-01  -9.21004534e-01  -6.29425883e-01
#    -1.04850844e-01   5.76163709e-01]]
# ------------------------------------------------------------
# [[-1.36460924 -0.87841016]
#  [ 1.09924674 -1.08868659]]
# ------------------------------------------------------------
# [[  2.20000000e+01   2.20000000e+01   1.87626636e+00   1.27253577e-01
#     1.77748889e-01  -2.79777735e-01  -1.29324961e+00   1.18464601e+00
#     5.91014000e-03   3.91639054e-01]
#  [  2.20000000e+01   2.20000000e+01  -1.12289786e+00  -6.78496242e-01
#     4.25694019e-01   1.06920172e-02   8.05350840e-01  -7.63205171e-01
#     3.23375136e-01   4.49576497e-01]
#  [ -1.05841234e-01  -2.85442650e-01   1.01552248e+00  -1.69130695e+00
#    -1.15725782e-03   1.47837210e+00   6.69513494e-02   4.84152675e-01
#    -3.82478178e-01  -1.20722222e+00]
#  [  3.09620090e-02  -7.98057988e-02   5.60789108e-01  -1.78828037e+00
#     1.95968688e-01  -2.27404922e-01  -1.72476351e+00  -1.23355138e+00
#    -9.63081241e-01  -1.60457373e+00]
#  [  3.23699638e-02  -5.90677261e-01  -1.92470342e-01  -4.10615206e-01
#     3.35628651e-02  -1.41288012e-01  -3.40104014e-01   1.13289797e+00
#     6.52421772e-01   9.17639136e-01]
#  [  5.11528432e-01   1.64260656e-01  -7.84879684e-01  -2.26418376e-01
#    -8.90385628e-01  -3.38358462e-01   1.96581781e+00  -7.17314780e-02
#     1.23814851e-01   7.64766455e-01]
#  [  2.22003132e-01  -1.70102561e+00  -1.60155982e-01   1.10927892e+00
#    -1.14349496e+00  -6.86953589e-02  -3.56495827e-01  -5.14335275e-01
#     1.98457405e-01  -2.56361932e-01]
#  [ -6.98417068e-01  -1.91254735e-01   2.43045017e-01   1.62485635e+00
#     1.34399071e-01   4.48576242e-01   9.65317804e-03   2.63324082e-01
#     1.71964037e+00   1.81242809e-01]
#  [ -3.06579232e-01   6.82186067e-01  -1.34259194e-01   8.21067274e-01
#     2.75264233e-01   2.36507237e-01  -5.18465579e-01   2.66540438e-01
#    -4.08049405e-01   4.00008321e-01]
#  [  6.39303386e-01  -3.73077720e-01  -3.93441260e-01   1.44391823e+00
#    -4.43163812e-01  -2.28796184e-01  -9.21004534e-01  -6.29425883e-01
#    -1.04850844e-01   5.76163709e-01]]
# ------------------------------------------------------------
# [[  2.20000000e+01   2.20000000e+01   1.87626636e+00   1.27253577e-01
#     1.77748889e-01  -2.79777735e-01  -1.29324961e+00   1.18464601e+00
#     5.91014000e-03   3.91639054e-01]
#  [  2.20000000e+01   2.20000000e+01  -1.12289786e+00  -6.78496242e-01
#     4.25694019e-01   1.06920172e-02   8.05350840e-01  -7.63205171e-01
#     3.23375136e-01   4.49576497e-01]
#  [ -1.05841234e-01  -2.85442650e-01   1.01552248e+00  -1.69130695e+00
#    -1.15725782e-03   1.47837210e+00   6.69513494e-02   4.84152675e-01
#    -3.82478178e-01  -1.20722222e+00]
#  [  3.09620090e-02  -7.98057988e-02   5.60789108e-01  -1.78828037e+00
#     1.95968688e-01  -2.27404922e-01  -1.72476351e+00  -1.23355138e+00
#    -9.63081241e-01  -1.60457373e+00]
#  [  3.23699638e-02  -5.90677261e-01  -1.92470342e-01  -4.10615206e-01
#     3.35628651e-02  -1.41288012e-01  -3.40104014e-01   1.13289797e+00
#     6.52421772e-01   9.17639136e-01]
#  [  5.11528432e-01   1.64260656e-01  -7.84879684e-01  -2.26418376e-01
#    -8.90385628e-01  -3.38358462e-01   1.96581781e+00  -7.17314780e-02
#     1.23814851e-01   7.64766455e-01]
#  [  2.22003132e-01  -1.70102561e+00  -1.60155982e-01   1.10927892e+00
#    -1.14349496e+00  -6.86953589e-02  -3.56495827e-01  -5.14335275e-01
#     1.98457405e-01  -2.56361932e-01]
#  [ -6.98417068e-01  -1.91254735e-01   2.43045017e-01   1.62485635e+00
#     1.34399071e-01   4.48576242e-01   9.65317804e-03   2.63324082e-01
#     1.71964037e+00   1.81242809e-01]
#  [ -3.06579232e-01   6.82186067e-01  -1.34259194e-01   8.21067274e-01
#     2.75264233e-01   2.36507237e-01  -5.18465579e-01   2.66540438e-01
#    -4.08049405e-01   4.00008321e-01]
#  [  6.39303386e-01  -3.73077720e-01  -3.93441260e-01   1.44391823e+00
#    -4.43163812e-01  -2.28796184e-01  -9.21004534e-01  -6.29425883e-01
#    -1.04850844e-01   5.76163709e-01]]
# [[  2.20000000e+01   2.20000000e+01   1.87626636e+00   1.27253577e-01
#     1.77748889e-01  -2.79777735e-01  -1.29324961e+00   1.18464601e+00
#     5.91014000e-03   3.91639054e-01]
#  [  2.20000000e+01   2.20000000e+01  -1.12289786e+00  -6.78496242e-01
#     4.25694019e-01   1.06920172e-02   8.05350840e-01  -7.63205171e-01
#     3.23375136e-01   4.49576497e-01]
#  [ -1.05841234e-01  -2.85442650e-01   1.01552248e+00  -1.69130695e+00
#    -1.15725782e-03   1.47837210e+00   6.69513494e-02   4.84152675e-01
#    -3.82478178e-01  -1.20722222e+00]
#  [  3.09620090e-02  -7.98057988e-02   5.60789108e-01  -1.78828037e+00
#     1.95968688e-01  -2.27404922e-01  -1.72476351e+00  -1.23355138e+00
#    -9.63081241e-01  -1.60457373e+00]
#  [  3.23699638e-02  -5.90677261e-01  -1.92470342e-01  -4.10615206e-01
#     3.35628651e-02  -1.41288012e-01  -3.40104014e-01   1.13289797e+00
#     6.52421772e-01   9.17639136e-01]
#  [  5.11528432e-01   1.64260656e-01  -7.84879684e-01  -2.26418376e-01
#    -8.90385628e-01  -3.38358462e-01   1.96581781e+00  -7.17314780e-02
#     1.23814851e-01   7.64766455e-01]
#  [  2.22003132e-01  -1.70102561e+00  -1.60155982e-01   1.10927892e+00
#    -1.14349496e+00  -6.86953589e-02  -3.56495827e-01  -5.14335275e-01
#     1.98457405e-01  -2.56361932e-01]
#  [ -6.98417068e-01  -1.91254735e-01   2.43045017e-01   1.62485635e+00
#     1.34399071e-01   4.48576242e-01   9.65317804e-03   2.63324082e-01
#     1.71964037e+00   1.81242809e-01]
#  [ -3.06579232e-01   6.82186067e-01  -1.34259194e-01   8.21067274e-01
#     2.75264233e-01   2.36507237e-01  -5.18465579e-01   2.66540438e-01
#    -4.08049405e-01   4.00008321e-01]
#  [  6.39303386e-01  -3.73077720e-01  -3.93441260e-01   1.44391823e+00
#    -4.43163812e-01  -2.28796184e-01  -9.21004534e-01  -6.29425883e-01
#    -1.04850844e-01   5.76163709e-01]]
# ------------------------------------------------------------
# <dtype: 'float32_ref'>
# [[-1.26520741  0.15191878 -0.20875564 -1.3333143  -1.2195878   1.98078632
#   -0.37404546 -0.08800519  1.59779143  0.43416482]
#  [-0.49763143  1.67703199 -0.75461388  1.39778709 -0.59904957  1.07046878
#   -0.66246015  1.20341682 -0.91598368  0.02659398]
#  [ 0.15760519  1.58692122 -1.64905691 -0.82413578 -1.35249043 -0.05494672
#    0.1835352  -1.16055572  0.06633712 -0.60654968]
#  [ 0.44664332  1.32015193  1.19064319  1.04832637 -1.35440087  1.46269107
#   -0.73073471  1.79925811 -0.81201482 -0.52865958]
#  [ 0.86693144  0.58485049  0.74763191  0.16709246  0.87199962 -0.01152798
#   -0.45584357  1.63553476 -0.97385317  0.22666731]
#  [ 0.88356006  0.02941392  0.48637423  0.6196385   0.72364163  0.73068076
#    0.24449067 -1.82126474  0.43334225 -0.57979202]
#  [ 0.90533078 -0.46376067  1.15587556 -0.83500886  0.61234719  0.32427955
#    1.49570584 -0.48938131 -1.18225038  0.19612415]
#  [ 0.66482532  1.62603843  0.83715767  0.93517244 -1.04942799  1.2456522
#    1.45724511  0.82795757 -0.39859715 -1.37503803]
#  [ 0.84474677 -0.97584003 -0.23359941  0.02022619  0.15871523  0.07553232
#   -0.58795238 -1.66832328 -1.08443391 -0.25014412]
#  [ 0.00373921  0.82514638 -0.06235779  0.58550674 -0.90887821 -0.91024882
#    0.57362115 -1.92959452  1.00629067 -0.69716936]]
# None
# (10, 10)
# ------------------------------------------------------------
# [[ 1.  1.]
#  [ 2.  2.]]

```

