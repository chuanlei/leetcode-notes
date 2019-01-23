# Pandas学习笔记

![参考资料](https://seancheney.gitbook.io/python-for-data-analysis-2nd/di-05-zhang-pandas-ru-men)

https://posts.careerengine.us/posts/category/%E7%94%9F%E6%B4%BB?&docked-header=false

`Pandas`和`NumPy`最大的不同在于`Pandas`是专门为处理表格和混杂数据设计的，而`NumPy`更适合处理统一的数值数组数据。

```python
import pandas as pd
from pandas import Series, DataFrame
```



## Series
Series是一种类似于一维数组的对象，它由一组数据（各种NumPy数据类型）以及一组与之相关的数据标签（即索引）组成。

```python
obj = pd.Series([4, 7, -5, 3])
```

由于我们没有为数据指定索引，于是会自动创建一个0到N-1（N为数据的长度）的整数型索引。你可以通过Series 的values和index属性获取其数组表示形式和索引对象：

```python
obj.values
obj.index
```

通常，我们希望所创建的Series带有一个可以对各个数据点进行标记的索引：

```python
obj2 = pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])
```

与普通NumPy数组相比，你可以通过索引的方式选取Series中的单个或一组值：
```python
obj2['a']
obj2[['c', 'a', 'd']]
```

使用NumPy函数或类似NumPy的运算（如根据布尔型数组进行过滤、标量乘法、应用数学函数等）都会保留索引值的链接
```python
obj2[obj2 > 0]
obj2 * 2
np.exp(obj2)
```
还可以将Series看成是一个定长的有序字典，因为它是索引值到数据值的一个映射。它可以用在许多原本需要字典参数的函数中：

```python
'b' in obj2
'e' in obj2
```

如果数据被存放在一个Python字典中，也可以直接通过这个字典来创建Series：
```python
sdata = {'Ohio': 35000, 'Texas': 71000, 'Oregon': 16000, 'Utah': 5000}
obj3 = pd.Series(sdata)
```

对于许多应用而言，Series最重要的一个功能是，它会根据运算的索引标签自动对齐数据：
```python
In [35]: obj3
Out[35]: 
Ohio      35000
Oregon    16000
Texas     71000
Utah       5000
dtype: int64

In [36]: obj4
Out[36]: 
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
dtype: float64

In [37]: obj3 + obj4
Out[37]: 
California         NaN
Ohio           70000.0
Oregon         32000.0
Texas         142000.0
Utah               NaN
dtype: float64
```



## DataFrame
`DataFrame`是其核心数据结构。

### structured array与record array
```python
# A structured array
my_array = np.ones(3, dtype=([('foo', int), ('bar', float)]))
print(my_array['foo'])

# A record array
my_array2 = my_array.view(np.recarray)
print(my_array2.foo)
```

`DataFrame`的创建

建DataFrame的办法有很多，最常用的一种是直接传入一个由等长列表或NumPy数组组成的字典：
```python
data = {'state': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada', 'Nevada'],
        'year': [2000, 2001, 2002, 2001, 2002, 2003],
        'pop': [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}
frame = pd.DataFrame(data)

frame2 = pd.DataFrame(data, columns=['year', 'state', 'pop', 'debt'],
             index=['one', 'two', 'three', 'four', 'five', 'six'])
```

通过类似字典标记的方式或属性的方式，可以将DataFrame的列获取为一个Series：
```python
frame2['state']
frame2.year
```

行也可以通过位置或名称的方式进行获取，比如用loc属性（稍后将对此进行详细讲解）：
```python
frame2.loc['three']
```

关键字del用于删除列。作为del的例子，我先添加一个新的布尔值的列，state是否为'Ohio'：
```python
In [61]: frame2['eastern'] = frame2.state == 'Ohio'

In [62]: frame2
Out[62]: 
       year   state  pop  debt  eastern
one    2000    Ohio  1.5   NaN     True
two    2001    Ohio  1.7  -1.2     True
three  2002    Ohio  3.6   NaN     True
four   2001  Nevada  2.4  -1.5    False
five   2002  Nevada  2.9  -1.7    False
six    2003  Nevada  3.2   NaN    False
```

del方法可以用来删除这列：
```python
In [63]: del frame2['eastern']

In [64]: frame2.columns
Out[64]: Index(['year', 'state', 'pop', 'debt'], dtype='object')
```

如果嵌套字典传给DataFrame，pandas就会被解释为：外层字典的键作为列，内层键则作为行索引：
```python
In [65]: pop = {'Nevada': {2001: 2.4, 2002: 2.9},
....:        'Ohio': {2000: 1.5, 2001: 1.7, 2002: 3.6}}
In [66]: frame3 = pd.DataFrame(pop)

In [67]: frame3
Out[67]: 
      Nevada  Ohio
2000     NaN   1.5
2001     2.4   1.7
2002     2.9   3.6
```
你也可以使用类似NumPy数组的方法，对DataFrame进行转置（交换行和列）：
```python
In [68]: frame3.T
Out[68]: 
        2000  2001  2002
Nevada   NaN   2.4   2.9
Ohio     1.5   1.7   3.6
```

### DataFrame基本功能
#### 重新索引
```python
obj = pd.Series([4.5, 7.2, -5.3, 3.6], index=['d', 'b', 'a', 'c'])
obj2 = obj.reindex(['a', 'b', 'c', 'd', 'e'])
```

对于时间序列这样的有序数据，重新索引时可能需要做一些插值处理。method选项即可达到此目的，例如，使用ffill可以实现前向值填充：
```python
obj3 = pd.Series(['blue', 'purple', 'yellow'], index=[0, 2, 4])
obj3.reindex(range(6), method='ffill')
```

列可以用columns关键字重新索引：
```python
In [102]: states = ['Texas', 'Utah', 'California']

In [103]: frame.reindex(columns=states)
Out[103]: 
   Texas  Utah  California
a      1   NaN           2
c      4   NaN           5
d      7   NaN           8
```

