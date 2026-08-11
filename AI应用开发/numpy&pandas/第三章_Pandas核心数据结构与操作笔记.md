

> 基于《尚硅谷大模型技术之Numpy与Pandas》整理，结合实操讲解，每个知识点附代码与运行结果。

---

## 目录

- [3.1 Pandas 简介](#31-pandas-简介)
- [3.2 Pandas 数据结构 - Series](#32-pandas-数据结构---series)
  - [3.2.1 Series 特点](#321-series-特点)
  - [3.2.2 Series 的创建](#322-series-的创建)
  - [3.2.3 Series 常用属性](#323-series-常用属性)
  - [3.2.4 Series 常用方法](#324-series-常用方法)
  - [3.2.5 Series 的布尔索引](#325-series-的布尔索引)
  - [3.2.6 Series 的运算](#326-series-的运算)
- [3.3 Pandas 数据结构 - DataFrame](#33-pandas-数据结构---dataframe)
  - [3.3.1 DataFrame 概述](#331-dataframe-概述)
  - [3.3.2 DataFrame 的创建](#332-dataframe-的创建)
  - [3.3.3 DataFrame 常用属性](#333-dataframe-常用属性)
  - [3.3.4 DataFrame 常用方法](#334-dataframe-常用方法)
  - [3.3.5 DataFrame 的布尔索引](#335-dataframe-的布尔索引)
  - [3.3.6 DataFrame 的运算](#336-dataframe-的运算)
  - [3.3.7 DataFrame 的更改操作](#337-dataframe-的更改操作)
  - [3.3.8 DataFrame 数据的导入与导出](#338-dataframe-数据的导入与导出)
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

#### 多条件筛选

```python
# 并且 &
print(s[(s > 2) & (s < 7)])

# 或者 |
print(s[(s < 0) | (s > 6)])

# 取反 ~
print(s[~(s > 2)])
```

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

---

## 3.3 Pandas 数据结构 - DataFrame

### 3.3.1 DataFrame 概述

DataFrame 是一个二维表格型数据结构，类似于 Excel 表格或数据库表：
- 含有一组有序的列，每列可以是不同的值类型
- 既有**行索引**也有**列索引**
- 可看做由多个 Series 组成的字典（共用一个索引）
- 支持数据访问、筛选、分割、合并、重塑、聚合、转换等操作

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

#### loc 与 iloc 取值详解

**loc[行标签, 列标签]** —— 标签切片包含首尾：
```python
df.loc["aa":"cc"]              # 取 aa 到 cc 所有行
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

### 3.3.4 DataFrame 常用方法

#### axis 参数（核心重点）

| 参数 | 含义 | 操作方向 | 示例 |
|------|------|----------|------|
| `axis=0` / `'index'` | 沿行方向，对每列处理 | 纵向（上下） | 每列求和、按列排序 |
| `axis=1` / `'columns'` | 沿列方向，对每行处理 | 横向（左右） | 每行求和、按行排序 |

> 记忆口诀：**0 竖着走（按列聚合），1 横着走（按行聚合）**

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

#### 排序

```python
df.sort_index()                              # 按行索引排序
df.sort_values(by="age")                     # 按 age 升序
df.sort_values(by=["age", "id"], ascending=[False, True])  # 多列排序
```

#### 取最大/最小 N 条

```python
df.nlargest(n=2, columns="age")   # age 最大的2条
df.nsmallest(n=1, columns="age")  # age 最小的1条
```

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
- 运算结果：基于已有列计算

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

#### inplace 参数总结

| 取值 | 行为 | 适用场景 |
|------|------|----------|
| `False`（默认） | 生成新表格，原表不变，需变量接收 | 新手预览，防止误操作 |
| `True` | 直接修改原表，无需赋值 | 确认操作无误后使用 |

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

---

## 3.4 Pandas 日期数据处理

### to_datetime() 日期格式转换

```python
import pandas as pd

df = pd.DataFrame({
    "gmv": [100, 200, 300, 400],
    "trade_date": ["2025-01-06", "2023-10-31", "2023-12-31", "2023-01-05"]
})
df["ymd"] = pd.to_datetime(df["trade_date"])
print(df)
```
运行结果：
```
   gmv  trade_date        ymd
0  100  2025-01-06 2025-01-06
1  200  2023-10-31 2023-10-31
2  300  2023-12-31 2023-12-31
3  400  2023-01-05 2023-01-05
```

### dt 访问器提取日期属性

```python
df['yy'] = df['ymd'].dt.year        # 年
df['mm'] = df['ymd'].dt.month       # 月
df['dd'] = df['ymd'].dt.day         # 日
df['week'] = df['ymd'].dt.day_name() # 星期几
df['quarter'] = df['ymd'].dt.quarter # 季度
df['mend'] = df['ymd'].dt.is_month_end  # 是否月底
df['yend'] = df['ymd'].dt.is_year_end   # 是否年底
```

### to_period() 获取统计周期

| freq | 含义 | 示例 |
|------|------|------|
| `'D'` | 按天 | 2024-01-01 |
| `'W'` | 按周 | 2024-01-01/2024-01-07 |
| `'M'` | 按月 | 2024-05 |
| `'Q'` | 按季度 | 2024Q2 |
| `'A'` / `'Y'` | 按年 | 2024 |

```python
df["ystat"] = df["ymd"].dt.to_period("Y")  # 年
df["mstat"] = df["ymd"].dt.to_period("M")  # 月
df["qstat"] = df["ymd"].dt.to_period("Q")  # 季度
df["wstat"] = df["ymd"].dt.to_period("W")  # 周
```

---

## 3.5 DataFrame 数据分析入门

### 加载数据集（weather 天气数据集）

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

### 查看部分数据

```python
df.head()     # 前5行
df.tail(10)   # 后10行
```

### 获取列数据

```python
df["date"]                  # 取一列（返回Series）
df[["date"]]                # 取一列（返回DataFrame）
df[["date", "temp_max", "temp_min"]]  # 取多列
```

### 按行获取数据

```python
df.loc[1]                   # 行标签为1的数据
df.loc[[1, 10, 100]]        # 指定多行
df.iloc[0]                  # 行位置为0
df.iloc[-1]                 # 最后一行
```

### 获取指定行与列

```python
df.loc[1, "precipitation"]              # 行标签1，列precipitation
df.loc[:, "precipitation"]              # 所有行，指定列
df.iloc[:, [3, 5, -1]]                  # 所有行，列位置3、5、最后
df.iloc[:10, 2:6]                       # 前10行，列位置2-5
df.loc[:10, ["date", "precipitation", "temp_max", "temp_min"]]
```

### 分组聚合计算

```python
# 按月分组，统计最高温和最低温的平均值
df["month"] = pd.to_datetime(df["date"]).dt.to_period("M").astype(str)
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

### 分组频数计算

```python
df.groupby("month")["weather"].nunique()  # 每月不同天气状况的数量
```

### 基本绘图

```python
df.groupby("month")[["temp_max", "temp_min"]].mean().plot()  # 折线图
```

### 常用统计值

```python
df.describe()              # 数值列统计
df.describe().T            # 转置后查看
df.describe(include="all") # 统计所有列
df.describe(include=["float64"])  # 只统计float64列
```

### 常用排序与筛选

```python
df.nlargest(30, "temp_max")                    # 最高温最大的30天
df.nlargest(30, "temp_max").nsmallest(5, "temp_min")  # 从中找最低温最小的5天

# 找出每年最高温度
df["year"] = pd.to_datetime(df["date"]).dt.to_period("Y").astype(str)
df_sort = df.sort_values(["year", "temp_max"], ascending=[True, False])
df_sort.drop_duplicates(subset="year")
```

### employees 员工数据集案例

字段：`employee_id`、`first_name`、`last_name`、`email`、`phone_number`、`job_id`、`salary`、`commission_pct`、`manager_id`、`department_id`

```python
df = pd.read_csv("data/employees.csv")

# 薪资最低/最高的员工
df[df["salary"] == df["salary"].min()]
df.loc[df["salary"] == df["salary"].max()]

# 薪资最高的10名员工
df.nlargest(10, "salary")

# 所有部门id
df["department_id"].unique()

# 每个部门员工数
df.groupby("department_id")["employee_id"].count().rename("employee_count")

# 平均薪资最高的部门
df.groupby("department_id")["salary"].mean().nlargest(1)
```

---

## 3.6 Pandas 数据组合函数

### concat 连接

沿一条轴将多个对象堆叠，`axis` 设置连接方向。

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

#### DataFrame 与 DataFrame 连接

```python
df1 = pd.DataFrame(data={"a": [1, 2], "b": [4, 5]}, index=[1, 2])
df2 = pd.DataFrame(data={"a": [7, 8], "b": [10, 11]}, index=[1, 2])

pd.concat([df1, df2])              # 按行连接
pd.concat([df1, df2], axis=1)      # 按列连接
```

#### 重置索引

```python
pd.concat([df1, df2], ignore_index=True)  # 连接后重置为0,1,2,3
```

#### 类似 join 的连接

- `join="outer"`（默认）：并集合并
- `join="inner"`：交集合并

```python
df1 = pd.DataFrame(data={"a": [1, 2], "b": [4, 5]}, index=[1, 2])
df2 = pd.DataFrame(data={"b": [7, 8], "c": [10, 11]}, index=[2, 3])

pd.concat([df1, df2])              # 并集，缺失填NaN
pd.concat([df1, df2], join="inner") # 交集，只保留共同列b
```

### merge 合并

通过一个或多个列将行连接（类似 SQL 的 JOIN）。

#### 一对一连接

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

#### 多对一连接

一列有重复值，结果保留重复：

```python
df2 = pd.DataFrame({
    "group": ["Accounting", "Engineering", "HR"],
    "supervisor": ["Carly", "Guido", "Steve"]
})
df3 = pd.merge(df1, df2)
```

#### 多对多连接

两边共同列都有重复，产生笛卡尔积：

```python
df2 = pd.DataFrame({
    "group": ["Accounting", "Accounting", "Engineering", "Engineering", "HR", "HR"],
    "skills": ["math", "spreadsheets", "coding", "linux", "spreadsheets", "organization"]
})
df3 = pd.merge(df1, df2)
```

#### 指定合并键

```python
pd.merge(df1, df2, on="employee")           # 共同列名相同时
pd.merge(df1, df2, left_on="employee", right_on="name")  # 列名不同时
pd.merge(df1, df2, left_index=True, right_index=True)    # 按索引合并
```

#### 连接方式（how 参数）

| how | 说明 |
|-----|------|
| `inner`（默认） | 内连接，只保留交集 |
| `outer` | 外连接，并集，缺失填NaN |
| `left` | 左连接，保留左表所有行 |
| `right` | 右连接，保留右表所有行 |

```python
pd.merge(df1, df2, how="outer")
pd.merge(df1, df2, how="left")
pd.merge(df1, df2, how="right")
```

#### 重复列名处理

```python
pd.merge(df1, df2, on="name")                         # 默认后缀 _x, _y
pd.merge(df1, df2, on="name", suffixes=("_df1", "_df2"))  # 自定义后缀
```

---

## 3.7 Pandas 缺失值处理

### 缺失值类型

- **NaN**：浮点型特殊值，表示无效/未定义数字（`nan == nan` 结果为 False）
- **NA**：表示数据不可用/缺失，不限于数字类型
- **None**：Python 原生空值，在 Series 中也被视为缺失

```python
import pandas as pd
import numpy as np

s = pd.Series([np.nan, None, pd.NA])
print(s.isnull())  # 全部为 True
```

### 加载数据时控制缺失值

```python
pd.read_csv("data/weather_withna.csv")                        # 默认空白→NaN
pd.read_csv("data/weather_withna.csv", keep_default_na=False) # 空白不转NaN
pd.read_csv("data/weather_withna.csv", na_values=["2015-12-31"]) # 指定值转为NaN
```

### 查看缺失值

```python
df.isnull().sum()  # 每列缺失值数量
```

### 剔除缺失值 dropna()

#### Series 剔除缺失值

```python
s = pd.Series([1, pd.NA, None])
s.dropna()  # 只保留非空值
```

#### DataFrame 剔除缺失值

```python
df = pd.DataFrame([[1, pd.NA, 2], [2, 3, 5], [pd.NA, 4, 6]])

df.dropna()                    # 默认：任何含缺失值的整行删除
df.dropna(axis=1)              # 任何含缺失值的整列删除
df.dropna(how="all")           # 只有全部为缺失值才删除
df.dropna(thresh=2)            # 至少2个非空值才保留
df.dropna(subset=[0])          # 只看第0列，有缺失则删整行
```

### 填充缺失值 fillna()

#### 固定值填充

```python
df.fillna(0)                                          # 全部填0
df.fillna({"temp_max": 60, "temp_min": -60})          # 字典指定不同列填不同值
```

#### 统计值填充

```python
df.fillna(df[["precipitation", "temp_max", "temp_min", "wind"]].mean())
```

#### 前后有效值填充

```python
df.ffill()  # 用前面的有效值填充（forward fill）
df.bfill()  # 用后面的有效值填充（backward fill）
```

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

插值方法：`linear`(线性)、`time`(时间序列)、`polynomial`(多项式)

---

## 3.8 Pandas 的 apply 函数

对 DataFrame 或 Series 进行逐行、逐列或逐元素的自定义操作。

### Series 使用 apply()

```python
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

### DataFrame 使用 apply()

```python
def func(s):
    return s.sum()

df = pd.DataFrame({"a": [10, 20, 30], "b": [40, 50, 60]})
df.apply(func)           # 默认 axis=0，按列求和
df.apply(func, axis=1)   # axis=1，按行求和
```

按行处理获取多列数据：

```python
def func(s):
    return s["a"] / s["b"]

df.apply(func, axis=1)  # 必须 axis=1 才能访问一行中的不同列
```
运行结果：
```
0    0.25
1    0.40
2    0.50
dtype: float64
```

### 向量化函数

普通函数无法直接处理向量（含 if 判断时），用 `np.vectorize` 向量化：

```python
@np.vectorize
def f(x, y):
    if y == 0:
        return np.nan
    return x / y

df = pd.DataFrame({"a": [10, 20, 30], "b": [40, 0, 60]})
print(f(df["a"], df["b"]))  # [0.25  nan 0.5 ]
```

---

## 3.9 数据聚合、转换、过滤

### GroupBy 对象

```python
df = pd.read_csv("data/employees.csv")
g = df.groupby("department_id")  # 返回 DataFrameGroupBy 对象
```

#### 查看分组

```python
g.groups              # 分组结果字典
g.get_group(50)       # 获取指定分组数据
g["salary"]           # 取分组后的某列（SeriesGroupBy）
g["salary"].mean()    # 每组平均薪资
```

#### 按组迭代

```python
for dept_id, group in df.groupby("department_id"):
    print(f"部门 {dept_id}, 数据形状 {group.shape}")
```

#### 多字段分组

```python
salary_mean = df.groupby(["department_id", "job_id"])[["salary", "commission_pct"]].mean()
salary_mean.reset_index()                    # 重置复合索引
df.groupby(["department_id", "job_id"], as_index=False)[["salary"]].mean()  # 分组时不设索引
```

### cut() 分箱

将连续数据分割成离散区间：

```python
salary = pd.cut(df.iloc[9:16]["salary"], 3)                  # 分成3个等宽区间
salary = pd.cut(df.iloc[9:16]["salary"], [0, 10000, 20000])  # 自定义区间边界
salary = pd.cut(df.iloc[9:16]["salary"], 3, labels=["low", "medium", "high"])  # 自定义标签
```

### agg() 聚合

#### 一次计算多个统计值

```python
df.groupby("department_id")["salary"].agg(["min", "median", "max"])
```

#### 多列不同统计

```python
df.groupby("department_id").agg({"job_id": "nunique", "commission_pct": "mean"})
```

#### 重命名统计列

```python
df.groupby("department_id").agg(
    {"job_id": "nunique", "commission_pct": "mean"}
).rename(columns={"job_id": "工种数", "commission_pct": "佣金比例平均值"})
```

#### 自定义聚合函数

```python
def f(x):
    result = set()
    for i in x:
        result.add(i[0])
    return result

df.groupby("department_id")["last_name"].agg(f)
```

### transform() 转换

返回与原数据形状相同的结果，不缩减：

```python
# 每组薪资减去组均值（标准化）
df.groupby("department_id")["salary"].transform(lambda x: x - x.mean())

# 按分组用平均值填充缺失值
def fill_missing(x):
    if np.isnan(x.mean()):
        return 0
    return x.fillna(x.mean())

df["salary"] = df.groupby("department_id")["salary"].transform(fill_missing)
```

### filter() 过滤

按分组属性丢弃整组数据：

```python
# 只保留 commission_pct 全部非空的部门
df.groupby("department_id").filter(lambda x: x["commission_pct"].notnull().all())
```

---

## 3.10 Pandas 透视表

透视表根据多个行分组键和列分组键对数据聚合。

```python
df = pd.read_csv("data/sleep.csv")  # 睡眠健康数据集

sleep_duration_stage = pd.cut(df["sleep_duration"], [0, 5, 6, 7, 8, 9, 10, 11, 12])
stress_level_stage = pd.cut(df["stress_level"], 4)

# 基本透视表：行=睡眠时间+压力等级，值=睡眠质量，聚合=均值
df.pivot_table(values="sleep_quality", index=[sleep_duration_stage, stress_level_stage], aggfunc="mean")

# 添加列维度
df.pivot_table(values="sleep_quality", index=[sleep_duration_stage, stress_level_stage],
               columns=["occupation"], aggfunc="mean")

# 多列维度
df.pivot_table(values="sleep_quality", index=[sleep_duration_stage, stress_level_stage],
               columns=["occupation", "gender"], aggfunc="mean")
```

---

## 3.11 Pandas 时间序列

### 日期与时间类型

| 类型 | 说明 | 对应索引 |
|------|------|----------|
| `Timestamp` | 时间戳（替代 datetime） | `DatetimeIndex` |
| `Period` | 时间周期 | `PeriodIndex` |
| `Timedelta` | 时间增量 | `TimedeltaIndex` |

### to_datetime() 转换

```python
pd.to_datetime("2015-01-01")  # 返回 Timestamp
pd.to_datetime(["4th of July, 2015", "2015-Jul-6", "07-07-2015"], format="mixed")
```

读取 CSV 时直接解析日期：
```python
pd.read_csv("data/weather.csv", parse_dates=[0])  # 第0列解析为日期
```

### 提取日期部分

```python
d = pd.Timestamp("2015-01-01 09:08:07.123456")
d.year, d.month, d.day, d.hour, d.minute, d.second, d.microsecond

# Series 用 dt 访问器
df["year"] = pd.to_datetime(df["date"]).dt.year
df["month"] = pd.to_datetime(df["date"]).dt.month
```

### 时间作为索引

```python
df["date"] = pd.to_datetime(df["date"])
df.set_index("date", inplace=True)

df.loc["2013-01":"2013-06"]  # 2013年1-6月
df.loc["2015"]               # 2015年全年
df.between_time("9:00", "11:00")  # 某时间段
df.at_time("3:33")           # 某时刻
```

### date_range() 生成时间序列

```python
pd.date_range("2015-07-03", "2015-07-10")               # 开始+结束，默认天
pd.date_range("2015-07-03", periods=5)                  # 开始+周期数
pd.date_range("2015-07-03", periods=5, freq="h")        # 按小时
pd.date_range("2015-07-03", periods=10, freq="QE-JAN")  # 季度末（1月为季末）
pd.date_range("2015-07-03", periods=10, freq="W-WED")   # 每周三
pd.date_range("2015-07-03", periods=10, freq="2h30min") # 组合频率
```

### resample() 重新采样

```python
df[["temp_max", "temp_min"]].resample("YE").mean()  # 按年重采样，计算均值
```

---

## 3.12 Matplotlib 可视化

### 两种画图接口

#### 状态接口（MATLAB 风格）

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(0, 10, 100)
y1 = np.sin(x)
y2 = np.cos(x)

plt.figure(figsize=(10, 6))
plt.subplot(2, 1, 1)
plt.xlim(0, 10); plt.ylim(-1, 1)
plt.xlabel("x"); plt.ylabel("sin(x)"); plt.title("sin")
plt.plot(x, y1)

plt.subplot(2, 1, 2)
plt.plot(x, y2)
plt.xlabel("x"); plt.ylabel("cos(x)"); plt.title("cos")
plt.show()
```

#### 面向对象接口（推荐）

```python
fig, ax = plt.subplots(2, figsize=(10, 6))
ax[0].plot(x, y1)
ax[0].set_xlabel("x"); ax[0].set_ylabel("sin(x)"); ax[0].set_title("sin")
ax[1].plot(x, y2)
ax[1].set_xlabel("x"); ax[1].set_ylabel("cos(x)"); ax[1].set_title("cos")
plt.show()
```

### 中文显示配置

```python
from matplotlib import rcParams
rcParams["font.sans-serif"] = ["SimHei"]    # 指定中文字体
rcParams["axes.unicode_minus"] = False      # 解决负号显示问题
```

### 单变量可视化：直方图

```python
fig = plt.figure()
ax1 = fig.add_subplot(1, 1, 1)
ax1.hist(df["precipitation"], bins=5)
ax1.set_xlabel("降水量"); ax1.set_ylabel("出现频次")
plt.show()
```

### 双变量可视化：散点图

```python
fig = plt.figure()
ax1 = fig.add_subplot(1, 1, 1)
ax1.scatter(df["temp_max"], df["precipitation"])
ax1.set_xlabel("最高气温"); ax1.set_ylabel("降水量")
plt.show()
```

---

## 3.13 Pandas 可视化

基于 Matplotlib 封装，直接在 DataFrame/Series 上调用 `plot()`。

### 单变量图表

```python
# 柱状图
pd.cut(df["sleep_duration"], [0,5,6,7,8,9,10,11,12]).value_counts().plot.bar()

# 折线图
pd.cut(df["sleep_duration"], [0,5,6,7,8,9,10,11,12]).value_counts().sort_index().plot()

# 面积图
pd.cut(df["sleep_duration"], [0,5,6,7,8,9,10,11,12]).value_counts().sort_index().plot.area()

# 直方图
df["sleep_duration"].value_counts().plot.hist()

# 饼状图
pd.cut(df["sleep_duration"], [0,5,6,7,8,9,10,11,12]).value_counts().sort_index().plot.pie()
```

### 双变量图表

```python
df.plot.scatter(x="sleep_duration", y="sleep_quality")          # 散点图
df.plot.hexbin(x="sleep_duration", y="sleep_quality", gridsize=10)  # 蜂窝图
```

### 堆叠图

```python
df_pivot = df.pivot_table(values="person_id", index="sleep_quality_stage",
                          columns="sleep_duration_stage", aggfunc="count")
df_pivot.plot.bar()                 # 分组柱状图
df_pivot.plot.bar(stacked=True)     # 堆叠柱状图
df_pivot.plot.line()                # 折线图
```

---

## 附录：核心知识点速查表

### axis 参数
| axis | 方向 | 操作对象 |
|------|------|----------|
| 0 / index | 纵向（上下） | 对每列处理 |
| 1 / columns | 横向（左右） | 对每行处理 |

### loc vs iloc
| 方法 | 索引依据 | 切片规则 |
|------|----------|----------|
| loc | 标签 | 包含首尾 |
| iloc | 数字位置 | 左闭右开 |

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
| `isna()/isnull()` | 判断缺失 |
| `dropna()` | 删除缺失 |
| `fillna()` | 填充缺失 |
| `ffill()/bfill()` | 前后值填充 |
| `interpolate()` | 插值填充 |
