# 尚硅谷 Pandas 学习笔记（详细整理版）

> 基于《尚硅谷大模型技术之Numpy与Pandas》整理，结合实操讲解，每个知识点附代码与运行结果。
> **本版特色**：对链式调用、多步骤组合等"复杂函数"逐层拆解，并补充易错点、记忆口诀与版本兼容说明。

---

## 使用前必读：环境与版本

- 本笔记基于 **pandas 2.0.x** 实测验证（代码可直接复制运行）。
- ⚠️ **频率代码版本差异（重要）**：pandas 2.2 起引入了新频率代码 `YE`（年）、`QE`（季）、`ME`（月），
  旧代码 `Y`/`A`、`Q`、`M` 依然可用（有弃用警告）。**pandas 2.0 及以下不支持 `YE`/`QE`/`ME`，会报 `ValueError: Invalid frequency`**。
  本笔记统一使用 2.0 兼容写法，并在相关位置标注 2.2+ 写法对照：

  | 含义 | pandas ≤ 2.0（本笔记用） | pandas ≥ 2.2 |
  |------|------------------------|--------------|
  | 年 | `"Y"` 或 `"A"` | `"YE"` |
  | 季度 | `"Q"` | `"QE"` |
  | 月末 | `"M"` | `"ME"` |
  | 季度末1月 | `"Q-JAN"` | `"QE-JAN"` |

- 绘图相关：`matplotlib`、`missingno` 需要自行安装：`pip install matplotlib missingno`。

---

## 目录

- [3.1 Pandas 简介](#31-pandas-简介)
- [3.2 Pandas 数据结构 - Series](#32-pandas-数据结构---series)
- [3.3 Pandas 数据结构 - DataFrame](#33-pandas-数据结构---dataframe)
- [3.4 Pandas 日期数据处理](#34-pandas-日期数据处理)
- [3.5 DataFrame 数据分析入门](#35-dataframe-数据分析入门)
- [3.6 Pandas 数据组合函数](#36-pandas-数据组合函数)
- [3.7 Pandas 缺失值处理](#37-pandas-缺失值处理)
- [3.8 Pandas 的 apply 函数](#38-pandas-的-apply-函数)
- [3.9 数据聚合、转换、过滤](#39-数据聚合转换过滤)
- [3.10 Pandas 透视表](#310-pandas-透视表)
- [3.11 Pandas 时间序列](#311-pandas-时间序列)
- [3.12 Matplotlib 可视化](#312-matplotlib-可视化)
- [3.13 Pandas 可视化](#313-pandas-可视化)
- [附录：核心知识点速查表](#附录核心知识点速查表)

---

## 3.1 Pandas 简介

Pandas 是一个开源的数据分析和数据处理库，基于 Python 编程语言。它提供了易于使用的数据结构和数据分析工具，特别适用于处理结构化数据（如表格型数据，类似于 Excel 表格）。

### 核心数据结构

- **Series**：一维的标签化数组对象
- **DataFrame**：面向列的二维表结构

### Pandas 功能特点

- 有标签轴的数据结构，每个轴都被赋予特定标签
- 集成时间序列功能
- 灵活处理缺失数据
- 合并和关系操作（类似 SQL）
- 复杂精细的索引功能，便捷完成重塑、切片、聚合等操作

---

## 3.2 Pandas 数据结构 - Series

Series 是 Pandas 中的一个核心数据结构，类似于一个一维的数组，具有**数据**和**索引**两部分。

### 3.2.1 Series 特点

| 特点 | 说明 |
|------|------|
| 一维数组 | 每个元素都有一个对应的索引值 |
| 索引 | 默认从 0 开始的整数，也可自定义标签索引 |
| 数据类型 | 可容纳整数、浮点数、字符串、Python 对象等 |
| 大小不变性 | 创建后大小不变，append/delete 会生成新对象 |
| 操作 | 支持数学运算、统计分析、字符串处理等 |
| 缺失数据 | 使用 NaN（Not a Number）表示缺失值 |
| 自动对齐 | 多个 Series 运算时，自动根据索引对齐数据 |

### 3.2.2 Series 的创建

#### 方式1：通过列表创建（默认索引）

```python
import pandas as pd

s = pd.Series([4, 7, -5, 3])
print(s)
```

运行结果：
```
0    4
1    7
2   -5
3    3
dtype: int64
```

> 索引在左边，值在右边。未指定索引时，自动创建 0 到 N-1 的整数索引。

#### 方式2：通过列表创建并指定索引

```python
s = pd.Series([4, 7, -5, 3], index=["a", "b", "c", "d"])
print(s)
```

运行结果：
```
a    4
b    7
c   -5
d    3
dtype: int64
```

#### 方式3：指定索引和名称

```python
s = pd.Series([4, 7, -5, 3], index=["a", "b", "c", "d"], name="hello_python")
print(s)
```

运行结果：
```
a    4
b    7
c   -5
d    3
Name: hello_python, dtype: int64
```

#### 方式4：通过字典创建

字典的 key 自动变为索引，value 变为数据：

```python
dic = {"a": 4, "b": 7, "c": -5, "d": 3}
s = pd.Series(dic)
print(s)
```

运行结果：
```
a    4
b    7
c   -5
d    3
dtype: int64
```

通过 `index` 参数筛选字典中的部分键：

```python
s1 = pd.Series(dic, index=["a", "c"], name="aacc")
print(s1)
```

运行结果：
```
a    4
c   -5
Name: aacc, dtype: int64
```

> 💡 字典没写到的键不会被取出来，`index` 相当于"点名提取"。

### 3.2.3 Series 常用属性

```python
import pandas as pd

arrs = pd.Series([11, 22, 33, 44, 55], name="atguigu", index=["a", "b", "c", "d", "e"])
```

| 属性 | 说明 | 示例代码 | 运行结果 |
|------|------|----------|----------|
| `index` | 索引对象 | `arrs.index` | `Index(['a','b','c','d','e'], dtype='object')` |
| `values` | 值数组 | `arrs.values` | `array([11, 22, 33, 44, 55])` |
| `ndim` | 维度 | `arrs.ndim` | `1` |
| `shape` | 形状 | `arrs.shape` | `(5,)` |
| `size` | 元素个数 | `arrs.size` | `5` |
| `dtype` / `dtypes` | 元素类型 | `arrs.dtype` | `int64` |
| `name` | 序列名称 | `arrs.name` | `'atguigu'` |
| `loc[]` | 按标签索引 | `arrs.loc["c"]` | `33` |
| `iloc[]` | 按位置索引 | `arrs.iloc[0]` | `11` |
| `at[]` | 标签取单个元素 | `arrs.at["a"]` | `11` |
| `iat[]` | 位置取单个元素 | `arrs.iat[3]` | `44` |

#### loc 标签切片（包含首尾）

```python
print(arrs.loc["c":"d"])
```
运行结果：
```
c    33
d    44
Name: atguigu, dtype: int64
```

#### iloc 位置切片（左闭右开）

```python
print(arrs.iloc[0:3])
```
运行结果：
```
a    11
b    22
c    33
Name: atguigu, dtype: int64
```

> ⚠️ **loc 含右端，iloc 不含右端**：`loc["c":"d"]` 取到 c、d 两个；`iloc[0:3]` 只取位置 0、1、2 三个。
> 这是全笔记出现频率最高的坑，务必记牢。

### 3.2.4 Series 常用方法

```python
import pandas as pd
import numpy as np

arrs = pd.Series([11, 22, np.nan, None, 44, 22], index=['a', 'b', 'c', 'd', 'e', 'f'])
```

#### 数据预览

```python
print(arrs.head())      # 查看前5行（默认）
print(arrs.tail(3))     # 查看后3行
```

`head()` 运行结果：
```
a    11.0
b    22.0
c     NaN
d     NaN
e    44.0
dtype: float64
```

#### 缺失值判断

```python
print(arrs.isin([11]))  # 元素是否在集合中
print(arrs.isna())      # 元素是否为缺失值
```

`isna()` 运行结果：
```
a    False
b    False
c     True
d     True
e    False
f    False
dtype: bool
```

#### 统计运算（自动忽略缺失值）

```python
print(arrs.sum())       # 求和：99.0
print(arrs.mean())      # 平均值：24.75
print(arrs.min())       # 最小值：11.0
print(arrs.max())       # 最大值：44.0
print(arrs.var())       # 方差
print(arrs.std())       # 标准差
print(arrs.median())    # 中位数：22.0
print(arrs.mode())      # 众数
```

> **中位数说明**：去除缺失值后数据为 `[11, 22, 44, 22]`，排序后 `[11, 22, 22, 44]`，偶数个取中间两个的平均值 `(22+22)/2 = 22`。

#### quantile() 分位数

```python
print(arrs.quantile(0.25, interpolation="midpoint"))
```

> **分位数原理**：有序数据 `[11, 22, 22, 44]`，n=4，q=0.25，位置 `i=(4-1)×0.25=0.75`，位于第1个(11)和第2个(22)之间，midpoint 插值取平均 `(11+22)/2=16.5`。

interpolation 可选值：
- `linear`（默认）：线性加权平均
- `lower`：取偏小的数
- `higher`：取偏大的数
- `nearest`：取最近的数
- `midpoint`：两数平均

#### 描述性统计

```python
print(arrs.describe())
```
运行结果：
```
count     4.000000
mean     24.750000
std      13.772023
min      11.000000
25%      19.250000
50%      22.000000
75%      27.500000
max      44.000000
dtype: float64
```

> 💡 `describe()` 一次性给出 count/mean/std/min/四分位数/max，是"看数据先手"第一招。

#### 计数与去重

```python
print(arrs.value_counts())   # 每个元素出现次数
print(arrs.count())          # 非空元素个数：4
print(len(arrs))             # 总长度：6
print(arrs.drop_duplicates())# 去重
print(arrs.unique())         # 去重后的数组
print(arrs.nunique())        # 去重后非缺失值个数
```

`value_counts()` 运行结果：
```
22.0    2
11.0    1
44.0    1
dtype: int64
```

> ⚠️ 注意区分：`count()` 数非空个数，`size`/`len` 数总长度，`unique()` 返回数组，`nunique()` 返回个数。

#### 其他常用方法

```python
print(arrs.sample())              # 随机采样
print(arrs.sort_index())          # 按索引排序
print(arrs.sort_values())         # 按值排序
print(arrs.replace(22, "haha"))   # 替换值
print(arrs.to_frame())            # 转为 DataFrame
print(arrs.keys())                # 返回索引对象
```

#### 相关性与协方差

```python
arr1 = pd.Series([1, 2, 3])
arr2 = pd.Series([1, 2, 3])
arr3 = pd.Series([3, 2, 1])
arr4 = pd.Series([6, 7, 8])

print(arr1.corr(arr2))   # 完全正相关：1.0
print(arr1.corr(arr3))   # 完全负相关：-1.0
print(arr1.corr(arr4))   # 完全正相关：1.0
print(arr1.cov(arr3))    # 协方差
```

> 💡 相关性取值范围 [-1, 1]：1 同增同减，-1 反向变动，0 无关。corr 看"关系强弱"，cov 看"变动方向"。

#### 绘制直方图

```python
arr7 = pd.Series([3, 2, 1, 1, 1, 2, 2])
arr7.hist(bins=3)
```

> 直方图将数据划分为若干区间（bins），用矩形高度表示每个区间的频数。

#### items() 遍历

```python
for i, v in arr7.items():
    print(i, v)
```

> 💡 `items()` 每次返回 (索引, 值) 元组，适合需要同时拿到索引和值的遍历场景。

### 3.2.5 Series 的布尔索引

用条件判断生成布尔序列，再用布尔序列筛选数据，`True` 保留，`False` 剔除。

```python
s = pd.Series({"a": -1.2, "b": 3.5, "c": 6.8, "d": 2.9})

# 步骤1：生成布尔掩码
bools = s > s.mean()
print(bools)
```
运行结果：
```
a    False
b     True
c     True
d    False
dtype: bool
```

```python
# 步骤2：布尔索引筛选
print(s[bools])
```
运行结果：
```
b    3.5
c    6.8
dtype: float64
```

> 💡 **布尔索引的本质**：条件判断生成一个 True/False 序列（掩码），把掩码放进 `[]` 里，True 的位置留下、False 的位置去掉。
> 这是 Pandas 最核心的筛选手段，3.3.5 的 DataFrame 版本和 3.5 的数据分析都会反复用到。

#### 多条件筛选

```python
# 并且 &
print(s[(s > 2) & (s < 7)])

# 或者 |
print(s[(s < 0) | (s > 6)])

# 取反 ~
print(s[~(s > 2)])
```

> ⚠️ **多条件必须加括号**：`s[(s > 2) & (s < 7)]` 里的括号不能省，`&`/`|` 是位运算符，优先级低于比较运算。
> 用 `and`/`or` 会报错（Series 不支持 Python 的 and/or）。

### 3.2.6 Series 的运算

#### 1）Series 与标量运算

标量与每个元素分别计算（广播机制）：

```python
s = pd.Series({"a": -1.2, "b": 3.5, "c": 6.8, "d": 2.9})
print(s * 10)
```
运行结果：
```
a   -12.0
b    35.0
c    68.0
d    29.0
dtype: float64
```

#### 2）Series 与 Series 运算

**核心规则：按索引标签对齐计算，不按顺序！匹配不到的填 NaN。**

```python
s1 = pd.Series([1, 1, 1, 1])              # 默认索引 0,1,2,3
s2 = pd.Series([2, 2, 2, 2], index=[1, 2, 3, 4])
print(s1 + s2)
```
运行结果：
```
0    NaN
1    3.0
2    3.0
3    3.0
4    NaN
dtype: float64
```

> 索引 0 仅 s1 有 → NaN；索引 1/2/3 两边都有 → 1+2=3；索引 4 仅 s2 有 → NaN。
>
> ⚠️ **索引对齐是 Pandas 的铁律**（3.3.6 的 DataFrame 版同理）：两个对象运算，先按索引"配对"，配对上的才算，配不上的填 NaN。
> 它不管两个 Series 的先后顺序，只看索引是否相同。
## 3.3 Pandas 数据结构 - DataFrame

### 3.3.1 DataFrame 概述

DataFrame 是一个二维表格型数据结构，类似于 Excel 表格或数据库表：
- 含有一组有序的列，每列可以是不同的值类型
- 既有**行索引**也有**列索引**
- 可看做由多个 Series 组成的字典（共用一个索引）
- 支持数据访问、筛选、分割、合并、重塑、聚合、转换等操作

> 💡 **一句话理解**：DataFrame = 多个等长的 Series 并排拼起来，共享同一个行索引。行索引管"第几行"，列名管"哪一列"。

### 3.3.2 DataFrame 的创建

#### 方式1：通过字典创建

```python
df = pd.DataFrame({"id": [101, 102, 103], "name": ["张三", "李四", "王五"], "age": [20, 30, 40]})
print(df)
```
运行结果：
```
    id name  age
0  101   张三   20
1  102   李四   30
2  103   王五   40
```

> 字典的 key 成为**列名**，value 列表成为**列数据**；行索引自动生成 0,1,2...

#### 方式2：指定列顺序和行索引

```python
df = pd.DataFrame(
    data={"age": [20, 30, 40], "name": ["张三", "李四", "王五"]},
    columns=["name", "age"],
    index=[101, 102, 103]
)
print(df)
```
运行结果：
```
    name  age
101   张三   20
102   李四   30
103   王五   40
```

> 💡 `columns` 决定列的顺序（可以打乱字典顺序）；`index` 自定义行索引。

### 3.3.3 DataFrame 常用属性

```python
import pandas as pd

df = pd.DataFrame(
    data={"id": [101, 102, 103], "name": ["张三", "李四", "王五"], "age": [20, 30, 40]},
    index=["aa", "bb", "cc"]
)
```

| 属性 | 说明 | 示例 | 结果 |
|------|------|------|------|
| `index` | 行索引 | `df.index` | `Index(['aa','bb','cc'])` |
| `columns` | 列标签 | `df.columns` | `Index(['id','name','age'])` |
| `values` | 值（二维数组） | `df.values` | `[[101,'张三',20],...]` |
| `ndim` | 维度 | `df.ndim` | `2` |
| `shape` | 形状(行,列) | `df.shape` | `(3, 3)` |
| `size` | 元素总数 | `df.size` | `9` |
| `dtypes` | 各列类型 | `df.dtypes` | 各列类型 |
| `T` | 行列转置 | `df.T` | 行变列、列变行 |
| `loc[]` | 按标签索引 | `df.loc["aa":"cc"]` | 标签切片 |
| `iloc[]` | 按位置索引 | `df.iloc[0:3, 2]` | 位置切片 |
| `at[]` | 标签取单个值 | `df.at["aa","name"]` | `'张三'` |
| `iat[]` | 位置取单个值 | `df.iat[0, 1]` | `'张三'` |

> 💡 `shape` 记住"先行后列"：`(3, 3)` = 3 行 3 列。

#### loc 与 iloc 取值详解

**loc[行标签, 列标签]** —— 标签切片包含首尾：
```python
df.loc["aa":"cc"]              # 取 aa 到 cc 所有行（含 cc）
df.loc[:, ["id", "name"]]      # 所有行，取 id 和 name 列
df.loc[df["age"] > 25, "name"] # 布尔筛选+指定列
```

**iloc[行位置, 列位置]** —— 位置切片左闭右开：
```python
df.iloc[0:1]       # 第0行
df.iloc[0:3, 2]    # 前3行，第2列
df.iloc[-2:, [1,2]]# 倒数2行，第1、2列
```

> **关键区别**：loc 切片 `["aa":"cc"]` 包含 cc；iloc 切片 `[0:3]` 不包含 3。
> 另外：`loc` 不能用负数（`-1` 不是合法标签），`iloc` 可以（`-1` 表示最后一行/列）。

### 3.3.4 DataFrame 常用方法

#### axis 参数（核心重点）

| 参数 | 含义 | 操作方向 | 示例 |
|------|------|----------|------|
| `axis=0` / `'index'` | 沿行方向，对每列处理 | 纵向（上下） | 每列求和、按列排序 |
| `axis=1` / `'columns'` | 沿列方向，对每行处理 | 横向（左右） | 每行求和、按行排序 |

> 记忆口诀：**0 竖着走（按列聚合），1 横着走（按行聚合）**
>
> 💡 更准确的理解：`axis` 指定的是"**被压缩/被遍历的那个方向**"。`df.sum(axis=0)` 表示沿行方向（0轴）逐个元素累加，结果是一个数对应每一列，所以是"每列求和"。

#### 测试数据

```python
df = pd.DataFrame(
    data={
        "id": [101, 102, 103, 104, 105, 106, 101],
        "name": ["张三", "李四", "王五", "赵六", "冯七", "周八", "张三"],
        "age": [10, 20, 30, 40, None, 60, 10]
    },
    index=["aa", "bb", "cc", "dd", "ee", "ff", "aa"]
)
```

#### 数据预览

```python
df.head()     # 前5行
df.tail()     # 后5行
df.info()     # 基本信息（行数、列数、类型、缺失值）
df.describe() # 数值列统计信息
```

#### 缺失值与匹配

```python
df.isna()              # 判断每个元素是否缺失
df.isin([103, 106])    # 元素是否在集合中
df.count()             # 每列非空元素个数
```

#### 统计运算

```python
df["age"].sum()       # 求和
df["age"].mean()      # 平均值
df["age"].min()       # 最小值
df["age"].max()       # 最大值
df["age"].var()       # 方差
df["age"].std()       # 标准差
df["age"].median()    # 中位数
df["age"].mode()      # 众数
df["age"].quantile(0.5) # 分位数
```

> 统计运算会自动跳过缺失值（None/NaN 不参与计算）。

#### 计数、去重、采样

```python
df.value_counts()              # 每行出现次数
df.duplicated(subset="age")    # 判断是否为重复行
df.drop_duplicates()           # 去重
df.sample()                    # 随机采样
df.replace(20, "haha")         # 替换值
```

#### 表格比较

```python
df1 = pd.DataFrame({"id": [101], "name": ["张三"], "age": [10]})
df2 = pd.DataFrame({"id": [101], "name": ["张三"], "age": [10]})
print(df1.equals(df2))  # True
```

#### 累计运算

```python
df3 = pd.DataFrame({'A': [2, 5, 3, 7, 4], 'B': [1, 6, 2, 8, 3]})

df3.cummax()   # 累计最大值
df3.cummin()   # 累计最小值
df3.cumsum()   # 累计和
df3.cumprod()  # 累计积
```

`cumsum()` 运行结果：
```
   A   B
0  2   1
1  7   7
2 10   9
3 17  17
4 21  20
```

> 💡 累计运算 = "走到哪算到哪"：第 n 行存的是前 n 行的汇总。适合做增长曲线、余额变化等分析。

#### diff() 一阶差分

公式：当前元素 - 前一个元素

```python
print(df3.diff())
```
运行结果：
```
     A    B
0  NaN  NaN
1  3.0  5.0
2 -2.0 -4.0
3  4.0  6.0
4 -3.0 -5.0
```

参数说明：
- `periods=1`（默认）：向前偏移1行做差
- `axis=0`（默认）：按列计算；`axis=1`：按行计算

> ⚠️ 第 0 行没有"前一个元素"，所以结果是 NaN。

#### 排序

```python
df.sort_index()                              # 按行索引排序
df.sort_values(by="age")                     # 按 age 升序
df.sort_values(by=["age", "id"], ascending=[False, True])  # 多列排序
```

> ⚠️ `sort_values` 默认不修改原表，返回新表；要改原表需 `inplace=True`。
> 多列排序时：先按第一列排，第一列相同再看第二列；`ascending` 传列表与 `by` 列表一一对应。

#### 取最大/最小 N 条

```python
df.nlargest(n=2, columns="age")   # age 最大的2条
df.nsmallest(n=1, columns="age")  # age 最小的1条
```

> 💡 `nlargest`/`nsmallest` 相当于"排序后取头/尾 N 条"的快捷方式，**不排序、直接挑**，速度更快。
> 完整用法见 3.5 的【逐层拆解】。

### 3.3.5 DataFrame 的布尔索引

用条件判断生成布尔序列，筛选满足条件的行。

```python
df = pd.DataFrame(
    data={"age": [20, 30, 40, 10], "name": ["张三", "李四", "王五", "赵六"]},
    columns=["name", "age"],
    index=[101, 104, 103, 102],
)

# 步骤1：生成布尔掩码
print(df["age"] > 25)
```
运行结果：
```
101    False
104     True
103     True
102    False
Name: age, dtype: bool
```

```python
# 步骤2：筛选
print(df[df["age"] > 25])
```
运行结果：
```
     name  age
104   李四   30
103   王五   40
```

> ⚠️ **注意形状**：`df["age"] > 25` 是长度与行数相同的布尔 Series；把它放进 `df[...]` 里，True 的行保留。
> 与 Series 版（3.2.5）的区别：DataFrame 用布尔索引筛选的是**行**。

#### 多条件筛选

```python
df[(df["age"] > 20) & (df["age"] < 40)]   # 并且
df[(df["age"] < 15) | (df["age"] > 35)]   # 或者
df[~(df["age"] > 25)]                      # 取反
```

#### 搭配 loc 精准控制输出列

```python
df.loc[df["age"] > 25, "name"]           # 筛选行，只取 name 列（返回Series）
df.loc[df["age"] > 25, ["name", "age"]]  # 筛选行，取 name 和 age 两列（返回DataFrame）
df.loc[df["age"] > 25, :]                # 筛选行，保留全部列
```

> 💡 **布尔索引 + loc 的组合公式**：`df.loc[行条件, 列选择]` —— 行条件筛选行，列选择决定输出哪些列。这是数据分析里最常用的取数写法。

### 3.3.6 DataFrame 的运算

#### 1）DataFrame 与标量运算

每个元素分别与标量计算。数字列正常运算，字符串列乘法会重复拼接：

```python
df = pd.DataFrame(
    data={"age": [20, 30, 40, 10], "name": ["张三", "李四", "王五", "赵六"]},
    columns=["name", "age"],
    index=[101, 104, 103, 102],
)
print(df * 2)
```
运行结果：
```
       name  age
101  张三张三   40
104  李四李四   60
103  王五王五   80
102  赵六赵六   20
```

> 字符串只能做乘法（重复），加减除会报错。

#### 2）DataFrame 与 DataFrame 运算（索引对齐，重中之重）

**核心铁律：不按上下顺序加减，严格依靠「行索引 + 列名」匹配对位计算；匹配不到填 NaN。**

```python
df1 = pd.DataFrame(
    data={"age": [10, 20, 30, 40], "name": ["张三", "李四", "王五", "赵六"]},
    columns=["name", "age"],
    index=[101, 102, 103, 104],
)
df2 = pd.DataFrame(
    data={"age": [10, 20, 30, 40], "name": ["张三", "李四", "王五", "田七"]},
    columns=["name", "age"],
    index=[102, 103, 104, 105],
)
print(df1 + df2)
```
运行结果：
```
        name   age
101      NaN   NaN
102  李四张三  30.0
103  王五李四  50.0
104  赵六王五  70.0
105      NaN   NaN
```

逐行拆解：
- 索引 101：仅 df1 有 → 全部 NaN
- 索引 102：两边都有 → name: 李四+张三=李四张三，age: 20+10=30
- 索引 103：两边都有 → name: 王五+李四=王五李四，age: 30+20=50
- 索引 104：两边都有 → name: 赵六+王五=赵六王五，age: 40+30=70
- 索引 105：仅 df2 有 → 全部 NaN

> ⚠️ 注意 name 列也是"相加拼接"的，因为字符串 + 字符串 = 拼接。实际业务中很少对字符串列做运算，通常只对数值列操作。

### 3.3.7 DataFrame 的更改操作

#### 预备数据

```python
df = pd.DataFrame({
    "age": [20, 30, 40, 10],
    "name": ["张三", "李四", "王五", "赵六"],
    "id": [101, 102, 103, 104]
})
```
默认行索引为 0,1,2,3。

#### 1）设置行索引 set_index()

把普通列转为左侧行索引：
- `inplace=False`（默认）：返回新表格，原表不变
- `inplace=True`：直接修改原表

```python
df.set_index("id", inplace=True)
print(df)
```
运行结果：
```
     age name
id
101   20   张三
102   30   李四
103   40   王五
104   10   赵六
```

> 💡 设置索引后，`id` 不再是"列"，不能再用 `df["id"]` 取（要用 `df.index`）。这是 3.6 merge 用 `left_index=True` 的原因。

#### 2）重置行索引 reset_index()

把行索引变回普通列，恢复 0,1,2,3 默认索引：

```python
df.reset_index(inplace=True)
print(df)
```
运行结果：
```
    id  age name
0  101   20   张三
1  102   30   李四
2  103   40   王五
3  104   10   赵六
```

#### 3）修改行索引名和列名

**方式一：rename() 字典映射（推荐，精准局部修改）**

```python
df.set_index("id", inplace=True)
df.rename(
    index={101: "一", 102: "二", 103: "三", 104: "四"},
    columns={"age": "年龄", "name": "姓名"},
    inplace=True
)
print(df)
```
运行结果：
```
    年龄  姓名
id
一    20  张三
二    30  李四
三    40  王五
四    10  赵六
```

**方式二：直接整体赋值（批量全覆盖）**

```python
df.index = ["Ⅰ", "Ⅱ", "Ⅲ", "Ⅳ"]
df.columns = ["年齡", "名稱"]
print(df)
```
运行结果：
```
   年齡  名稱
Ⅰ   20  张三
Ⅱ   30  李四
Ⅲ   40  王五
Ⅳ   10  赵六
```

> 注意：列表长度必须与行数/列数完全一致。

#### 4）添加列

`df["新列名"] = 数据`，新列永远放在最右侧：

```python
df = pd.DataFrame({
    "age": [20, 30, 40, 10],
    "name": ["张三", "李四", "王五", "赵六"],
    "id": [101, 102, 103, 104]
})
df.set_index("id", inplace=True)

df["phone"] = ["13333333333", "14444444444", "15555555555", "16666666666"]
print(df)
```
运行结果：
```
     age name          phone
id
101   20   张三   13333333333
102   30   李四   14444444444
103   40   王五   15555555555
104   10   赵六   16666666666
```

赋值内容三种类型：
- 列表：一行对应一个数据
- 固定数字：整列填充同一个值
- 运算结果：基于已有列计算（如 `df["age"] * 2`）

#### 5）删除列

**方式一：drop()（万能，可删行可删列）**

```python
df.drop("phone", axis=1, inplace=True)  # axis=1 删列
```

**方式二：del 关键字（仅删列，直接修改原表）**

```python
del df["phone"]
```

> 删除行用 `df.drop(索引值, axis=0)`。

#### 6）插入列 insert()

在指定位置插入列，`df["列名"]` 只能加在末尾：

```python
df.insert(loc=0, column="phone", value=df["age"] * df.index)
print(df)
```
运行结果：
```
     phone  age name
id
101   2020   20   张三
102   3060   30   李四
103   4120   40   王五
104   1040   10   赵六
```

参数说明：
- `loc=0`：最左边第1列；`loc=1`：第2列
- 无 inplace 参数，直接修改原表

> ⚠️ 上面 `df["age"] * df.index` 是 Series 与索引的运算（索引也是 Series），按位置对位相乘：101×20=2020。

#### inplace 参数总结

| 取值 | 行为 | 适用场景 |
|------|------|----------|
| `False`（默认） | 生成新表格，原表不变，需变量接收 | 新手预览，防止误操作 |
| `True` | 直接修改原表，无需赋值 | 确认操作无误后使用 |

> ⚠️ 新版本 pandas 正在逐步弃用 `inplace`，建议养成"`df = df.方法(...)` 接收返回值"的习惯，两种写法结果等价。

### 3.3.8 DataFrame 数据的导入与导出

#### 导出数据

```python
import os
import pandas as pd

os.makedirs("data", exist_ok=True)
df = pd.DataFrame({"age": [20, 30, 40, 10], "name": ["张三", "李四", "王五", "赵六"], "id": [101, 102, 103, 104]})
df.set_index("id", inplace=True)

df.to_csv("data/df.csv")                              # CSV
df.to_csv("data/df.tsv", sep="\t")                    # TSV（制表符分隔）
df.to_csv("data/df_noindex.csv", index=False)         # 不保存行索引
df.to_excel("data/df.xlsx")                           # Excel
df.to_pickle("data/df.pkl")                           # pickle 二进制
df.to_json("data/df.json")                            # JSON
df.to_html("data/df.html")                            # HTML
df.to_clipboard()                                     # 复制到剪贴板
df_dict = df.to_dict()                                # 转为字典
```

> ⚠️ `index=False` 很常用：导出 CSV 时默认会带上行索引列，如果不需要就要显式关掉。
> 保存 Excel 需要 `openpyxl` 库：`pip install openpyxl`。

#### 导入数据

```python
df_csv = pd.read_csv("data/df.csv", index_col="id")       # 指定行索引列
df_tsv = pd.read_csv("data/df.tsv", sep="\t")             # 指定分隔符
df_excel = pd.read_excel("data/df.xlsx", index_col="id")  # Excel
df_pkl = pd.read_pickle("data/df.pkl")                    # pickle
df_json = pd.read_json("data/df.json")                    # JSON
df_html = pd.read_html("data/df.html", index_col=0)[0]    # HTML（返回列表，取第0个）
df_from_dict = pd.DataFrame(df_dict)                      # 从字典创建
```

> 💡 `read_html` 返回的是**列表**（一个网页可能有多张表），所以要加 `[0]` 取第一张。
> `read_csv` 的常用参数：`sep`（分隔符）、`index_col`（哪列当索引）、`parse_dates`（哪列解析为日期，见 3.11）、`na_values`（哪些值算缺失，见 3.7）。
## 3.4 Pandas 日期数据处理

> 本节主线：**字符串日期 → `to_datetime()` 转成日期类型 → `.dt` 拆出年/月/日/星期 → `to_period()` 归到统计周期**。

### 3.4.1 to_datetime() 日期格式转换

#### 参数说明

| 参数 | 说明 |
|------|------|
| `arg` | 要转换为日期时间的对象（单个字符串 / 列表 / Series / 列） |
| `errors` | 解析失败的处理方式：`'raise'`（默认，报错）、`'coerce'`（变 NaT）、`'ignore'`（原样返回） |
| `dayfirst` | `True` 时按"日月年"解析，如 `"10/11/12"` → `2012-11-10`；默认 `False` |
| `yearfirst` | `True` 时按"年月日"解析，如 `"10/11/12"` → `2010-11-12`；dayfirst 与 yearfirst 同时为 True 时 yearfirst 优先 |
| `format` | 指定解析格式（如 `"%Y/%m/%d"`），解析更快更稳 |
| `utc` | `True` 时返回 UTC（协调世界时）时间 |
| `unit` | 数字时间戳的单位（`'s'` 秒、`'ms'` 毫秒、`'ns'` 纳秒等），如 `pd.to_datetime(1700000000, unit='s')` |
| `infer_datetime_format` | 自动推断日期字符串格式，可提速 5~10 倍 |
| `origin` | 数字时间戳的参考起点，默认 `'unix'`（1970-01-01） |
| `cache` | 缓存已转换的日期，重复日期多时显著加速 |

> ⚠️ **笔记纠错**：`errors` 的实际默认值是 **`'raise'`**（解析失败直接抛异常），不是 `'ignore'`。
> 实战最常用 `errors='coerce'`：坏数据变成 `NaT`（Not a Time），程序不崩。

#### 将字符串字段转换为日期类型

```python
import pandas as pd

df = pd.DataFrame({"gmv": [100, 200, 300, 400],
                   "trade_date": ["2025-01-06", "2023-10-31", "2023-12-31", "2023-01-05"]})
df["ymd"] = pd.to_datetime(df["trade_date"])
print(df)
print("列类型：", df["ymd"].dtype)
```
运行结果：
```
   gmv  trade_date        ymd
0  100  2025-01-06 2025-01-06
1  200  2023-10-31 2023-10-31
2  300  2023-12-31 2023-12-31
3  400  2023-01-05 2023-01-05
列类型： datetime64[ns]
```

> 💡 **为什么必须转换**：`trade_date` 是字符串（dtype 是 `object`），不能比大小、不能算间隔、不能按月分组；
> 转成 `datetime64[ns]` 后才有日期的一切能力。`ymd` 只是自定义列名（Year-Month-Day 的缩写），换成任何名字都行。

### 3.4.2 dt 访问器提取日期属性

> `.dt` 是 pandas 给"日期类型 Series"配的专用工具箱（访问器），就像字符串 Series 有 `.str`。
> 只有 dtype 为 `datetime64` 的列才能用 `.dt`，普通列用了会报错——这也是判断一列是不是日期的小技巧。

```python
df['yy'] = df['ymd'].dt.year          # 年
df['mm'] = df['ymd'].dt.month         # 月
df['dd'] = df['ymd'].dt.day           # 日
df['week'] = df['ymd'].dt.day_name()  # 星期几（注意是方法，要加括号）
df['quarter'] = df['ymd'].dt.quarter  # 季度
df['mend'] = df['ymd'].dt.is_month_end   # 是否月底（返回布尔）
df['yend'] = df['ymd'].dt.is_year_end    # 是否年底（返回布尔）
print(df)
```
运行结果（部分）：
```
   gmv  trade_date        ymd    yy  mm  dd      week  quarter   mend   yend
0  100  2025-01-06 2025-01-06  2025   1   6    Monday        1  False  False
1  200  2023-10-31 2023-10-31  2023  10  31   Tuesday        4   True  False
2  300  2023-12-31 2023-12-31  2023  12  31    Sunday        4   True   True
3  400  2023-01-05 2023-01-05  2023   1   5  Thursday        1  False  False
```

**记忆口诀**：`.dt.` 后面跟什么取什么——`.year`/`.month`/`.day`/`.quarter` 是**属性**（不加括号），
`.day_name()`/`.month_name()` 是**方法**（要加括号），`.is_month_end`/`.is_year_end` 返回布尔。

> 💡 一次赋多列：`df['yy'], df['mm'], df['dd'] = df['ymd'].dt.year, df['ymd'].dt.month, df['ymd'].dt.day`
> 等价于写三行，右边三个 Series 长度相同，Python 多重赋值一一对应。

### 3.4.3 to_period() 获取统计周期

| freq | 含义 | 示例 |
|------|------|------|
| `'D'` | 按天 | 2024-01-01 |
| `'W'` | 按周（周日结束） | 2024-01-01/2024-01-07 |
| `'M'` | 按月 | 2024-05 |
| `'Q'` | 按季度 | 2024Q2 |
| `'A'` / `'Y'` | 按年 | 2024 |

```python
df["ystat"] = df["ymd"].dt.to_period("Y")  # 年
df["mstat"] = df["ymd"].dt.to_period("M")  # 月
df["qstat"] = df["ymd"].dt.to_period("Q")  # 季度
df["wstat"] = df["ymd"].dt.to_period("W")  # 周
print(df[["ymd", "mstat", "qstat", "wstat"]])
```
运行结果（部分）：
```
         ymd    mstat   qstat                  wstat
0 2025-01-06  2025-01  2025Q1  2025-01-06/2025-01-12
1 2023-10-31  2023-10  2023Q4  2023-10-30/2023-11-05
2 2023-12-31  2023-12  2023Q4  2023-12-25/2023-12-31
3 2023-01-05  2023-01  2023Q1  2023-01-02/2023-01-08
```

> 💡 **用途预告**：`to_period("M")` 生成的 `2023-10` 可以直接拿去 `groupby`，实现"按月统计"。
> 3.5 的分组聚合就是基于这一步。周周期显示成区间 `2023-10-30/2023-11-05`，表示这一周的范围。

---

## 3.5 DataFrame 数据分析入门

### 3.5.1 加载数据集（weather 天气数据集）

字段说明：`date`(日期)、`precipitation`(降水量)、`temp_max`(最高温)、`temp_min`(最低温)、`wind`(风力)、`weather`(天气状况)

```python
import pandas as pd

df = pd.read_csv("data/weather.csv")
print(type(df))           # <class 'pandas.core.frame.DataFrame'>
print(df.shape)           # (1461, 6)
print(df.columns)         # 列名
print(df.dtypes)          # 各列类型
df.info()                 # 基本信息
```

pandas 与 Python 常用数据类型对照：

| pandas类型 | Python类型 | 说明 |
|------------|-----------|------|
| `object` | `string` | 字符串类型 |
| `int64` | `int` | 整型 |
| `float64` | `float` | 浮点型 |
| `datetime64` | `datetime` | 日期时间类型 |

> 💡 看 `dtypes` 是数据分析第一步：先搞清楚每列是数字、文本还是日期，才知道能用哪些方法。

### 3.5.2 查看部分数据

```python
df.head()     # 前5行
df.tail(10)   # 后10行
```

### 3.5.3 获取列数据

```python
df["date"]                  # 取一列（返回Series）
df[["date"]]                # 取一列（返回DataFrame）
df[["date", "temp_max", "temp_min"]]  # 取多列
```

> ⚠️ 单列用字符串 `df["date"]` 返回 Series；**双中括号** `df[["date"]]` 返回 DataFrame（保留表格形态）。
> 取多列必须用列表：`df[["date", "temp_max"]]`。

### 3.5.4 按行获取数据

```python
df.loc[1]                   # 行标签为1的数据
df.loc[[1, 10, 100]]        # 指定多行
df.iloc[0]                  # 行位置为0
df.iloc[-1]                 # 最后一行
```

### 3.5.5 获取指定行与列

```python
df.loc[1, "precipitation"]              # 行标签1，列precipitation（单个值）
df.loc[:, "precipitation"]              # 所有行，指定列（整列）
df.iloc[:, [3, 5, -1]]                  # 所有行，列位置3、5、最后
df.iloc[:10, 2:6]                       # 前10行，列位置2-5（不含6）
df.loc[:10, ["date", "precipitation", "temp_max", "temp_min"]]
```

> 📌 **loc / iloc 速记**：
> - 格式统一是 `df[行, 列]`，逗号前管行、逗号后管列，`:` 表示全部
> - `loc` 按**名字**（标签），切片**含右端**，不能用负数
> - `iloc` 按**位置**（第几个），切片**不含右端**，支持 `-1`（倒数第一）

### 3.5.6 分组聚合计算

**通用公式**（全笔记最重要的公式之一）：

```python
df.groupby("分组字段")["要聚合的字段"].聚合函数()
df.groupby(["分组字段", "分组字段2"])[["要聚合的字段", "要聚合的字段2"]].聚合函数()
```

#### 【逐层拆解】按月分组，统计最高温和最低温的平均值

```python
df["month"] = pd.to_datetime(df["date"]).dt.to_period("M").astype(str)  # 先造出 month 列
month_temp_mean = df.groupby("month")[["temp_max", "temp_min"]].mean()
print(month_temp_mean)
```
运行结果（部分）：
```
          temp_max  temp_min
month
2012-01   7.054839  1.541935
2012-02   9.275862  3.203448
2012-03   9.554839  2.838710
...
```

**这行代码实际是 4 步嵌套，从里往外拆**：

1. **`pd.to_datetime(df["date"])`** → 把 date 列从字符串转成日期类型（3.4 的知识）
2. **`.dt.to_period("M")`** → 把每个日期归到"年-月"周期，如 `2012-01-15` → `2012-01`
3. **`.astype(str)`** → 把周期转成字符串 `"2012-01"`（方便分组和显示），存进新列 `df["month"]`
4. **`df.groupby("month")[["temp_max", "temp_min"]].mean()`** → 再拆成三步：
   - `df.groupby("month")` → 按 month 分组，得到 DataFrameGroupBy 对象（此时**还没计算**）
   - `[["temp_max", "temp_min"]]` → 从分组对象里挑出要聚合的两列
   - `.mean()` → 对每组、每列求平均（此刻才真正计算）

> 💡 **分组三步曲**：`groupby(分组字段)` → `[聚合字段]` → `.聚合函数()`。
> 分组后默认把分组字段变成行索引；多个分组字段时得到复合索引（3.9 详解）。

#### 分组频数计算

统计每个月不同天气状况的数量：

```python
df.groupby("month")["weather"].nunique()
```
运行结果（部分）：
```
month
2012-01    4
2012-02    4
2012-03    4
2012-04    4
2012-05    3
```

> 💡 `nunique()` = "去重后数个数"，即每组里 weather 有几种不同取值。`count()` 数的是非空条数，别混。

### 3.5.7 基本绘图

```python
df.groupby("month")[["temp_max", "temp_min"]].mean().plot()  # 折线图
```

> `plot()` 是 pandas 基于 matplotlib 的绘图方法，month 作为 x 轴，两个温度均值作为 y 轴（两条线）。

### 3.5.8 常用统计值

```python
df.describe()              # 数值列统计
df.describe().T            # 转置后查看（列太多时更好读）
df.describe(include="all") # 统计所有列
df.describe(include=["float64"])  # 只统计float64列
```

### 3.5.9 常用排序与筛选（重点：复杂函数逐层拆解）

先认识三个"排序家族"成员：

| 方法 | 作用 | 特点 |
|------|------|------|
| `nlargest(n, 列)` | 某列最大的 n 条 | 不排序，直接挑，快 |
| `nsmallest(n, 列)` | 某列最小的 n 条 | 不排序，直接挑，快 |
| `sort_values(列, ascending=...)` | 按列排序 | 可多列、可指定升降序 |
| `drop_duplicates(subset=列)` | 按列去重 | 保留每个组第一条 |

#### ① 找到最高温度最大的 30 天

```python
df.nlargest(30, "temp_max")
```

#### ② 【逐层拆解】从最高温最大的 30 天中，找最低温最小的 5 天

```python
df.nlargest(30, "temp_max").nsmallest(5, "temp_min")
```

**链式调用 = 上一步的结果直接喂给下一步**，拆开看：

- **第 1 步** `df.nlargest(30, "temp_max")`
  → 返回一个**新的 DataFrame**：temp_max 最大的 30 行（30 行 × 6 列）
- **第 2 步** 对第 1 步的结果 `.nsmallest(5, "temp_min")`
  → 在这 30 行里再挑 temp_min 最小的 5 行（5 行 × 6 列）

```
df（1461行）
  │ nlargest(30, "temp_max")   # 第一步：筛出最热的30天
  ▼
临时表（30行）
  │ nsmallest(5, "temp_min")   # 第二步：在这30天里找最冷的5天
  ▼
结果（5行）
```

> 💡 **本质**：就是"先筛后筛"两轮过滤。链式写法把中间表省了，但理解时要能拆出中间步骤。
> 顺序不能反：先 30 后 5 是先"热"再"冷"；反过来就是先冷后热，语义完全不同。
> （实测验证：第一步结果 temp_max 最小也 ≥ 32.8；第二步挑出的 5 行 temp_max 都在 33.3~34.5，确实是最热 30 天里的。）

#### ③ 【逐层拆解】找出每年的最高温度（三步组合）

```python
df["year"] = pd.to_datetime(df["date"]).dt.to_period("Y").astype(str)  # ① 造 year 列
df_sort = df.sort_values(["year", "temp_max"], ascending=[True, False])  # ② 排序
df_sort.drop_duplicates(subset="year")  # ③ 去重
```
运行结果：
```
            date  precipitation  temp_max  temp_min  wind weather  year
228  2012-08-16            0.0      34.4      18.3   2.8     sun  2012
546  2013-06-30            0.0      33.9      17.2   2.5     sun  2013
953  2014-08-11            0.5      35.6      17.8   2.6    rain  2014
1295 2015-07-19            0.0      35.0      17.2   3.3     sun  2015
```

**这个问题的思路是"排序 + 去重"的经典套路，逐层拆：**

- **第 1 步**：造 year 列（同 3.5.6 的套路：to_datetime → to_period("Y") → astype(str)）
- **第 2 步**：`sort_values(["year", "temp_max"], ascending=[True, False])`
  → 多列排序：**先按 year 升序**（2012、2013、2014、2015 排好），**year 相同的再按 temp_max 降序**（每年最热的天排在最前面）
  → 此时每年的第 1 行就是该年最热的一天
- **第 3 步**：`drop_duplicates(subset="year")`
  → 按 year 去重，**每个 year 只保留第一次出现的那一行**——正是每年最热的一天！

```
df（1461行）
  │ 新增 year 列
  ▼
df（1461行，多一列 year）
  │ sort_values(["year","temp_max"], ascending=[True,False])
  ▼        # 每年内部按温度从高到低排好
排序后（1461行）
  │ drop_duplicates(subset="year")   # 每年只留第一行 = 每年最高温
  ▼
结果（4行，每年一行）
```

> 💡 **套路总结**："按 XX 取每组第一行/最后一行"都可以用「排序 + `drop_duplicates(subset=XX)`」实现，
> 排序决定"第一行"是谁，去重决定"每组只留一个"。
> ⚠️ 注意 `drop_duplicates` 默认保留**第一次**出现的行，所以排序方向（升/降）决定了取的是最大还是最小。

### 3.5.10 案例：employees 员工数据集分析

字段：`employee_id`、`first_name`、`last_name`、`email`、`phone_number`、`job_id`、`salary`、`commission_pct`、`manager_id`、`department_id`

```python
df = pd.read_csv("data/employees.csv")
```

#### 找出薪资最低、最高的员工

```python
# 方式一：布尔索引（最直观）
print(df[df["salary"] == df["salary"].min()])   # 最低
print(df.loc[df["salary"] == df["salary"].max()])  # 最高

# 方式二：排序取头尾
print(df.sort_values("salary").head(1))                # 最低
print(df.sort_values("salary", ascending=False).head(1))  # 最高
```

> ⚠️ `df["salary"].min()` 是一个数（最低薪资），`df["salary"] == 这个数` 生成布尔掩码，再筛行。
> 注意 `df[条件]` 与 `df["salary"]` 的区别：前者是**行筛选**，后者是**取列**。

#### 找出薪资最高的 10 名员工

```python
df.nlargest(10, "salary")
```

#### 查看所有部门 id

```python
df["department_id"].unique()
```

#### 查看每个部门的员工数

```python
df.groupby("department_id")["employee_id"].count().rename("employee_count")
```

> 💡 `.rename("employee_count")` 给结果 Series 起名字（列名），后面绘图时图例更清楚。

#### 绘图：各部门员工数柱状图

```python
df.groupby("department_id")["employee_id"].count().rename("employee_count").plot(kind="bar")
```

#### 薪资的分布

```python
print(df["salary"].mean())     # 平均值
print(df["salary"].std())      # 标准差
print(df["salary"].median())   # 中位数
```

> 💡 平均薪资 vs 中位薪资：如果两者差很多，说明薪资分布偏斜（少数高薪拉高了平均）。

#### 【逐层拆解】找出平均薪资最高的部门

```python
df.groupby("department_id")["salary"].mean().nlargest(1)
```

**从里往外拆**：

1. `df.groupby("department_id")` → 按部门分组（DataFrameGroupBy 对象）
2. `["salary"]` → 只取 salary 列（SeriesGroupBy）
3. `.mean()` → 每个部门的平均薪资（**一个 Series：索引=部门，值=平均薪资**）
4. `.nlargest(1)` → 在这个 Series 里取最大值那一条 → 平均薪资最高的部门

```
df
  │ groupby("department_id")      # 分组
  │ ["salary"]                    # 取薪资列
  │ .mean()                       # 各部门平均薪资（Series）
  │ .nlargest(1)                  # 挑最大的一条
  ▼
平均薪资最高的部门
```

> 💡 **链式套路总结**：`groupby(...)[列].统计().nlargest(n)` 是"分组统计后再挑 Top N"的标准写法。
> 记住：**`.mean()` 的结果仍然是 Series/DataFrame，可以继续接任何 Series/DataFrame 的方法**——这就是链式调用能一路点下去的原因。
## 3.6 Pandas 数据组合函数

### 3.6.1 concat 连接

沿着一条轴将多个对象堆叠到一起，`axis` 设置连接方向：
- `axis=0`（默认）：**纵向堆叠**（上下拼接，行变多）
- `axis=1`：**横向拼接**（左右拼接，列变多）

#### Series 与 Series 连接

```python
s1 = pd.Series(["A", "B"], index=[1, 2])
s2 = pd.Series(["D", "E"], index=[4, 5])
s3 = pd.Series(["G", "H"], index=[7, 8])

pd.concat([s1, s2, s3])           # 按行连接（纵向堆叠）
pd.concat([s1, s2, s3], axis=1)   # 按列连接（横向拼接，缺失填NaN）
```

按行连接结果：
```
1    A
2    B
4    D
5    E
7    G
8    H
dtype: object
```

按列连接结果：
```
     0    1    2
1    A  NaN  NaN
2    B  NaN  NaN
4  NaN    D  NaN
5  NaN    E  NaN
7  NaN  NaN    G
8  NaN  NaN    H
```

> 💡 axis=1 时三个 Series 成为三列（列名 0/1/2），各行索引取三者的**并集**，凑不齐的位置填 NaN。
> 类比：axis=0 像"把几页纸上下粘起来"，axis=1 像"左右并排"，纸张大小不同就留白（NaN）。

#### DataFrame 与 Series 连接

```python
df1 = pd.DataFrame(data={"a": [1, 2], "b": [4, 5]}, index=[1, 2])
s1 = pd.Series(data=[7, 10], index=[1, 2], name="a")

pd.concat([df1, s1])  # 按行连接：Series 的 name 成为列名
```
运行结果：
```
     a    b
1    1  4.0
2    2  5.0
1    7  NaN
2   10  NaN
```

> 💡 Series 与 DataFrame 按行连接时，Series 的 `name` 会变成列名，没对应列的位置填 NaN。

#### DataFrame 与 DataFrame 连接

```python
df1 = pd.DataFrame(data={"a": [1, 2], "b": [4, 5]}, index=[1, 2])
df2 = pd.DataFrame(data={"a": [7, 8], "b": [10, 11]}, index=[1, 2])

pd.concat([df1, df2])       # 按行连接（行变多，索引重复没关系）
pd.concat([df1, df2], axis=1)  # 按列连接（列变多）
```

按列连接结果：
```
   a  b  a   b
1  1  4  7  10
2  2  5  8  11
```

#### 重置索引

```python
pd.concat([df1, df2], ignore_index=True)  # 连接后重置为0,1,2,3
```
运行结果：
```
   a   b
0  1   4
1  2   5
2  7  10
3  8  11
```

> ⚠️ 纵向拼接后索引会重复（1,2,1,2）。要干净的 0~N-1 索引就加 `ignore_index=True`。

#### 类似 join 的连接（列方向的并集/交集）

```python
df1 = pd.DataFrame(data={"a": [1, 2], "b": [4, 5]}, index=[1, 2])
df2 = pd.DataFrame(data={"b": [7, 8], "c": [10, 11]}, index=[2, 3])

pd.concat([df1, df2])               # 并集（默认 join="outer"），缺失填NaN
pd.concat([df1, df2], join="inner") # 交集，只保留共同列 b
```

并集结果：
```
     a  b     c
1  1.0  4   NaN
2  2.0  5   NaN
2  NaN  7  10.0
3  NaN  8  11.0
```

交集结果：
```
   b
1  4
2  5
2  7
3  8
```

> ⚠️ 这里的 `join` 管的是**列**（其他轴），跟 SQL 的 join 概念不同，别和 merge 的 how 混了。

### 3.6.2 merge 合并

通过一个或多个列将行连接（类似 SQL 的 JOIN），是"按共同列的值匹配"。

#### 数据连接的类型：一对一 / 多对一 / 多对多

**一对一**：两边共同列的值都不重复：

```python
df1 = pd.DataFrame({
    "employee": ["Bob", "Jake", "Lisa", "Sue"],
    "group": ["Accounting", "Engineering", "Engineering", "HR"]
})
df2 = pd.DataFrame({
    "employee": ["Lisa", "Bob", "Jake", "Sue"],
    "hire_date": [2004, 2008, 2012, 2014]
})
df3 = pd.merge(df1, df2)  # 自动按共同列 employee 合并
print(df3)
```
运行结果：
```
  employee        group  hire_date
0      Bob   Accounting       2008
1     Jake  Engineering       2012
2     Lisa  Engineering       2004
3      Sue           HR       2014
```

> 💡 自动匹配：两边都有同名列 `employee`，就把它当"键"，**按值配对，不管行顺序**（df2 里 Lisa 在前，但结果按 df1 的顺序排）。

**多对一**：一边的值有重复，结果保留重复：

```python
df2 = pd.DataFrame({
    "group": ["Accounting", "Engineering", "HR"],
    "supervisor": ["Carly", "Guido", "Steve"]
})
df3 = pd.merge(df1, df2)
```
运行结果：
```
  employee        group supervisor
0      Bob   Accounting      Carly
1     Jake  Engineering      Guido
2     Lisa  Engineering      Guido   # Engineering 有两个员工，supervisor 重复出现
3      Sue           HR      Steve
```

**多对多**：两边共同列都有重复，产生笛卡尔积：

```python
df2 = pd.DataFrame({
    "group": ["Accounting", "Accounting", "Engineering", "Engineering", "HR", "HR"],
    "skills": ["math", "spreadsheets", "coding", "linux", "spreadsheets", "organization"]
})
df3 = pd.merge(df1, df2)
```
运行结果：
```
  employee        group        skills
0      Bob   Accounting          math
1      Bob   Accounting  spreadsheets
2     Jake  Engineering        coding
3     Jake  Engineering         linux
4     Lisa  Engineering        coding
5     Lisa  Engineering         linux
6      Sue           HR  spreadsheets
7      Sue           HR  organization
```

> ⚠️ 多对多产生**笛卡尔积**：左边 2 个 Engineering × 右边 2 个 Engineering = 4 行 Engineering。
> 理解方式：每个左行去匹配右表里所有值相同的行，能配上的都保留。

#### 设置合并的键与索引

```python
pd.merge(df1, df2, on="employee")                       # 共同列名相同时，on 指定键
pd.merge(df1, df2, left_on="employee", right_on="name") # 列名不同时，分别指定
pd.merge(df1, df2, left_index=True, right_index=True)   # 按索引合并
```

**left_on / right_on 示例**（列名不同）：

```python
df1 = pd.DataFrame({"employee": ["Bob", "Jake", "Lisa", "Sue"],
                    "group": ["Accounting", "Engineering", "Engineering", "HR"]})
df2 = pd.DataFrame({"name": ["Bob", "Jake", "Lisa", "Sue"],
                    "salary": [70000, 80000, 120000, 90000]})
df3 = pd.merge(df1, df2, left_on="employee", right_on="name")
print(df3)
```
运行结果：
```
  employee        group  name  salary
0      Bob   Accounting   Bob   70000
1     Jake  Engineering  Jake   80000
2     Lisa  Engineering  Lisa  120000
3      Sue           HR   Sue   90000
```

> ⚠️ 列名不同时，两个键列都会保留在结果里（employee 和 name 内容一样）。

**left_index / right_index 示例**（按索引合并）：

```python
df1 = pd.DataFrame({"employee": ["Bob", "Jake", "Lisa", "Sue"],
                    "group": ["Accounting", "Engineering", "Engineering", "HR"]})
df2 = pd.DataFrame({"employee": ["Lisa", "Bob", "Jake", "Sue"],
                    "hire_date": [2004, 2008, 2012, 2014]})
df1.set_index("employee", inplace=True)
df2.set_index("employee", inplace=True)

df3 = pd.merge(df1, df2, left_index=True, right_index=True)
print(df3)
```
运行结果：
```
                 group  hire_date
employee
Bob        Accounting       2008
Jake      Engineering       2012
Lisa      Engineering       2004
Sue                HR       2014
```

> ⚠️ 设置索引后 employee 不再是"列"，用 `on="employee"` 在不同解释器下行为可能不一致，建议用 `left_index=True, right_index=True`。
> DataFrame 还有 `join()` 方法按索引合并，要求没有重叠列，或用 `lsuffix`/`rsuffix` 指定重叠列后缀：
> ```python
> df1.join(df2, lsuffix='_left', rsuffix='_right')
> ```

#### 设置数据连接的集合操作规则（how 参数）

| how | 说明 | 类比 |
|-----|------|------|
| `inner`（默认） | 内连接，只保留两边都有的键 | 交集 |
| `outer` | 外连接，全部保留，缺失填 NaN | 并集 |
| `left` | 左连接，保留左表所有行 | 以左表为主 |
| `right` | 右连接，保留右表所有行 | 以右表为主 |

```python
df1 = pd.DataFrame({"name": ["Peter", "Paul", "Mary"], "food": ["fish", "beans", "bread"]})
df2 = pd.DataFrame({"name": ["Mary", "Joseph"], "drink": ["wine", "beer"]})

pd.merge(df1, df2)             # inner：只有 Mary 两边都有
pd.merge(df1, df2, how="outer")  # outer：4 行，缺的填 NaN
pd.merge(df1, df2, how="left")   # left：Peter/Paul 的 drink 为 NaN
```

outer 运行结果：
```
     name   food drink
0  Joseph    NaN  beer
1    Mary  bread  wine
2    Paul  beans   NaN
3   Peter   fish   NaN
```

> 💡 **记忆**：inner = 都要有；outer = 全都要（缺的补 NaN）；left/right = 一边为主，另一边能配上就配，配不上补 NaN。

#### 重复列名的处理

```python
df1 = pd.DataFrame({"name": ["Bob", "Jake", "Lisa", "Sue"], "rank": [1, 2, 3, 4]})
df2 = pd.DataFrame({"name": ["Bob", "Jake", "Lisa", "Sue"], "rank": [3, 1, 4, 2]})

pd.merge(df1, df2, on="name")                     # 默认后缀 _x, _y
pd.merge(df1, df2, on="name", suffixes=("_df1", "_df2"))  # 自定义后缀
```

默认后缀结果：
```
   name  rank_x  rank_y
0   Bob       1       3
1  Jake       2       1
2  Lisa       3       4
3   Sue       4       2
```

---

## 3.7 Pandas 缺失值处理

### 3.7.1 pandas 中的缺失值

- **NaN**：浮点型特殊值，表示无效/未定义数字（`nan == nan` 结果为 False）
- **NA**：表示数据不可用/缺失，不限于数字类型（打印显示 `<NA>`）
- **None**：Python 原生空值，在 Series 中也被视为缺失

```python
import pandas as pd
import numpy as np

s = pd.Series([np.nan, None, pd.NA])
print(s.isnull())  # 全部为 True
```

> 💡 `isnull()` / `isna()` 完全等价，`notnull()` / `notna()` 是取反。见到哪个都不慌。

### 3.7.2 加载数据时控制缺失值

```python
pd.read_csv("data/weather_withna.csv")                        # 默认空白→NaN
pd.read_csv("data/weather_withna.csv", keep_default_na=False) # 空白不转NaN（保留空字符串）
pd.read_csv("data/weather_withna.csv", na_values=["2015-12-31"]) # 指定值转为NaN
```

> ⚠️ `keep_default_na=False` 后空白会变成空字符串而不是 NaN；`na_values` 可以指定"哪些值算缺失"。

### 3.7.3 查看缺失值

```python
df.isnull().sum()  # 每列缺失值数量（布尔值求和 = True 的个数）
```

> 💡 `isnull()` 得到 True/False 矩阵，`sum()` 按列求和，True 当 1 算，得到每列缺失个数。

### 3.7.4 剔除缺失值 dropna()

#### Series 剔除缺失值

```python
s = pd.Series([1, pd.NA, None])
s.dropna()  # 只保留非空值
```

#### DataFrame 剔除缺失值

无法从 DataFrame 中单独剔除一个值，只能剔除整行或整列。默认剔除**任何包含缺失值的整行**：

```python
df = pd.DataFrame([[1, pd.NA, 2], [2, 3, 5], [pd.NA, 4, 6]])

df.dropna()                    # 默认：任何含缺失值的整行删除
df.dropna(axis=1)              # 任何含缺失值的整列删除
df.dropna(how="all")           # 只有全部为缺失值才删除
df.dropna(thresh=2)            # 至少2个非空值才保留
df.dropna(subset=[0])          # 只看第0列，有缺失则删整行
```

参数速记：
| 参数 | 含义 |
|------|------|
| `axis` | 0 删行（默认）/ 1 删列 |
| `how` | `'any'`（默认，有一个缺失就删）/ `'all'`（全缺失才删） |
| `thresh` | 至少要有 N 个非空值才保留 |
| `subset` | 只看指定列有没有缺失 |

### 3.7.5 填充缺失值 fillna()

#### 固定值填充

```python
df.fillna(0)                                          # 全部填0
df.fillna({"temp_max": 60, "temp_min": -60})          # 字典指定不同列填不同值
```

#### 统计值填充（最常用）

```python
df.fillna(df[["precipitation", "temp_max", "temp_min", "wind"]].mean())
```

> 💡 思路：先算出各列平均值（一个 Series），再按列对应填充——**mean() 的索引正好是列名，自动对齐**。

#### 前后有效值填充

```python
df.ffill()  # 用前面的有效值填充（forward fill）
df.bfill()  # 用后面的有效值填充（backward fill）
```

> 💡 记忆：f = forward（向前借），b = backward（向后借）。适合"缺失值延续上一状态"的数据（如传感器读数）。

#### 线性插值填充

```python
s = pd.Series([1, np.nan, 3, 4, np.nan, 6])
s.interpolate()  # 线性插值
```
运行结果：
```
0    1.0
1    2.0
2    3.0
3    4.0
4    5.0
5    6.0
dtype: float64
```

> 💡 插值原理：缺失值前后的有效点连成直线，缺在哪就按比例取直线上的值。
> 1 和 3 中间缺的填 2（中点），4 和 6 中间缺的填 5。
> 插值方法：`linear`(线性)、`time`(时间序列)、`polynomial`(多项式，配合 `order` 指定阶数)。

---

## 3.8 Pandas 的 apply 函数

对 DataFrame 或 Series 进行逐行、逐列或逐元素的自定义操作。用于处理复杂变换逻辑，或无法用向量化操作完成的任务。

### 3.8.1 Series 使用 apply()

**Series 的 apply：函数接收的是 Series 中的【一个元素】**：

```python
import pandas as pd

def func(item):
    return item * 20

s = pd.Series([10, 20, 30])
s.apply(func)               # 每个元素×20
s.apply(lambda x: x * 20)   # lambda 写法
```

带参数的函数：

```python
def func1(item, p1):
    return item * p1

s.apply(func1, p1=3)  # 额外参数通过关键字传递
```

> ⚠️ 额外参数必须**关键字传参**（`p1=3`），apply 会把没匹配上的参数在调用 func 时传过去。

### 3.8.2 DataFrame 使用 apply()

**DataFrame 的 apply：函数接收的是 DataFrame 中的【一个 Series】**
（axis=0 时是"一列"，axis=1 时是"一行"）：

```python
def func(s):
    return s.sum()

df = pd.DataFrame({"a": [10, 20, 30], "b": [40, 50, 60]})
df.apply(func)           # 默认 axis=0，按列求和
df.apply(func, axis=1)   # axis=1，按行求和
```

**【逐层拆解】为什么取多列必须 axis=1？**

```python
def func(s):
    return s["a"] / s["b"]

df = pd.DataFrame({"a": [10, 20, 30], "b": [40, 50, 60]})
print(df.apply(func, axis=1))
```
运行结果：
```
0    0.25
1    0.40
2    0.50
dtype: float64
```

- `axis=0`（默认）时，func 收到的 `s` 是**一列**（如 a 列），`s["a"]` 会报错——列里没有叫 "a" 的行
- `axis=1` 时，func 收到的 `s` 是**一行**（含 a、b 两个元素），`s["a"]`/`s["b"]` 才能取到同行不同列的值

```
df.apply(func, axis=1) 的含义：
  第0行 → func(行0) → 10/40 = 0.25
  第1行 → func(行1) → 20/50 = 0.40
  第2行 → func(行2) → 30/60 = 0.50
```

> 💡 **判断口诀**：函数里要用"同一行的多个列"→ 必须 `axis=1`；函数只处理单列整体 → 默认 `axis=0`。

### 3.8.3 向量化函数

普通函数直接传入 Series 会报错（`if y == 0` 中 y 是向量，不能和标量比较）：

```python
def f(x, y):
    if y == 0:
        return np.nan
    return x / y

df = pd.DataFrame({"a": [10, 20, 30], "b": [40, 0, 60]})
print(f(df["a"], df["b"]))  # ValueError
```

**方式一：np.vectorize() 包装**：

```python
f_vec = np.vectorize(f)
print(f_vec(df["a"], df["b"]))  # [0.25  nan 0.5 ]
```

**方式二：@np.vectorize 装饰器**：

```python
@np.vectorize
def f(x, y):
    if y == 0:
        return np.nan
    return x / y

print(f(df["a"], df["b"]))  # [0.25  nan 0.5 ]
```

> 💡 `np.vectorize` 的作用：把"只处理单个值的函数"变成"能逐个处理数组元素的函数"，
> 内部相当于对每个元素调用一次原函数。⚠️ 它只是方便写法，性能上并不比显式循环快多少；
> 追求性能优先用 pandas/numpy 内置的向量化运算（如 `np.where`）。
## 3.9 数据聚合、转换、过滤

### 3.9.1 DataFrameGroupBy 对象

对 DataFrame 调用 `groupby()` 后，返回 **DataFrameGroupBy 对象**——可以理解成"若干组 DataFrame 的集合"，
**在调用聚合函数之前不会真正计算**（惰性）：

```python
df = pd.read_csv("data/employees.csv")
g = df.groupby("department_id")
# <pandas.core.groupby.generic.DataFrameGroupBy object at ...>
```

#### 查看分组

```python
g.groups              # 分组结果字典：键=组名，值=该组所有行索引的列表
g.get_group(50)       # 获取指定分组的数据（DataFrame）
g["salary"]           # 取分组后的某列（SeriesGroupBy）
g["salary"].mean()    # 每组平均薪资（此时才计算）
```

`groups` 结果示例：`{10.0: [100], 20.0: [101, 102], 30.0: [14, 15, 16, 17, 18, 19], ...}`

#### 按组迭代

```python
for dept_id, group in df.groupby("department_id"):
    print(f"当前组为{dept_id}，组里的数据情况{group.shape}:")
    print(group.iloc[:, 0:3])
    print("-------------------")
```

> 💡 迭代时每次拿到 `(组名, 该组的 DataFrame)` 两个值，适合"每组都要单独处理"的场景。

#### 按多字段分组（复合索引）

```python
salary_mean = df.groupby(["department_id", "job_id"])[
    ["salary", "commission_pct"]
].mean()  # 按 department_id 和 job_id 分组
print(salary_mean.index)   # MultiIndex（复合索引）
print(salary_mean.reset_index())  # 重置为普通列
```

分组结果索引示例（MultiIndex）：
```
MultiIndex([( 10.0, 'AD_ASST'),
            ( 20.0,  'MK_MAN'),
            ( 20.0,  'MK_REP'),
            ...])
```

重置索引后：
```
    department_id      job_id        salary  commission_pct
0           10.0     AD_ASST   4400.000000             NaN
1           20.0      MK_MAN  13000.000000             NaN
2           20.0      MK_REP   6000.000000             NaN
...
```

> 💡 多字段分组后索引是复合索引（MultiIndex）。两种还原方式：
> `reset_index()` 事后还原；或分组时直接加 `as_index=False`（一步到位，推荐）：
> ```python
> df.groupby(["department_id", "job_id"], as_index=False)[["salary"]].mean()
> ```

### 3.9.2 cut() 分箱

将连续数据分割成离散区间，常用于把"数值"变成"等级"。

| 参数 | 说明 |
|------|------|
| `x` | 要分箱的数组或 Series（数值型） |
| `bins` | 整数=均匀分成几段；列表=自定义区间边界 |
| `right` | 默认 `True`：区间右端闭合（含右端点）；`False` 则左端闭合 |
| `labels` | 给每个区间指定标签 |

```python
df = pd.read_csv("data/employees.csv")

# 分成3个等宽区间（默认右闭合，显示为 (a, b]）
salary = pd.cut(df.iloc[9:16]["salary"], 3)
print(salary)
```
运行结果（部分）：
```
9      (8366.667, 11000.0]
10    (5733.333, 8366.667]
11    (5733.333, 8366.667]
...
Name: salary, dtype: category
Categories (3, interval[float64, right]): [(3092.1, 5733.333] < (5733.333, 8366.667] < (8366.667, 11000.0]]
```

```python
# 自定义边界
salary = pd.cut(df.iloc[9:16]["salary"], [0, 10000, 20000])
# 自定义标签
salary = pd.cut(df.iloc[9:16]["salary"], 3, labels=["low", "medium", "high"])
```
带标签结果：
```
9       high
10    medium
11    medium
12    medium
13    medium
14      high
15       low
Name: salary, dtype: category
Categories (3, object): ['low' < 'medium' < 'high']
```

> 💡 `cut` 返回值是 **category（分类）类型**，可以直接 `value_counts()` 统计各段数量、直接 `plot.bar()` 画柱状图。
> 注意：`(a, b]` 表示"大于 a 且小于等于 b"（右闭合）；最小值落在第一个区间左边时会出现 NaN。

### 3.9.3 分组聚合

通用公式：
```python
df.groupby("分组字段")["要聚合的字段"].聚合函数()
df.groupby(["分组字段", "分组字段2"])[["要聚合的字段", "要聚合的字段2"]].聚合函数()
```

#### 常用聚合函数

| 方法 | 说明 |
|------|------|
| `sum()` | 求和 |
| `mean()` | 平均值 |
| `min()` / `max()` | 最小值 / 最大值 |
| `var()` / `std()` | 方差 / 标准差 |
| `median()` | 中位数 |
| `quantile()` | 指定位置的分位数，如 `quantile(0.5)` |
| `describe()` | 常见统计信息 |
| `size()` | 所有元素的个数（含缺失） |
| `count()` | 非空元素的个数 |
| `first` / `last` / `nth` | 第一行 / 最后一行 / 第 n 行 |
| `nunique()` | 去重后的个数 |

> ⚠️ `size()` vs `count()`：size 数"组里一共有几条"（含 NaN），count 数"非空几条"。

#### 【逐层拆解】一次计算多个统计值（agg）

```python
df.groupby("department_id")["salary"].agg(["min", "median", "max"])
```
运行结果（部分）：
```
                   min   median      max
department_id
10.0            4400.0   4400.0   4400.0
20.0            6000.0   9500.0  13000.0
30.0            2500.0   2850.0  11000.0
...
```

拆解：
1. `df.groupby("department_id")` → 按部门分组
2. `["salary"]` → 只取薪资列
3. `.agg(["min", "median", "max"])` → **同时算三个统计值**，每个统计值成为结果的一列

> 💡 `agg`（aggregate 的缩写）= 聚合，比单独调 `.min()`/`.max()` 高效，一次算完。

#### 多个列计算不同的统计值（agg 传字典）

```python
df.groupby("department_id").agg({"job_id": "nunique", "commission_pct": "mean"})
```
运行结果（部分）：
```
               job_id  commission_pct
department_id
10.0                1             NaN
20.0                2             NaN
...
```

> 💡 字典格式：`{"列名": "统计方式"}`——不同列可以各算各的。这是 agg 最灵活的地方。

#### 重命名统计值

```python
df.groupby("department_id").agg(
    {"job_id": "nunique", "commission_pct": "mean"}
).rename(columns={"job_id": "工种数", "commission_pct": "佣金比例平均值"})
```
运行结果（部分）：
```
               工种数  佣金比例平均值
department_id
10.0             1      NaN
20.0             2      NaN
...
```

> ⚠️ `rename` 加 `inplace=True` 才会改原表；链式写法里直接 `.rename(columns=...)` 接在结果后面即可（返回新表）。

#### 自定义函数

```python
def f(x):
    """统计每个部门员工 last_name 的首字母"""
    result = set()
    for i in x:
        result.add(i[0])
    return result

df.groupby("department_id")["last_name"].agg(f)
```
运行结果（部分）：
```
department_id
10.0                    {W}
20.0                 {F, H}
30.0          {B, T, R, C, K, H}
...
```

> 💡 传给自定义函数的参数 `x` 是**该组那一列的整个 Series**（不是单个元素！），
> 与 apply 的区别要分清：**agg 的函数收"一组数据"，apply 的函数收"一个元素/一行"**。

### 3.9.4 分组转换 transform()

**聚合 vs 转换的本质区别**：
- 聚合（agg）：一组数据 → 一个数（结果变短）
- 转换（transform）：一组数据 → 一组同长度的数据（**结果形状与输入一致**）

#### 【逐层拆解】组内标准化：每组样本减去组均值

```python
df.groupby("department_id")["salary"].transform(lambda x: x - x.mean())
```
运行结果（部分）：
```
0      4666.666667
1     -2333.333333
2     -2333.333333
3      3240.000000
...
```

- 每组内部：`x - x.mean()` 把该组每个薪资减去组平均
- 结果长度 = 原数据行数（1461 行 → 1461 个值，位置一一对应）
- 这正是"数据标准化"的第一步（去均值）

> ⚠️ 对比记忆：`groupby(...)[列].mean()` 返回每组**一个**值（行数 = 组数）；
> `transform(...)` 返回每组**一整套**值（行数 = 原行数）。transform 的结果可以直接赋回原列，如 `df["salary"] = ...`。

#### 按分组用平均值填充缺失值（经典实战）

```python
na_index = pd.Series(df.index.tolist()).sample(30)  # 随机挑30行
df.loc[na_index, "salary"] = pd.NA                  # 这30行的salary设为缺失

def fill_missing(x):
    if np.isnan(x.mean()):   # 如果组平均值也是NaN（整组都缺）
        return 0
    return x.fillna(x.mean())  # 用组平均值填充缺失

df["salary"] = df.groupby("department_id")["salary"].transform(fill_missing)
```

> 💡 思路：不同部门的薪资水平不同，用**各组自己的平均值**填各组自己的缺失，比全局平均值更合理。
> 这就是"分组填充"的标准写法。

### 3.9.5 分组过滤 filter()

过滤操作可以按照**分组的属性**丢弃整组数据：

```python
# 只保留 commission_pct 全部非空的部门
commission_pct_filter = df.groupby("department_id").filter(
    lambda x: x["commission_pct"].notnull().all()
)
```

> 💡 `filter` 的函数收的是**整个组的 DataFrame**（`x` 是组内所有行），返回 True 保留整组、False 丢弃整组。
> 与布尔索引的区别：布尔索引筛**行**，filter 按**组**筛。

**三种操作对比（面试常问）**：

| 操作 | 函数收到什么 | 返回什么 | 形状 |
|------|-------------|---------|------|
| `agg` 聚合 | 组内一列 Series | 每组的统计值 | 变短（组数行） |
| `transform` 转换 | 组内一列 Series | 与输入等长的值 | 不变（原行数） |
| `filter` 过滤 | 整个组的 DataFrame | True/False 决定整组去留 | 变短（丢弃整组） |

---

## 3.10 Pandas 透视表

### 3.10.1 什么是透视表

透视表是常见的数据汇总工具：根据**多个行分组键**和**多个列分组键**对数据进行聚合，
按行、列上的分组键把数据分配到各个矩形区域中——可以理解成"Excel 数据透视表"。

### 3.10.2 pivot_table() 参数

`DataFrame.pivot_table()` 与 `pandas.pivot_table()` 都能用，后者需要多传一个 `data` 参数。

| 参数 | 说明 |
|------|------|
| `values` | 待聚合的列（对哪列算统计值），默认聚合所有数值列 |
| `index` | 透视表的**行维度**（按哪些列分组放行） |
| `columns` | 透视表的**列维度**（按哪些列分组放列） |
| `aggfunc` | 聚合函数，默认 `mean`（可传 `"sum"`/`"count"`/列表等） |
| `fill_value` | 替换结果表中的缺失值 |
| `margins` | `True` 时添加"总计"行和列 |
| `dropna` | 默认 `True`：排除全缺失的行列；`False` 保留 |
| `observed` | `True` 只显示实际存在的组合 |

### 3.10.3 案例：睡眠质量分析透视表

sleep（睡眠健康和生活方式）数据集 13 个字段：
`person_id`、`gender`、`age`、`occupation`、`sleep_duration`(睡眠时长)、`sleep_quality`(睡眠质量 1~10)、
`physical_activity_level`、`stress_level`(压力等级 1~10)、`bmi_category`、`blood_pressure`、
`heart_rate`、`daily_steps`、`sleep_disorder`

```python
df = pd.read_csv("data/sleep.csv")

# 先把连续值分箱（cut 的实战应用）
sleep_duration_stage = pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12])  # 睡眠时长分8段
stress_level_stage = pd.cut(df["stress_level"], 4)  # 压力等级分4段
```

#### ① 统计不同睡眠时间、不同压力等级下的平均睡眠质量

```python
df.pivot_table(values="sleep_quality", index=[sleep_duration_stage, stress_level_stage], aggfunc="mean")
```
运行结果（部分）：
```
                              sleep_quality
sleep_duration stress_level
(0, 5]         (0.991, 3.25]       6.781818
               (3.25, 5.5]         6.161538
               (5.5, 7.75]         5.677778
               (7.75, 10.0]        6.082353
(5, 6]         (0.991, 3.25]       5.876923
...
```

> 相当于：`df.groupby([sleep_duration_stage, stress_level_stage])["sleep_quality"].mean()`
> ——透视表和 groupby 在这里做的事一样，只是透视表还能加"列维度"，更灵活。

#### ② 添加职业作为列维度

```python
df.pivot_table(
    values="sleep_quality",
    index=[sleep_duration_stage, stress_level_stage],
    columns=["occupation"],
    aggfunc="mean",
)
```
运行结果（部分）：
```
occupation                  Manual Labor  Office Worker   Retired   Student
sleep_duration stress_level
(0, 5]         (0.991, 3.25]      6.900000       6.350000  6.720000  6.750000
               (3.25, 5.5]        3.300000       7.966667  6.060000  5.650000
...
```

> 💡 `columns` 把 occupation 的取值（Manual Labor / Office Worker / Retired / Student）变成**列**，
> 表格瞬间变成"行=时长×压力、列=职业"的矩阵——这就是透视表"透视"二字的含义。

#### ③ 添加性别作为第二个列维度

```python
df.pivot_table(
    values="sleep_quality",
    index=[sleep_duration_stage, stress_level_stage],
    columns=["occupation", "gender"],
    aggfunc="mean",
)
```
运行结果（部分）：
```
occupation           Manual Labor       Office Worker      Retired        Student
gender                      Female  Male      Female  Male  Female  Male  Female  Male
sleep_duration stress_level
(0, 5]         (0.991, 3.25]    6.75   7.3       6.70   6.0     NaN  6.72    6.10  7.40
...
```

> ⚠️ 列维度多了之后，没有数据的组合位置就是 NaN（如 Retired-Female 在某个格子没有样本）。
> 可以加 `fill_value=0` 或 `margins=True` 观察总计。

> 💡 **pivot_table vs groupby 怎么选**：只要"行分组"→ groupby 更简单；需要"行+列双维度矩阵"→ pivot_table。
> 3.13 的堆叠图就是先 `pivot_table` 把数据变成矩阵再 `plot.bar(stacked=True)`。
## 3.11 Pandas 时间序列

### 3.11.1 Python 中的日期与时间工具

Python 标准库的 `datetime` 模块提供基础日期时间功能：

```python
from datetime import datetime

date1 = datetime(year=2000, month=1, day=1)
date2 = datetime.now()
print(date1)                # 2000-01-01 00:00:00
print(date2)                # 当前时间
print(date1.year)           # 2000
print(date1.month)          # 1
print(date1.day)            # 1
print(date2.weekday())      # 星期几（0=周一）
print(date2.strftime("%A")) # 星期几全名，如 Saturday
print(date2 - date1)        # 时间差：18263 days, 0:00:00
```

### 3.11.2 pandas 中的日期与时间

pandas 默认日期时间类型是 `datetime64[ns]`，有三种核心类型：

| 类型 | 说明 | 对应索引 |
|------|------|----------|
| `Timestamp` | 时间戳（某个时刻，替代 datetime） | `DatetimeIndex` |
| `Period` | 时间周期（某一段，如 2024-05） | `PeriodIndex` |
| `Timedelta` | 时间增量/持续时间（如 3 天） | `TimedeltaIndex` |

> 💡 三者对应三个问题："**什么时候**"（Timestamp）、"**哪一段**"（Period）、"**多久**"（Timedelta）。

#### datetime64

```python
pd.to_datetime("2015-01-01")  # 返回 Timestamp：2015-01-01 00:00:00

# format="mixed" 自动混合解析多种格式
pd.to_datetime(["4th of July, 2015", "2015-Jul-6", "07-07-2015", "20150708"], format="mixed")
# DatetimeIndex(['2015-07-04', '2015-07-06', '2015-07-07', '2015-07-08'], dtype='datetime64[ns]')
```

> 💡 单值 → `Timestamp`；列表/Series → `DatetimeIndex`。Timestamp 的 dtype 显示仍是 `datetime64[ns]`
> （Timestamp 是 pandas 对 numpy datetime64 的封装，dtype 反映底层存储类型）。

加载数据时解析日期（两种方式等价）：

```python
df = pd.read_csv("data/weather.csv")
pd.to_datetime(df["date"])                 # 事后转换
df = pd.read_csv("data/weather.csv", parse_dates=[0])  # 读取时直接解析第0列
```

#### 提取日期的各个部分

**单个 Timestamp 直接用属性**：

```python
d = pd.Timestamp("2015-01-01 09:08:07.123456")
d.year        # 2015
d.month       # 1
d.day         # 1
d.hour        # 9
d.minute      # 8
d.second      # 7
d.microsecond # 123456
```

**Series 必须用 .dt 访问器**：

```python
df = pd.read_csv("data/weather.csv", parse_dates=[0])
df_date = pd.to_datetime(df["date"])
df["year"] = df_date.dt.year
df["month"] = df_date.dt.month
df["day"] = df_date.dt.day
```

> ⚠️ 单个 Timestamp 直接 `.year`；多个（Series）要 `.dt.year`——少写 `.dt` 是高频报错点。

#### period

```python
df["quarter"] = pd.to_datetime(df["date"]).dt.to_period("Q")
# 2012-01-01 → 2012Q1
```

#### timedelta64

日期相减得到时间增量：

```python
df = pd.read_csv("data/weather.csv", parse_dates=[0])
df_date = pd.to_datetime(df["date"])
timedelta = df_date - df_date[0]  # 每个日期减去第一个日期
# 0    0 days
# 1    1 days
# 2    2 days
# Name: date, dtype: timedelta64[ns]
```

### 3.11.3 使用时间作为索引

#### DatetimeIndex

```python
df = pd.read_csv("data/weather.csv")
df["date"] = pd.to_datetime(df["date"])
df.set_index("date", inplace=True)
df.info()
# DatetimeIndex: 1461 entries, 2012-01-01 to 2015-12-31
```

**时间索引的切片威力**（这是时间索引最大的价值）：

```python
df.loc["2013-01":"2013-06"]   # 2013年1~6月的数据（字符串切片自动解析）
df.loc["2015"]                # 2015年全年
df.between_time("9:00", "11:00")  # 某时间段（需索引含时分秒）
df.at_time("3:33")                # 某时刻
```

> 💡 用时间字符串直接切片，是时间索引的杀手锏——不用写任何条件表达式。
> `"2015"` 这种"只到年"的写法会自动覆盖全年。

#### TimedeltaIndex

```python
df = pd.read_csv("data/weather.csv", parse_dates=[0])
df_date = pd.to_datetime(df["date"])
df["timedelta"] = df_date - df_date[0]   # timedelta64 列
df.set_index("timedelta", inplace=True)
df.info()
# TimedeltaIndex: 1461 entries, 0 days to 1460 days

df.loc["0 days":"5 days"]   # 按时间增量切片
```

### 3.11.4 生成时间序列 date_range()

用开始日期、结束日期和频率代码（可选）创建有规律的日期序列，默认按天：

```python
pd.date_range("2015-07-03", "2015-07-10")         # 开始+结束
pd.date_range("2015-07-03", periods=5)            # 开始+周期数（二选一即可）
pd.date_range("2015-07-03", periods=5, freq="h")  # 按小时
```

### 3.11.5 时间频率与偏移量

#### 常见频率代码

| 代码 | 说明 |
|------|------|
| `D` | 天（calendar day，含双休日） |
| `B` | 天（business day，仅工作日） |
| `W` | 周 |
| `M` | 月末（≤2.0）；`ME`（≥2.2） |
| `MS` | 月初（month start） |
| `Q` | 季末（≤2.0）；`QE`（≥2.2） |
| `QS` | 季初（quarter start） |
| `Y` / `A` | 年末（≤2.0）；`YE`（≥2.2） |
| `YS` | 年初（year start） |
| `h` | 小时；`bh` 工作小时 |
| `min` | 分钟 |
| `s` / `ms` / `us` / `ns` | 秒 / 毫秒 / 微秒 / 纳秒 |

> ⚠️ **版本提醒**：`ME`/`QE`/`YE` 是 pandas 2.2+ 新代码，2.0 及以下要用 `M`/`Q`/`Y`（见文首对照表）。

#### 偏移量（改变周期的起点）

在频率代码后加三位月份缩写，改变季/年的结束月份；加三位星期缩写，改变一周起点：

```python
pd.date_range("2015-07-03", periods=10, freq="Q-JAN")  # 1月为季度末（2.2+ 写作 QE-JAN）
# 2015-07-31, 2015-10-31, 2016-01-31, 2016-04-30, ...

pd.date_range("2015-07-03", periods=10, freq="W-WED")  # 每周三为一周起点
# 2015-07-08, 2015-07-15, 2015-07-22, ...

pd.date_range("2015-07-03", periods=10, freq="2h30min")  # 组合频率：2小时30分一步
# 00:00, 02:30, 05:00, 07:30, 10:00, ...
```

### 3.11.6 重新采样 resample()

按**新的频率**重新分组数据（如按天 → 按年），之后接聚合函数：

```python
df = pd.read_csv("data/weather.csv")
df["date"] = pd.to_datetime(df["date"])
df.set_index("date", inplace=True)

# 按年分组，计算每年的平均最高/最低温度
print(df[["temp_max", "temp_min"]].resample("Y").mean())
```
运行结果：
```
             temp_max  temp_min
date
2012-12-31  15.276776  7.289617
2013-12-31  16.058904  8.153973
2014-12-31  16.995890  8.662466
2015-12-31  17.427945  8.835616
```

> 💡 **resample 与 groupby 的关系**：resample 就是"时间版 groupby"——先按时间频率分组，再聚合。
> 区别：resample 要求**索引必须是时间类型**，且 `freq` 传频率代码（"Y"/"M"/"h"...）。
> ⚠️ 笔记原例 `resample("YE")` 是 2.2+ 写法，2.0 环境请用 `resample("Y")`。

---

## 3.12 Matplotlib 可视化

### 3.12.1 Matplotlib 简介

Matplotlib 是 Python 最常用的绘图库：
- 支持折线图、散点图、柱状图、直方图、饼图、热图、箱型图、极坐标图、3D 图等
- 高度自定义：标题、轴标签、刻度、图例、颜色、字体、线条样式、坐标轴范围、图形大小
- 与 NumPy、Pandas 紧密集成；可输出 PNG、PDF、SVG、EPS 等格式
- Jupyter 中交互式绘图；可用 FuncAnimation 生成动画

**显示环境**：脚本文件里必须 `plt.show()` 才显示；Notebook 中运行单元格后 PNG 图形直接嵌入。

> ⚠️ 需要先安装：`pip install matplotlib`。中文显示需配置字体（见 3.12.4）。

### 3.12.2 两种画图接口

#### 状态接口（MATLAB 风格，适合快速画图）

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(0, 10, 100)   # 创建 x 轴数据（0~10 均匀取100个点）
y1 = np.sin(x)                # y1 = sin(x)
y2 = np.cos(x)                # y2 = cos(x)

plt.figure(figsize=(10, 6))   # 创建画布，10×6英寸

plt.subplot(2, 1, 1)          # 2行1列子图，选第1个
plt.xlim(0, 10)               # x轴范围
plt.ylim(-1, 1)               # y轴范围
plt.xlabel("x")               # x轴标签
plt.ylabel("sin(x)")          # y轴标签
plt.title("sin")              # 子图标题
plt.plot(x, y1)               # 画折线

plt.subplot(2, 1, 2)          # 选第2个子图
plt.plot(x, y2)
plt.xlabel("x"); plt.ylabel("cos(x)"); plt.title("cos")

plt.show()                    # 显示
```

> 💡 状态接口 = "对当前画布/子图直接操作"，像在纸上画：`plt.xxx()` 作用于"当前选中的图"。

#### 面向对象接口（推荐，适合精细控制）

```python
fig, ax = plt.subplots(2, figsize=(10, 6))  # 一次创建画布+2个子图对象

ax[0].plot(x, y1)
ax[0].set_xlim(0, 10); ax[0].set_ylim(-1, 1)
ax[0].set_xlabel("x"); ax[0].set_ylabel("sin(x)"); ax[0].set_title("sin")

ax[1].plot(x, y2)
ax[1].set_xlim(0, 10); ax[1].set_ylim(-1, 1)
ax[1].set_xlabel("x"); ax[1].set_ylabel("cos(x)"); ax[1].set_title("cos")

plt.show()
```

> 💡 面向对象 = 每个子图都是 `ax` 对象，`ax.方法()` 只作用于该子图，不会串台。
> 对比：状态接口的 `plt.方法()` 作用于"当前子图"，切换子图要重新 `plt.subplot(...)`。
> **多子图、复杂图强烈建议面向对象接口**。

### 3.12.3 中文显示配置（必配）

```python
from matplotlib import rcParams
rcParams["font.sans-serif"] = ["SimHei"]    # 指定中文字体（黑体）
rcParams["axes.unicode_minus"] = False      # 解决负号显示为方块的问题
```

> ⚠️ 不配中文字体，图里的中文会变成方框；不关 `unicode_minus`，负号会显示异常。

### 3.12.4 单变量可视化：直方图

```python
fig = plt.figure()
ax1 = fig.add_subplot(1, 1, 1)
ax1.hist(df["precipitation"], bins=5)   # 降水量分5组，画频数直方图
ax1.set_xlabel("降水量")
ax1.set_ylabel("出现频次")
plt.show()
```

> 💡 直方图适合看"单个数值列长什么样"：集中在哪、是否偏态、有没有极端值。

### 3.12.5 双变量可视化：散点图

```python
fig = plt.figure()
ax1 = fig.add_subplot(1, 1, 1)
ax1.scatter(df["temp_max"], df["precipitation"])  # 横轴最高温，纵轴降水量
ax1.set_xlabel("最高气温")
ax1.set_ylabel("降水量")
plt.show()
```

> 💡 散点图适合看"两个数值变量的关系"：正相关/负相关/无相关，一眼看出。

### 3.12.6 多变量可视化：按年份着色

```python
def year_color(x):
    """为不同年份返回不同颜色"""
    match x.year:
        case 2012: return "r"
        case 2013: return "g"
        case 2014: return "b"
        case 2015: return "k"

df = pd.read_csv("data/weather.csv")
df["date"] = pd.to_datetime(df["date"])
df["color"] = df["date"].apply(year_color)   # apply 逐元素算颜色（3.8 知识）

fig = plt.figure()
ax1 = fig.add_subplot(1, 1, 1)
ax1.scatter(df["temp_max"], df["precipitation"], c=df["color"], alpha=0.5)
# c 设置颜色，alpha 设置透明度（0~1，越小越透明）
ax1.set_xlabel("最高气温")
ax1.set_ylabel("降水量")
plt.show()
```

> 💡 `match/case` 是 Python 3.10+ 的语法（模式匹配），等价于 if/elif/else。
> 这里把"日期"变成"颜色"用了 apply——把 3.8 的知识用在了绘图上，注意前后呼应。

---

## 3.13 Pandas 可视化

pandas 封装了 matplotlib，可以直接在 DataFrame/Series 上调用 `plot()`，一行出图。

### 3.13.1 单变量图表（sleep 数据集）

```python
df = pd.read_csv("data/sleep.csv")

# 柱状图：不同睡眠时长的数量（cut 分箱 + value_counts + plot.bar 一条链）
pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12]).value_counts().plot.bar(
    color=["red", "green", "blue", "yellow", "cyan", "magenta", "black", "purple"]
)

# 折线图：先 sort_index() 让区间按顺序排，再画线
pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12]).value_counts().sort_index().plot()

# 面积图
pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12]).value_counts().sort_index().plot.area()

# 直方图
df["sleep_duration"].value_counts().plot.hist()

# 饼状图
pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12]).value_counts().sort_index().plot.pie()
```

> 💡 链条解读（以柱状图为例）：
> `pd.cut(...)` 分箱 → `.value_counts()` 数每箱数量 → `.plot.bar()` 画柱状图。
> 折线/面积/饼图都要先 `.sort_index()` 把区间按大小排好，否则画出来顺序是乱的。

### 3.13.2 双变量图表

```python
df.plot.scatter(x="sleep_duration", y="sleep_quality")        # 散点图
df.plot.hexbin(x="sleep_duration", y="sleep_quality", gridsize=10)  # 蜂窝图（六边形密度图）
```

> 💡 蜂窝图是散点图的"密度版"：数据点太多重叠时，用六边形格子统计密度，颜色越深点越多。

### 3.13.3 堆叠图（pivot_table + plot 组合，综合实战）

```python
df["sleep_quality_stage"] = pd.cut(df["sleep_quality"], range(11))   # 质量分箱
df["sleep_duration_stage"] = pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12])  # 时长分箱

# 透视表：行=质量分段，列=时长分段，值=人数（3.10 知识）
df_pivot_table = df.pivot_table(
    values="person_id", index="sleep_quality_stage",
    columns="sleep_duration_stage", aggfunc="count"
)

df_pivot_table.plot.bar()                  # 分组柱状图
df_pivot_table.plot.bar(stacked=True)      # 堆叠柱状图（每根柱子叠起来）
df_pivot_table.plot.line()                 # 折线图
```

> 💡 这是全笔记综合度最高的链条：`cut` 分箱 → `pivot_table` 变矩阵 → `plot` 出图，
> 把 3.9/3.10/3.13 的知识串起来了。`stacked=True` 让多个分类"叠"在一根柱子上。

---

## 附录：核心知识点速查表

### axis 参数
| axis | 方向 | 操作对象 |
|------|------|----------|
| 0 / index | 纵向（上下） | 对每列处理 |
| 1 / columns | 横向（左右） | 对每行处理 |

### loc vs iloc
| 方法 | 索引依据 | 切片规则 | 负数 |
|------|----------|----------|------|
| loc | 标签（名字） | 包含首尾 | 不支持 |
| iloc | 数字位置 | 左闭右开 | 支持（-1=最后） |

### inplace 参数
| 值 | 行为 |
|----|------|
| False（默认） | 返回新对象，原数据不变 |
| True | 直接修改原数据 |

### 索引对齐运算
两对象运算时，按行索引+列名匹配，匹配不到填 NaN，不按顺序对位。

### 缺失值处理
| 方法 | 作用 |
|------|------|
| `isna()`/`isnull()` | 判断缺失 |
| `dropna()` | 删除缺失（how/thresh/subset/axis 控制） |
| `fillna()` | 填充缺失（固定值/字典/统计值） |
| `ffill()`/`bfill()` | 前后值填充 |
| `interpolate()` | 插值填充（linear/time/polynomial） |

### 分组三兄弟
| 操作 | 函数收到 | 结果形状 |
|------|---------|---------|
| `agg()` 聚合 | 组内 Series | 每组一行（变短） |
| `transform()` 转换 | 组内 Series | 与输入等长（不变） |
| `filter()` 过滤 | 整组 DataFrame | 保留/丢弃整组 |

### 链式调用心法
1. 每个方法都返回新对象（Series/DataFrame），所以可以一直 `.方法()` 点下去
2. 读长链时**从里往外拆**，每步只看"上一步返回了什么"
3. 常见套路：`筛选 → 分组 → 聚合 → 排序取Top`、`分箱 → 计数 → 绘图`

### 频率代码版本对照（pandas ≤2.0 vs ≥2.2）
| 含义 | ≤2.0 | ≥2.2 |
|------|------|------|
| 年 | `Y` / `A` | `YE` |
| 季度 | `Q` | `QE` |
| 月末 | `M` | `ME` |
| 季末1月 | `Q-JAN` | `QE-JAN` |
