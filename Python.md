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
#### 输出csv文件
```python
df.to_csv("path/to/file/file.csv")
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

### 直接索引

```python
df.country
df['country']
# 先列后行，先serial再取元素
df['country']['index']
```

### Indexing in pandas

iloc按照index寻找某一列，某一行，也可以某一个元素，和python对2维数组切片一样

```python
# 按照2维数组的访问顺序，先行后列
df.iloc[0]
df.iloc[:,0]
df.iloc[[0,2,3],0]
```

loc按照名字来寻找

```python
# 按照2维数组的访问顺序，先行后列，行的名字，再列的名字
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

# Numpy




# Matplotlib

[快速入门](https://matplotlib.org/stable/users/explain/quick_start.html)

![[240930-171757794-Pasted image 20240930171756.png]]

## figure，axes

每个figure管理其中的全部axes
其中**subplot_mosaic**可以实现更复杂的布局

```python
fig = plt.figure()             # an empty figure with no Axes
fig, ax = plt.subplots()       # a figure with a single Axes
fig, axs = plt.subplots(2, 2)  # a figure with a 2x2 grid of Axes
# a figure with one Axes on the left, and two on the right:
fig, axs = plt.subplot_mosaic([['left', 'right_top'],
                               ['left', 'right_bottom']])
```

fig管理axes

```python
fig.subplots()
fig.add_subplot(nrows, ncols, index)
fig.delaxes(ax)
fig.get_axes()
```

## label

可以通过axes的方法设置标题, x-label, y-label

```python
ax.set_title("title")
ax.set_xlabel("xlabel")
ax.set_ylabel("ylabel")
```


## Styling Artists

### color

对于scatter，边缘内部都有颜色设定

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.scatter(data1, data2, s=50, facecolor='C0', edgecolor='k')
```

### linestyle

可以在plot中设定，也可以随后设定

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
x = np.arange(len(data1))
ax.plot(x, np.cumsum(data1), color='blue', linewidth=3, linestyle='--')
l, = ax.plot(x, np.cumsum(data2), color='orange', linewidth=2)
l.set_linestyle(':')
```

### Linewidths, linestyles, and markersizes



```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.plot(data1, 'o', label='data1')
ax.plot(data2, 'd', label='data2')
ax.plot(data3, 'v', label='data3')
ax.plot(data4, 's', label='data4')
ax.legend()
```


### 标记文本

可以使用text在文本上标记
更详细text信息参考[Text in Matplotlib](https://matplotlib.org/stable/users/explain/text/text_intro.html)

```python
mu, sigma = 115, 15
x = mu + sigma * np.random.randn(10000)
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
# the histogram of the data
n, bins, patches = ax.hist(x, 50, density=True, facecolor='C0', alpha=0.75)

ax.set_xlabel('Length [cm]')
ax.set_ylabel('Probability')
ax.set_title('Aardvark lengths\n (not really)')
ax.text(75, .025, r'$\mu=115,\ \sigma=15$')
ax.axis([55, 175, 0, 0.03])
ax.grid(True)
```

### 注释

使用annotate可以使用有箭头的标注

```python
fig, ax = plt.subplots(figsize=(5, 2.7))

t = np.arange(0.0, 5.0, 0.01)
s = np.cos(2 * np.pi * t)
line, = ax.plot(t, s, lw=2)

ax.annotate('local max', xy=(2, 1), xytext=(3, 1.5),
            arrowprops=dict(facecolor='black', shrink=0.05))

ax.set_ylim(-2, 2)
```

### legend

可以使用legend加入图例

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.plot(np.arange(len(data1)), data1, label='data1')
ax.plot(np.arange(len(data2)), data2, label='data2')
ax.plot(np.arange(len(data3)), data3, 'd', label='data3')
ax.legend()
```

## Axis scales and ticks

### Scales

处理线性标度，还有非线性标度
更多参考[Scales](https://matplotlib.org/stable/gallery/scales/index.html)

```python
fig, axs = plt.subplots(1, 2, figsize=(5, 2.7), layout='constrained')
xdata = np.arange(len(data1))  # make an ordinal for this
data = 10**data1
axs[0].plot(xdata, data)

axs[1].set_yscale('log')
axs[1].plot(xdata, data)
```

### Tick locators and formatters

可以对ax的刻度进行标注，不同scale的ax有不同的标注器

```python
fig, axs = plt.subplots(2, 1, layout='constrained')
axs[0].plot(xdata, data1)
axs[0].set_title('Automatic ticks')

axs[1].plot(xdata, data1)
axs[1].set_xticks(np.arange(0, 100, 30), ['zero', '30', 'sixty', '90'])
axs[1].set_yticks([-1.5, 0, 1.5])  # note that we don't need to specify labels
axs[1].set_title('Manual ticks')
```

### Additional Axis objects

额外的坐标轴

```python
fig, (ax1, ax3) = plt.subplots(1, 2, figsize=(7, 2.7), layout='constrained')
l1, = ax1.plot(t, s)
ax2 = ax1.twinx()
l2, = ax2.plot(t, range(len(t)), 'C1')
ax2.legend([l1, l2], ['Sine (left)', 'Straight (right)'])

ax3.plot(t, s)
ax3.set_xlabel('Angle [rad]')
ax4 = ax3.secondary_xaxis('top', functions=(np.rad2deg, np.deg2rad))
ax4.set_xlabel('Angle [°]')
```

## Color mapped data

颜色映射第三维度数据

```python
from matplotlib.colors import LogNorm

X, Y = np.meshgrid(np.linspace(-3, 3, 128), np.linspace(-3, 3, 128))
Z = (1 - X/2 + X**5 + Y**3) * np.exp(-X**2 - Y**2)

fig, axs = plt.subplots(2, 2, layout='constrained')
pc = axs[0, 0].pcolormesh(X, Y, Z, vmin=-1, vmax=1, cmap='RdBu_r')
fig.colorbar(pc, ax=axs[0, 0])
axs[0, 0].set_title('pcolormesh()')

co = axs[0, 1].contourf(X, Y, Z, levels=np.linspace(-1.25, 1.25, 11))
fig.colorbar(co, ax=axs[0, 1])
axs[0, 1].set_title('contourf()')

pc = axs[1, 0].imshow(Z**2 * 100, cmap='plasma', norm=LogNorm(vmin=0.01, vmax=100))
fig.colorbar(pc, ax=axs[1, 0], extend='both')
axs[1, 0].set_title('imshow() with LogNorm()')

pc = axs[1, 1].scatter(data1, data2, c=data3, cmap='RdBu_r')
fig.colorbar(pc, ax=axs[1, 1], extend='both')
axs[1, 1].set_title('scatter()')
```

![[240930-171718595-Pasted image 20240930171716.png]]


# Python 原生方法

## Python魔法函数

参考[Python：实例讲解Python中的魔法函数(高级语法)](https://zhuanlan.zhihu.com/p/344951719)

## Python函数重载

[使用functools.singledispatch在Python中实现函数重载](https://cloud.tencent.com/developer/article/2263948)

## 可迭代对象

[Python详解可迭代对象（Iterable）、序列（Sequence）、迭代器（Iterator）、生成器（generator）](https://blog.csdn.net/qq_23981335/article/details/105110398)

# Conda

## 初始化

```bash
conda init [your shell]
```

## 克隆环境

[Conda虚拟环境的复制和迁移](https://blog.csdn.net/guigenyi/article/details/130404653)

```python
conda create -n newenv --clone oldenv
```
## 删除环境

```python
conda remove -n myenv --all
```



# Pytorch

## 交换tensor的维度

[pytorch：交换tensor的维度](https://blog.csdn.net/Caesar6666/article/details/109824498)

```python
import torch
tensor = torch.randn(8,3,5,4)
y = tensor.transpose(1,2) # 只能交换两维度
z = tensor.permute(1,0,3,2) # 可以任意排列维度
```


# Pip

有时候下载太慢了，而且没换源，可以临时使用国内的清华源
```bash
pip install -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple some-package
```
