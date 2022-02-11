# Pandas

[TOC]

## 数据对象

Pandas主要有两种数据对象：

- Series: 带有索引的序列对象
- DataFrame: 类似与数据库table有行列的数据对象。

**Series的索引类似于每个元素, DataFrame的索引对应着每一行。**

### Series

#### 初始化

```python
# 通过传入一个序列给pd.Series初始化一个Series对象, 比如list
s1 = pd.Series(['1', '2', '3', '4'])
print(s1)
------------------------------------
0    1
1    2
2    3
3    4
dtype:object
------------------------------------
```

###DataFrame

#### 初始化

```python
# 通过传入一个numpy的二维数组初始化一个DataFrame对象
df1 = pd.DataFrame(np.random.randn(6, 4))
print(df1)
------------------------------------
          0         1         2         3
0 -1.088847  0.061343  0.726717 -0.728613
1  0.841443 -1.909181 -0.946675  1.570772
2  0.663304 -0.537591 -0.853323  1.072097
3 -0.109878 -0.885990  0.364150  1.158230
4 -0.761403  0.686631  0.317446 -0.273890
5  0.677503  0.043771  0.243795 -0.947072
------------------------------------

# 通过dict字典
df2 = pd.DataFrame({'A': 1.,
                    'B': pd.Timestamp('20130102'),
                    'C': pd.Series(1, index=list(range(4)), dtype='float32'),
                    'D': np.array([3] * 4, dtype='int32'),
                    'E': pd.Categorical(["test", "train", "test", "train"]),
                    'F': 'foo'})
print(df2)
------------------------------------
     A          B    C  D      E    F
0  1.0 2013-01-02  1.0  3   test  foo
1  1.0 2013-01-02  1.0  3  train  foo
2  1.0 2013-01-02  1.0  3   test  foo
3  1.0 2013-01-02  1.0  3  train  foo
------------------------------------
```

#### Axis 图解

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/RAwjyr0lc0KlXWp5qZETdGibrDuBUYgPlVRBEQ380aplEhibDWzIqHF00cD785pkdaQQmeIMZTPg9e3tzBAL9x0g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 操作

##### 查询

- **简单查询**

  ```python
  # 查看df数据类型
  df.dtypes
  
  # 查看前5行
  df.head()
  
  # 查看前10行
  df.head(10)
  
  # 查看 10到20行
  df[10:21]
  
  # 查看看Date列前5个数据
  df["date"].head() # 或者df.date.head()
  
  # 查看看Date列,code列, open列前5个数据
  df[["date","code", "open"]].head()
  
  # 查看后5行
  df.tail()
  ```

- **行列组合条件查询**

  ```python
  # 查看date, code列的第10行
  df.loc[10, ["date", "code"]]
  
  # 查看date, code列的第10行到20行
  df.loc[10:20, ["date", "code"]]
  ```

- **通过索引查询**

  ```python
  # 查看第1行
  df.iloc[0]
  
  # 查看最后1行
  df.iloc[-1]
  
  # 查看第1列，前5个数值
  df.iloc[:,0].head()
  
  # 查看前2到4行，第1，3列
  df.iloc[2:4, [0,2]]
  ```

- **通过条件筛选**

  ```python
  # 查看open列大于10的前5行
  df[df.open > 10].head()
  
  # 查看open列大于10且open列小于10.6的前五行
  df[(df.open > 10) & (df.open < 10.6)].head()
  
  # 查看open列大于10或open列小于10.6的前五行
  df[(df.open > 10) | (df.open < 10.6)].head()
  ```

##### 修改

```python
# 将df的索引修改为date列的数据，并且将类型转换为datetime类型
df.index = pd.to_datetime(df.date)

# 修改列的字段
df.columns = ["Date", "Open", "Close", "High", "Low", "Volume", "Code"]

# 将Open列每个数值加1，apply方法并不直接修改源数据，所以需要将新值复制给df
df.Open = df.Open.apply(lambda x: x+1)

# 将Open，Close列前五条数据的数值都加1，如果多列，apply接收的对象是整个列
df[["Open", "Close"]].head().apply(lambda x: x.apply(lambda x: x+1))
```

##### 删除

通过drop方法drop指定的行或者列。drop方法并不直接修改源数据，如果需要使源dataframe对象被修改，需要传入inplace=True。

```python
# 删除指定列, 删除Open列
df.drop("Open", axis=1).head() #或者df.drop(df.columns[1]) 
```

## 常用函数

### 统计

```python
# descibe方法会计算每列数据对象是数值的count, mean, std, min, max, 以及一定比率的值
df.describe()
------------------------------------
Open    Close   High    Low Volume
count   641.0000    641.0000    641.0000    641.0000    641.0000
mean    10.7862 9.7927  9.8942  9.6863  833968.6162
std 		1.5962  1.6021  1.6620  1.5424  607731.6934
min 		8.6580  7.6100  7.7770  7.4990  153901.0000
25% 		9.7080  8.7180  8.7760  8.6500  418387.0000
50% 		10.0770 9.0960  9.1450  8.9990  627656.0000
75% 		11.8550 10.8350 10.9920 10.7270 1039297.0000
max 		15.9090 14.8600 14.9980 14.4470 4262825.0000
------------------------------------

# 单独统计Open列的平均值
df.Open.mean()

# 查看居于95%的值, 默认线性拟合
df.Open.quantile(0.95)

# 查看Open列每个值出现的次数
df.Open.value_counts().head()
```

- **count：**计数，这一组数据中包含数据的个数
- **mean：**平均值，这一组数据的平均值
- **std：**标准差，这一组数据的标准差
- **min：**最小值
- **num%：**百分位数
- **max：**最大值

### 缺失值处理

```python
# 删除含有NaN的任意行
df.dropna(how='any')

# 删除含有NaN的任意列
df.dropna(how='any', axis=1)

# 将NaN的值改为5
df.fillna(value=5)
```

### 排序

```python
# 按列排序
df.sort_index(axis=1).head()

# 按行排序，降序（ase=False）
df.sort_index(ascending=False).head()

# 按照Open列的值从小到大排序
df.sort_values(by="Open")
```

### 合并

```python
# 分别取0到2行，2到4行，4到9行组成一个列表，通过concat方法按照axis=0，行方向合并, axis参数不指定，默认为0
split_rows = [df.iloc[0:2,:],
              df.iloc[2:4,:], 
              df.iloc[4:9]]
pd.concat(split_rows)

# 分别取2到3列，3到5列，5列及以后列数组成一个列表，通过concat方法按照axis=1，列方向合并
split_columns = [df.iloc[:,1:2], 
                 df.iloc[:,2:4], 
                 df.iloc[:,4:]]
pd.concat(split_columns, axis=1).head()

# 将第一行追加到最后一行
df.append(df.iloc[0,:], ignore_index=True).tail()
```

### 复制

由于dataframe是引用对象，所以需要显示调用copy方法用以复制整个dataframe对象。

## 可视化

pandas的绘图是使用matplotlib

