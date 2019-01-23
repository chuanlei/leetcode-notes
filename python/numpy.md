# numpy教程

## 参考文献

![参考文献](https://www.datacamp.com/community/tutorials/python-numpy-tutorial)


## 概要

数据分析三剑客：
1. NumPy
2. SciPy
3. Pandas

说白了，`NumPy`的核心价值在于提供了`array`数据结构。

```python
numpy.__version__
# Print out memory address
print(my_2d_array.data)

# Print out the shape of `my_array`
print(my_2d_array.shape)

# Print out the data type of `my_array`
print(my_2d_array.dtype)

# Print out the stride of `my_array`
print(my_2d_array.strides)
```

## 初始化
```python
np.array()
my_array = np.array([[1,2,3,4],[5,6,7,8]], dtype=np.int64)
```

#### 空数组
```python
# Create an array of ones
np.ones((3,4))

# Create an array of zeros
np.zeros((2,3,4),dtype=np.int16)

# Create an array with random values
np.random.random((2,2))

# Create an empty array
np.empty((3,2))

# Create a full array
np.full((2,2),7)

# Create an array of evenly-spaced values
np.arange(10,25,5)

# Create an array of evenly-spaced values
np.linspace(0,2,9)

# 单位矩阵
np.eye(4)
np.identity(5)
```

#### 从文本加载数据
```python
# This is your data in the text file
# Value1  Value2  Value3
# 0.2536  0.1008  0.3857
# 0.4839  0.4536  0.3561
# 0.1292  0.6875  0.5929
# 0.1781  0.3049  0.8928
# 0.6253  0.3486  0.8791

# Import your data
x, y, z = np.loadtxt('data.txt',
                    skiprows=1,
                    unpack=True)
```

`delimiter`和`dtype`也是可选的参数。

`genfromtxt`的使用
```python
# Your data in the text file
# Value1  Value2  Value3
# 0.4839  0.4536  0.3561
# 0.1292  0.6875  MISSING
# 0.1781  0.3049  0.8928
# MISSING 0.5801  0.2038
# 0.5993  0.4357  0.7410

my_array2 = np.genfromtxt('data2.txt',
                      skip_header=1,
                      filling_values=-999)
```

#### 保存NumPy数组
```python
x = np.arange(0.0, 5.0, 1.0)
np.savetxt('test.out', x, delimiter=',')
```

还有其他一些选项
1. `save()`: `.npy`格式
2. `savez()`: 没有压缩过`.npz`格式
3. `savez_compressed()`: 压缩过的`.npz`格式

## 科学计算
#### broadcast（扩展）

![官方资料](https://docs.scipy.org/doc/numpy/user/basics.broadcasting.html)

![通俗资料](https://blog.csdn.net/lanchunhui/article/details/50158975)

1. 形状相同
2. 其中一个是一维(可以拷贝扩展)

满足上面任何一个，我们认为两个数组可以`be broadcast together`

#### 数组的运算
1. `+` v.s `add()`
2. `-` v.s `subtract()`
3. `*` v.s `multiply()`
4. `/` v.s `divide()`
5. `%` v.s `remainder()`

还有一些其他算子，比如
1. `np.sqrt()`
2. `np.exp()`
3. `np.sin()`
4. `np.cos()`
5. `np.log()`
6. `np.dot()`

##### 聚合函数
1. `a.sum()`
2. `a.min()`
3. `b.max(axis=0)`
4. `b.cumsum(axis=1)`
5. `a.mean()`
6. `np.median()`
7. `np.corrcoef()`
8. `np.std(b)`

### 抽取子集
#### slice
```python
# Select items at index 0 and 1
print(my_array[0:2])

# Select items at row 0 and 1, column 1
print(my_2d_array[0:2,1])

# Select items at row 1
# This is the same as saying `my_3d_array[1,:,:]`
print(my_3d_array[1,...])
```

#### fancy indexing
```python
# Try out a simple example
print(my_array[my_array<2])

# Specify a condition
bigger_than_3 = (my_3d_array >= 3)

# Use the condition to index our 3d array
print(my_3d_array[bigger_than_3])
```

`fancy indexing`的用处
```python 
# Select elements at (1,0), (0,1), (1,2) and (0,0)
print(my_2d_array[[1, 0, 1, 0],[0, 1, 2, 0]])

# Select a subset of the rows and columns
print(my_2d_array[[1, 0, 1, 0]][:,[0,1,2,0]])
```

### 文档
```python
# Look up info on `mean` with `np.lookfor()` 
print(np.lookfor("mean"))

# Get info on data types with `np.info()`
np.info(np.ndarray.dtype)
```

## 操作数组
### 反置
```python
# Print `my_2d_array`
print(my_2d_array)

# Transpose `my_2d_array`
print(np.transpose(my_2d_array))

# Or use `T` to transpose `my_2d_array`
print(my_2d_array.T)
```

### reshape与resize
```python
# Print the shape of `x`
print(x.shape)

# Resize `x` to ((6,4))
np.resize(x, (6,4))

# Try out this as well
x.resize((6,4))

# Print out `x`
print(x)

# Flatten `x`
z = x.ravel()
```

### Append && Insert && Delete && Join && Split
```python
# Concatentate `my_array` and `x`
print(np.concatenate((my_array,x)))

# Stack arrays row-wise
print(np.vstack((my_array, my_2d_array)))

# Stack arrays row-wise
print(np.r_[my_resized_array, my_2d_array])

# Stack arrays horizontally
print(np.hstack((my_resized_array, my_2d_array)))

# Stack arrays column-wise
print(np.column_stack((my_resized_array, my_2d_array)))

# Stack arrays column-wise
print(np.c_[my_resized_array, my_2d_array])
```

## 可视化
#### histogram
```python
# Pass the array to `np.histogram()`
print(np.histogram(my_3d_array))

# Specify the number of bins
print(np.histogram(my_3d_array, bins=range(0,13)))
```















