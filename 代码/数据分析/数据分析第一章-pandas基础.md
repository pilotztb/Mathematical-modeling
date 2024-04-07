---
title: 数据分析第一章-pandas基础
author: pilot
categories:
  - 数模
  - 数据分析
abbrlink: c138cc70
date: 2024-02-27 08:41:06
tags:
description:
---

# 导入

<!--more-->

```python
In [1]: import pandas as pd
In [2]: from pandas import Series, DataFrame
```

# pandas的数据结构介绍

##  Series

### 创建最简单的series

```python
obj = pd.Series([4, 7, -5, 3])
```



一种类似于一维数组的对象，它由一组数据（各种NumPy数据类型）以及一组与之相关的数据标签（即索引）组成。仅由一组数据即可产生最简单的Series：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240228060409357.png)

Series的字符串表现形式为：索引在左边，值在右边。由于我们没有为数据指定索引，于是会自动创建一个0到N-1（N为数据的长度）的整数型索引。

### 获取series的数组表示形式和索引对象

可以通过Series 的values和index属性获取其数组表示形式和索引对象：

```python
obj.values
obj.index
```



![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240228060628380.png)

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240228060655444.png)

### 指定创建的Series的索引

```python
obj2 = pd.Series([4, 7, -5, 3], index = ['d', 'b', 'a', 'c'])
obj2
```



![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240228060854021.png)

### 通过索引的形式选取Series中单个或者一组值

```python
In [18]: obj2['a']
Out[18]: -5

In [19]: obj2['d'] = 6

In [20]: obj2[['c', 'a', 'd']]
Out[20]: 
c    3
a   -5
d    6
dtype: int64
```

### 修改Series中数组对象里的值

```python
In [21]: obj2[obj2 > 0]
Out[21]: 
d    6
b    7
c    3
dtype: int64

In [22]: obj2 * 2
Out[22]:
d    12
b    14
a   -10
c     6
dtype: int64

In [23]: np.exp(obj2)
Out[23]: 
d     403.428793
b    1096.633158
a       0.006738
c      20.085537
dtype: float64
```

### 与字典的关系

#### 可以将Series看成是一个定长的有序字典，因为它是索引值到数据值的一个映射。它可以用在许多原本需要字典参数的函数中：

```python
In [24]: 'b' in obj2
Out[24]: True

In [25]: 'e' in obj2
Out[25]: False
```

#### 直接传入一个python字典

```python
In [26]: sdata = {'Ohio': 35000, 'Texas': 71000, 'Oregon': 16000, 'Utah': 5000}

In [27]: obj3 = pd.Series(sdata)

In [28]: obj3
Out[28]: 
Ohio      35000
Oregon    16000
Texas     71000
Utah       5000
dtype: int64
```

#### 通过传入一个python列表，修改Series中的索引，从而修改Series中的键值对排序

```python
In [29]: states = ['California', 'Ohio', 'Oregon', 'Texas']

In [30]: obj4 = pd.Series(sdata, index=states)

In [31]: obj4
Out[31]: 
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
dtype: float64
```

在这个例子中，sdata中跟states索引相匹配的那3个值会被找出来并放到相应的位置上，但由于"California"所对应的sdata值找不到，所以其结果就为NaN（即“非数字”（not a number），在pandas中，它用于表示缺失或NA值）。因为‘Utah’不在states中，它被从结果中除去。

#### isnull和notnull函数可用于检测缺失数据：

```python
In [32]: pd.isnull(obj4)
Out[32]: 
California     True
Ohio          False
Oregon        False
Texas         False
dtype: bool

In [33]: pd.notnull(obj4)
Out[33]: 
California    False
Ohio           True
Oregon         True
Texas          True
dtype: bool
```

Series也有类似的实例方法：

```
In [34]: obj4.isnull()
Out[34]: 
California     True
Ohio          False
Oregon        False
Texas         False
dtype: bool
```

#### name属性

```python
In [38]: obj4.name = 'population'

In [39]: obj4.index.name = 'state'

In [40]: obj4
Out[40]: 
state
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
Name: population, dtype: float64
```

#### 修改索引

```python
In [41]: obj
Out[41]: 
0    4
1    7
2   -5
3    3
dtype: int64

In [42]: obj.index = ['Bob', 'Steve', 'Jeff', 'Ryan']

In [43]: obj
Out[43]: 
Bob      4
Steve    7
Jeff    -5
Ryan     3
dtype: int64
```

## DataFrame

### 通过传入等长列表或Numpy数组组成的字典建立DataFrame

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240304054932701.png)

### head方法

```python
frame.head()
```

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240304055151225.png)

### 指定列的排列顺序

```python
pd.DataFrame(data, columns = ['year', 'state', 'pop'])
```

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240304055531166.png)

### 指定行的编号

```python
frame2 = pd.DataFrame(data, columns = ['year', 'state', 'pop'], index = ['one', 'two', 'three', 'four', 'five', 'six'])
frame2
```

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240304060033591.png)

### 从DataFrame的列获取为一个Series

#### 类似字典标记的方式

```python
In [51]: frame2['state']
Out[51]: 
one        Ohio
two        Ohio
three      Ohio
four     Nevada
five     Nevada
six      Nevada
Name: state, dtype: object
```

#### 属性的方式

```python
In [52]: frame2.year
Out[52]: 
one      2000
two      2001
three    2002
four     2001
five     2002
six      2003
Name: year, dtype: int64
```

注： frame2[column]<font color = 'red'>适用于任何列的名</font>，但是frame2.column只有在<font color = "red">列名是一个合理的Python变量名</font>时才适用。

### 用loc获取行

```
In [53]: frame2.loc['three']
Out[53]: 
year     2002
state    Ohio
pop       3.6
Name: three, dtype: object
```

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240304061850402.png)

### 给缺失值的列赋值

#### 赋同一个值

```python
In [54]: frame2['debt'] = 16.5

In [55]: frame2
Out[55]: 
       year   state  pop  debt
one    2000    Ohio  1.5  16.5
two    2001    Ohio  1.7  16.5
three  2002    Ohio  3.6  16.5
four   2001  Nevada  2.4  16.5
five   2002  Nevada  2.9  16.5
six    2003  Nevada  3.2  16.5
```

#### 赋不同的值

```python
In [56]: frame2['debt'] = np.arange(6.)

In [57]: frame2
Out[57]: 
       year   state  pop  debt
one    2000    Ohio  1.5   0.0
two    2001    Ohio  1.7   1.0
three  2002    Ohio  3.6   2.0
four   2001  Nevada  2.4   3.0
five   2002  Nevada  2.9   4.0
six    2003  Nevada  3.2   5.0
```

注：将列表或数组赋值给某个列时，其长度必须跟DataFrame的长度相匹配。

### 赋值的是一个Series，就会精确匹配DataFrame的索引，所有的空位都将被填上缺失值：

```python
In [58]: val = pd.Series([-1.2, -1.5, -1.7], index=['two', 'four', 'five'])

In [59]: frame2['debt'] = val

In [60]: frame2
Out[60]: 
       year   state  pop  debt
one    2000    Ohio  1.5   NaN
two    2001    Ohio  1.7  -1.2
three  2002    Ohio  3.6   NaN
four   2001  Nevada  2.4  -1.5
five   2002  Nevada  2.9  -1.7
six    2003  Nevada  3.2   NaN
```

### 根据state是否为'Ohio'添加一列名为eastern的列

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

注意：不能用frame2.eastern创建新的列。

### 用del删除列

```python
In [63]: del frame2['eastern']

In [64]: frame2.columns
Out[64]: Index(['year', 'state', 'pop', 'debt'], dtype='object')
```

### 通过嵌套字典创建DataFrame，最外层会作为行，往内一层会作为列，再往里作为行与列交叉对应空格的值

```python
In [66]: frame3 = pd.DataFrame(pop)

In [67]: frame3
Out[67]: 
      Nevada  Ohio
2000     NaN   1.5
2001     2.4   1.7
2002     2.9   3.6
```

### 转置DataFrame

```python
frame3.T
```

### 截取一个DataFrame前两行的两种写法

```python
pdata = {'Ohio':frame3['Ohio'][:-1],
         'Nevada':frame3['Nevada'][:2]}
pd.DataFrame(pdata)
```

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240305063001812.png)

### 设置DataFrame的index和columns属性

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240305063455936.png)

### DataFrame的values属性

```python
In [74]: frame3.values
Out[74]: 
array([[ nan,  1.5],
       [ 2.4,  1.7],
       [ 2.9,  3.6]])
```

注：如果DataFrame各列的数据类型不同，则值数组的dtype就会选用能兼容所有列的数据类型

## 索引对象

pandas的索引对象负责管理轴标签和其他元数据（比如轴名称等）。构建Series或DataFrame时，所用到的任何数组或其他序列的标签都会被转换成一个Index：

```python
In [76]: obj = pd.Series(range(3), index=['a', 'b', 'c'])

In [77]: index = obj.index

In [78]: index
Out[78]: Index(['a', 'b', 'c'], dtype='object')

In [79]: index[1:]
Out[79]: Index(['b', 'c'], dtype='object')
```

<font color = "red">Index对象是不可变的，因此用户不能对其进行修改</font>

pandas与python不同，可以包含重复的标签

# 基本功能

## 重新索引

### 用Series的reindex会根据新索引重排，如果当前索引不存在就引入缺失值

```python
In [93]: obj2 = obj.reindex(['a', 'b', 'c', 'd', 'e'])

In [94]: obj2
Out[94]: 
a   -5.3
b    7.2
c    3.6
d    4.5
e    NaN
dtype: float64
```

### 用method进行插值处理

```python
In [95]: obj3 = pd.Series(['blue', 'purple', 'yellow'], index=[0, 2, 4])

In [96]: obj3
Out[96]: 
0      blue
2    purple
4    yellow
dtype: object

In [97]: obj3.reindex(range(6), method='ffill')
Out[97]: 
0      blue
1      blue
2    purple
3    purple
4    yellow
5    yellow
dtype: object
```

### DataFrame中使用reindex，修改行索引

```python
In [98]: frame = pd.DataFrame(np.arange(9).reshape((3, 3)),
   ....:                      index=['a', 'c', 'd'],
   ....:                      columns=['Ohio', 'Texas', 'California'])

In [99]: frame
Out[99]: 
   Ohio  Texas  California
a     0      1           2
c     3      4           5
d     6      7           8

In [100]: frame2 = frame.reindex(['a', 'b', 'c', 'd'])

In [101]: frame2
Out[101]: 
   Ohio  Texas  California
a   0.0    1.0         2.0
b   NaN    NaN         NaN
c   3.0    4.0         5.0
d   6.0    7.0         8.0
```

### DataFrame使用columns修改列索引

```python
In [102]: states = ['Texas', 'Utah', 'California']

In [103]: frame.reindex(columns=states)
Out[103]: 
   Texas  Utah  California
a      1   NaN           2
c      4   NaN           5
d      7   NaN           8
```

## 丢弃指定轴上的项

### Series中使用Drop方法返回在指定轴上删除指定值的新对象

```python
In [105]: obj = pd.Series(np.arange(5.), index=['a', 'b', 'c', 'd', 'e'])

In [106]: obj
Out[106]: 
a    0.0
b    1.0
c    2.0
d    3.0
e    4.0
dtype: float64

In [107]: new_obj = obj.drop('c')

In [108]: new_obj
Out[108]: 
a    0.0
b    1.0
d    3.0
e    4.0
dtype: float64

In [109]: obj.drop(['d', 'c'])
Out[109]: 
a    0.0
b    1.0
e    4.0
dtype: float64
```

```python
In [110]: data = pd.DataFrame(np.arange(16).reshape((4, 4)),
   .....:                     index=['Ohio', 'Colorado', 'Utah', 'New York'],
   .....:                     columns=['one', 'two', 'three', 'four'])

In [111]: data
Out[111]: 
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7
Utah        8    9     10    11
New York   12   13     14    15
```

### DataFrame用标签序列调用drop会从行标签（axis 0）删除值：

```python
In [112]: data.drop(['Colorado', 'Ohio'])
Out[112]: 
          one  two  three  four
Utah        8    9     10    11
New York   12   13     14    15
```

### DataFrame通过传递axis=1或axis='columns'可以删除列的值：

```python
In [113]: data.drop('two', axis=1)
Out[113]: 
          one  three  four
Ohio        0      2     3
Colorado    4      6     7
Utah        8     10    11
New York   12     14    15

In [114]: data.drop(['two', 'four'], axis='columns')
Out[114]: 
          one  three
Ohio        0      2
Colorado    4      6
Utah        8     10
New York   12     14
```

### 用inplace就地修改不返回新对象

```python
In [115]: obj.drop('c', inplace=True)

In [116]: obj
Out[116]: 
a    0.0
b    1.0
d    3.0
e    4.0
dtype: float64
```

##  索引、选取和过滤

### Series索引，类似numpy索引，可以切片,但是这个切片是包含末端的，还可以索引的对象不是整数而是字符

```python
In [117]: obj = pd.Series(np.arange(4.), index=['a', 'b', 'c', 'd'])

In [118]: obj
Out[118]: 
a    0.0
b    1.0
c    2.0
d    3.0
dtype: float64

In [119]: obj['b']
Out[119]: 1.0

In [120]: obj[1]
Out[120]: 1.0

In [121]: obj[2:4]
Out[121]: 
c    2.0
d    3.0
dtype: float64

In [122]: obj[['b', 'a', 'd']]
Out[122]:
b    1.0
a    0.0
d    3.0
dtype: float64

In [123]: obj[[1, 3]]
Out[123]: 
b    1.0
d    3.0
dtype: float64

In [124]: obj[obj < 2]
Out[124]: 
a    0.0
b    1.0
dtype: float64
```

### 用切片可以对Series的相应部分进行设置

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240308054828881.png)

### 通过一个值对DataFrame进行索引

```python
In [128]: data = pd.DataFrame(np.arange(16).reshape((4, 4)),
   .....:                     index=['Ohio', 'Colorado', 'Utah', 'New York'],
   .....:                     columns=['one', 'two', 'three', 'four'])

In [129]: data
Out[129]: 
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7
Utah        8    9     10    11
New York   12   13     14    15

In [130]: data['two']
Out[130]: 
Ohio         1
Colorado     5
Utah         9
New York    13
Name: two, dtype: int64
```

### 通过一个序列对DataFrame索引，主要要套两层[]

```python
In [128]: data = pd.DataFrame(np.arange(16).reshape((4, 4)),
   .....:                     index=['Ohio', 'Colorado', 'Utah', 'New York'],
   .....:                     columns=['one', 'two', 'three', 'four'])

In [129]: data
Out[129]: 
          one  two  three  four
Ohio        0    1      2     3
Colorado    4    5      6     7
Utah        8    9     10    11
New York   12   13     14    15
In [131]: data[['three', 'one']]
Out[131]: 
          three  one
Ohio          2    0
Colorado      6    4
Utah         10    8
New York     14   12
```

### 通过切片索引选取行

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240308055742644.png)

### 通过在列基础上条件判断索引

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240308060054604.png)

### 通过对整体条件判断

```python
In [134]: data < 5
Out[134]: 
            one    two  three   four
Ohio       True   True   True   True
Colorado   True  False  False  False
Utah      False  False  False  False
New York  False  False  False  False

In [135]: data[data < 5] = 0

In [136]: data
Out[136]: 
          one  two  three  four
Ohio        0    0      0     0
Colorado    0    5      6     7
Utah        8    9     10    11
New York   12   13     14    15
```

## 用loc和iloc从DataFrame选择行和列的子集。

### 使用loc直接选取轴名称

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240308060814326.png)

### 使用iloc通过轴下标选取

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/31c156b894c83af0378386730f25873.jpg)

### 切片获取标签

```python
In [141]: data.loc[:'Utah', 'two']
Out[141]: 
Ohio        0
Colorado    5
Utah        9
Name: two, dtype: int64
In [142]: data.iloc[:, :3][data.three > 5]
Out[142]: 
          one  two  three
Colorado    0    5      6
Utah        8    9     10
New York   12   13     14
```

##  算术运算和数据对齐

### Series的数据对齐

```python
In [150]: s1 = pd.Series([7.3, -2.5, 3.4, 1.5], index=['a', 'c', 'd', 'e'])

In [151]: s2 = pd.Series([-2.1, 3.6, -1.5, 4, 3.1],
   .....:                index=['a', 'c', 'e', 'f', 'g'])

In [152]: s1
Out[152]: 
a    7.3
c   -2.5
d    3.4
e    1.5
dtype: float64

In [153]: s2
Out[153]: 
a   -2.1
c    3.6
e   -1.5
f    4.0
g    3.1
dtype: float64
In [154]: s1 + s2
Out[154]: 
a    5.2
c    1.1
d    NaN
e    0.0
f    NaN
g    NaN
dtype: float64
```

### DataFrame的对齐

先取行标签和列标签的并集，然后两个DataFrame对象公共部分才会保留并运算，如果两个都没有或者只有1个有都是认为NAN

```python
In [155]: df1 = pd.DataFrame(np.arange(9.).reshape((3, 3)), columns=list('bcd'),
   .....:                    index=['Ohio', 'Texas', 'Colorado'])

In [156]: df2 = pd.DataFrame(np.arange(12.).reshape((4, 3)), columns=list('bde'),
   .....:                    index=['Utah', 'Ohio', 'Texas', 'Oregon'])

In [157]: df1
Out[157]: 
            b    c    d
Ohio      0.0  1.0  2.0
Texas     3.0  4.0  5.0
Colorado  6.0  7.0  8.0

In [158]: df2
Out[158]: 
          b     d     e
Utah    0.0   1.0   2.0
Ohio    3.0   4.0   5.0
Texas   6.0   7.0   8.0
Oregon  9.0  10.0  11.0
In [159]: df1 + df2
Out[159]: 
            b   c     d   e
Colorado  NaN NaN   NaN NaN
Ohio      3.0 NaN   6.0 NaN
Oregon    NaN NaN   NaN NaN
Texas     9.0 NaN  12.0 NaN
Utah      NaN NaN   NaN NaN
```

### 在算术方法中填充值

使用df1的add方法，传入df2以及一个fill_value参数，可以让原本产生NaN的值得到指定的值

```python
In [165]: df1 = pd.DataFrame(np.arange(12.).reshape((3, 4)),
   .....:                    columns=list('abcd'))

In [166]: df2 = pd.DataFrame(np.arange(20.).reshape((4, 5)),
   .....:                    columns=list('abcde'))

In [167]: df2.loc[1, 'b'] = np.nan

In [168]: df1
Out[168]: 
     a    b     c     d
0  0.0  1.0   2.0   3.0
1  4.0  5.0   6.0   7.0
2  8.0  9.0  10.0  11.0

In [169]: df2
Out[169]: 
      a     b     c     d     e
0   0.0   1.0   2.0   3.0   4.0
1   5.0   NaN   7.0   8.0   9.0
2  10.0  11.0  12.0  13.0  14.0
3  15.0  16.0  17.0  18.0  19.0
In [170]: df1 + df2
Out[170]: 
      a     b     c     d   e
0   0.0   2.0   4.0   6.0 NaN
1   9.0   NaN  13.0  15.0 NaN
2  18.0  20.0  22.0  24.0 NaN
3   NaN   NaN   NaN   NaN NaN
In [171]: df1.add(df2, fill_value=0)
Out[171]: 
      a     b     c     d     e
0   0.0   2.0   4.0   6.0   4.0
1   9.0   5.0  13.0  15.0   9.0
2  18.0  20.0  22.0  24.0  14.0
3  15.0  16.0  17.0  18.0  19.0
```

### DataFrame和Series之间的运算

#### 广播

从arr减去arr[0]，每一行都会执行这个操作	

```python
In [175]: arr = np.arange(12.).reshape((3, 4))

In [176]: arr
Out[176]: 
array([[  0.,   1.,   2.,   3.],
       [  4.,   5.,   6.,   7.],
       [  8.,   9.,  10.,  11.]])

In [177]: arr[0]
Out[177]: array([ 0.,  1.,  2.,  3.])

In [178]: arr - arr[0]
Out[178]: 
array([[ 0.,  0.,  0.,  0.],
       [ 4.,  4.,  4.,  4.],
       [ 8.,  8.,  8.,  8.]])
```

默认情况下，DataFrame和Series之间的算术运算会将Series的索引匹配到DataFrame的列，然后沿着行一直向下广播：

```python
In [179]: frame = pd.DataFrame(np.arange(12.).reshape((4, 3)),
   .....:                      columns=list('bde'),
   .....:                      index=['Utah', 'Ohio', 'Texas', 'Oregon'])

In [180]: series = frame.iloc[0]

In [181]: frame
Out[181]: 
          b     d     e
Utah    0.0   1.0   2.0
Ohio    3.0   4.0   5.0
Texas   6.0   7.0   8.0
Oregon  9.0  10.0  11.0

In [182]: series
Out[182]: 
b    0.0
d    1.0
e    2.0
Name: Utah, dtype: float64
In [183]: frame - series
Out[183]: 
          b    d    e
Utah    0.0  0.0  0.0
Ohio    3.0  3.0  3.0
Texas   6.0  6.0  6.0
Oregon  9.0  9.0  9.0
```

如果某个索引值在DataFrame的列或Series的索引中找不到，则参与运算的两个对象就会被重新索引以形成并集：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@master/image-20240311063147918.png)

传入的轴号就是希望匹配的轴。在本例中，我们的目的是匹配DataFrame的行索引（axis='index' or axis=0）并进行广播。

### 排序和排名
#### 排序

##### sort_index

在Series中

```python
In [201]: obj = pd.Series(range(4), index=['d', 'a', 'b', 'c'])

In [202]: obj.sort_index()
Out[202]:
a    1
b    2
c    3
d    0
dtype: int64
```

在DataFrame中

```python
In [203]: frame = pd.DataFrame(np.arange(8).reshape((2, 4)),
   .....:                      index=['three', 'one'],
   .....:                      columns=['d', 'a', 'b', 'c'])

In [204]: frame.sort_index()
Out[204]: 
       d  a  b  c
one    4  5  6  7
three  0  1  2  3

In [205]: frame.sort_index(axis=1)
Out[205]:
       a  b  c  d
three  1  2  3  0
one    5  6  7  4
```

降序

```python
In [206]: frame.sort_index(axis=1, ascending=False)
Out[206]: 
       d  c  b  a
three  0  3  2  1
one    4  7  6  5
```

##### sort_values

```python
In [207]: obj = pd.Series([4, 7, -3, 2])

In [208]: obj.sort_values()
Out[208]: 
2   -3
3    2
0    4
1    7
dtype: int64
```

在排序时，任何缺失值默认都会被放到Series的末尾：

根据某一列排序

```python
In [211]: frame = pd.DataFrame({'b': [4, 7, -3, 2], 'a': [0, 1, 0, 1]})

In [212]: frame
Out[212]: 
   a  b
0  0  4
1  1  7
2  0 -3
3  1  2

In [213]: frame.sort_values(by='b')
Out[213]: 
   a  b
2  0 -3
3  1  2
0  0  4
1  1  7
```

根据多个列进行排序，传入名称的列表即可：

```python
In [214]: frame.sort_values(by=['a', 'b'])
Out[214]: 
   a  b
2  0 -3
0  0  4
3  1  2
1  1  7
```

#### 排名

##### 按“average”排名

相同值的元素将被分配平均排名。例如，如果有n个元素并列，那么它们的排名将是：并列首行所在的行数+0.5*(n-1)。

举个例子：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/3320338d6e68533862a5af71232b456.jpg)

##### 按“first”排名

据出现顺序分配排名，按从小到大，排名等于行所在的行数。

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/62e313c1e197c77cf6b54978175b713.jpg)

##### max排名

相同值的元素将被分配最高排名。例如，如果有n个元素并列，那么它们的排名将都是最后一个元素所在的行数。

##### DataFrame可以在行或列上计算排名：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240319062648290.png)

### 带有重复标签的轴索引

## 汇总和计算描述统计

调用DataFrame的sum方法将会返回一个含有列的和的Series：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240320054354757.png)

传入axis='columns'或axis=1将会按行进行求和运算：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240320055013896.png)

NA值会自动被排除，除非整个切片（这里指的是行或列）都是NA。通过skipna选项可以禁用该功能：

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240320055248960.png)

求最大值与最小值的索引

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240320055957031.png)

累积求和

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/image-20240320060151721.png)

到b那一层就是b = b + a

到c那一层就是c = c + b + a

### 多个股票代码的demo

#### download模块。

举例：一次性获取 Amazon、Apple 和 Google 的数据

```python
data = yf.download("AMZN AAPL GOOG", start="2017-01-01", end="2017-04-30")
data
```

输出

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/v2-ef7734b928680d5172e82b910cba68f3_720w.webp)

按股票代码而不是开盘价/最高价/最低价/收盘价进行分组

```python
data = yf.download("AMZN AAPL GOOG", start="2017-01-01",
                    end="2017-04-30", group_by='tickers')
data
```

输出

![](https://cdn.jsdelivr.net/gh/pilotztb/myBlog_img@main/v2-359419adff58f22c6e0c1f2b4e6ef95e_720w.webp)

#### 市盈率

```python
aapl = yf.Ticker("aapl")
aapl.info['forwardPE']
```

1. `yf.Ticker("aapl")` 创建了一个 `Ticker` 对象，该对象表示了苹果公司的股票。
2. `aapl.info` 属性返回一个字典，包含了苹果公司股票的各种信息，如公司名称、市值、市盈率等。
3. `aapl.info['forwardPE']` 从返回的字典中获取了键为 `'forwardPE'` 的值，该值代表了苹果公司的预测市盈率（forward PE ratio）。

输出

```
24.95391
```

#### 股息

年度股息百分比：

```python
aapl.info['dividendRate']
```

输出

```
0.96
```

在发生的日期对每笔股息支付进行细分

输出

```
Date
1987-05-11 00:00:00-04:00    0.000536
1987-08-10 00:00:00-04:00    0.000536
1987-11-17 00:00:00-05:00    0.000714
1988-02-12 00:00:00-05:00    0.000714
1988-05-16 00:00:00-04:00    0.000714
                               ...   
2023-02-10 00:00:00-05:00    0.230000
2023-05-12 00:00:00-04:00    0.240000
2023-08-11 00:00:00-04:00    0.240000
2023-11-10 00:00:00-05:00    0.240000
2024-02-09 00:00:00-05:00    0.240000
Name: Dividends, Length: 82, dtype: float64
```

### 相关系数和协方差

```python
# 获取股票数据
all_data = {ticker: yf.Ticker(ticker).history(period='max')
            for ticker in ['AAPL', 'IBM', 'MSFT', 'GOOG']}

# 创建价格 DataFrame
price = pd.DataFrame({ticker: data['Close']
                      for ticker, data in all_data.items()})

# 创建成交量 DataFrame
volume = pd.DataFrame({ticker: data['Volume']
                       for ticker, data in all_data.items()})
```

相关系数

Series的corr方法用于计算两个Series中重叠的、非NA的、按索引对齐的值的相关系数

```python
In [244]: returns['MSFT'].corr(returns['IBM'])
Out[244]: 0.49976361144151144
```

协方差

cov用于计算协方差

```python
In [245]: returns['MSFT'].cov(returns['IBM'])
Out[245]: 8.8706554797035462e-05
```

DataFrame的corr和cov方法将以DataFrame的形式分别返回完整的相关系数或协方差矩阵：

```python
In [247]: returns.corr()
Out[247]: 
          AAPL      GOOG       IBM      MSFT
AAPL  1.000000  0.407919  0.386817  0.389695
GOOG  0.407919  1.000000  0.405099  0.465919
IBM   0.386817  0.405099  1.000000  0.499764
MSFT  0.389695  0.465919  0.499764  1.000000

In [248]: returns.cov()
Out[248]: 
          AAPL      GOOG       IBM      MSFT
AAPL  0.000277  0.000107  0.000078  0.000095
GOOG  0.000107  0.000251  0.000078  0.000108
IBM   0.000078  0.000078  0.000146  0.000089
MSFT  0.000095  0.000108  0.000089  0.000215
```

利用DataFrame的corrwith方法，你可以计算其列或行跟另一个Series或DataFrame之间的相关系数。传入一个Series将会返回一个相关系数值Series（针对各列进行计算）：

```
In [249]: returns.corrwith(returns.IBM)
Out[249]: 
AAPL    0.386817
GOOG    0.405099
IBM     1.000000
MSFT    0.499764
dtype: float64
```

传入一个DataFrame则会计算按列名配对的相关系数。这里，我计算百分比变化与成交量的相关系数：

```
In [250]: returns.corrwith(volume)
Out[250]: 
AAPL   -0.075565
GOOG   -0.007067
IBM    -0.204849
MSFT   -0.092950

dtype: float64
```

传入axis='columns'即可按行进行计算。无论如何，在计算相关系数之前，所有的数据项都会按标签对齐。

### 唯一值、值计数以及成员资格

#### unique

它可以得到Series中的唯一值数组：

```
In [251]: obj = pd.Series(['c', 'a', 'd', 'a', 'a', 'b', 'b', 'c', 'c'])
In [252]: uniques = obj.unique()

In [253]: uniques
Out[253]: array(['c', 'a', 'd', 'b'], dtype=object)
```

#### value_counts

```
In [254]: obj.value_counts()
Out[254]: 
c    3
a    3
b    2
d    1
dtype: int64
```

#### isin

用于判断矢量化集合的成员资格，可用于过滤Series中或DataFrame列中数据的子集：

```
In [256]: obj
Out[256]: 
0    c
1    a
2    d
3    a
4    a
5    b
6    b
7    c
8    c
dtype: object

In [257]: mask = obj.isin(['b', 'c'])

In [258]: mask
Out[258]: 
0     True
1    False
2    False
3    False
4    False
5     True
6     True
7     True
8     True
dtype: bool

In [259]: obj[mask]
Out[259]: 
0    c
5    b
6    b
7    c
8    c
dtype: object
```

####  Index.get_indexer

给你一个索引数组，从可能包含重复值的数组到另一个不同值的数组：

```
In [260]: to_match = pd.Series(['c', 'a', 'b', 'b', 'c', 'a'])

In [261]: unique_vals = pd.Series(['c', 'b', 'a'])

In [262]: pd.Index(unique_vals).get_indexer(to_match)
Out[262]: array([0, 2, 1, 1, 0, 2])
```

#### apply

产生柱状图

```
In [265]: result = data.apply(pd.value_counts).fillna(0)

In [266]: result
Out[266]: 
   Qu1  Qu2  Qu3
1  1.0  1.0  1.0
2  0.0  2.0  1.0
3  2.0  2.0  0.0
4  2.0  0.0  2.0
5  0.0  0.0  1.0
```

结果中的行标签是所有列的唯一值。后面的频率值是每个列中这些值的相应计数
