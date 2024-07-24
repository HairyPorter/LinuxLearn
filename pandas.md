# Pandas

参考[kaggle的pandas教程](https://www.kaggle.com/code/residentmario/creating-reading-and-writing)

## Creating, Reading and Writing

数据处理的基本单位是Series, DataFrame的每一列都是一个Series

### DataFrame

最左侧是Index，默认从0开始

#### 读取csv文件

```python
df = pd.read_csv("path/to/file/file.csv")
```

#### 查看DataFrame形状

```python
df.shape
```

#### 查看前几行

```python
df.head()
```

## Indexing, Selecting & Assigning

DataFrame寻址方式

### 得到一列

```python
df.country
df['country']
```

### Indexing in pandas

iloc按照index寻找某一列，某一行，也可以某一个元素，和python对2维数组切片一样

```python
df.iloc[0]
df.iloc[:,0]
df.iloc[[0,2,3],0]
```

loc按照名字来寻找

```python
df.loc[0, 'country']
df.loc[:, ['taster_name', 'taster_twitter_handle', 'points']]
```

loc也可以条件寻找

```python
df.loc[reviews.country == 'Italy']
df.loc[(reviews.country == 'Italy') & (reviews.points >= 90)]
df.loc[reviews.country.isin(['Italy', 'France'])]
df.loc[reviews.price.notnull()]
```

增加某一index(行)

```python
df.set_index("title")
```

修改元素和2维数组操作一样
