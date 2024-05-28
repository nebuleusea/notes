# 索引和选择数据

pandas 对象中的轴标签信息有多种用途：

- 使用已知指标识别数据（即提供*元数据*），这对于分析、可视化和交互式控制台显示很重要。
- 启用自动且显式的数据对齐。
- 允许直观地获取和设置数据集的子集。

在本节中，我们将重点关注最后一点：即如何对 pandas 对象进行切片、切块以及一般获取和设置子集。主要重点将放在 Series 和 DataFrame 上，因为它们在该领域受到了更多的开发关注。

笔记

Python 和 NumPy 索引运算符`[]`和属性运算符`.` 可以在各种用例中快速轻松地访问 pandas 数据结构。这使得交互式工作变得直观，因为如果您已经知道如何处理 Python 字典和 NumPy 数组，则几乎不需要学习什么新东西。然而，由于事先不知道要访问的数据类型，因此直接使用标准运算符存在一些优化限制。对于生产代码，我们建议您利用本章中介绍的优化的 pandas 数据访问方法。

警告

对于设置操作是否返回副本或引用可以取决于上下文。有时会出现这种情况，应该避免这种情况。请参阅[返回视图与副本](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-view-versus-copy)。`chained assignment`

请参阅[多重索引/高级索引](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced)以`MultiIndex`获取更多高级索引文档。

请参阅[食谱](https://pandas.pydata.org/docs/user_guide/cookbook.html#cookbook-selection)了解一些高级策略。



## 索引的不同选择

对象选择已添加了许多用户请求的附加内容，以支持更明确的基于位置的索引。 pandas 现在支持三种类型的多轴索引。

- `.loc`主要基于标签，但也可以与布尔数组一起使用。当找不到项目时`.loc`会引发。`KeyError`允许的输入有：

  > - 单个标签，例如`5`or `'a'`（请注意，它`5`被解释为 索引的*标签。此用途***不是**沿索引的整数位置。）。
  > - 标签列表或数组。`['a', 'b', 'c']`
  > - 带有标签的切片对象`'a':'f'`（请注意，与通常的 Python 切片相反，当索引中存在时，起始点和终止点**都包含在内！请参阅**[使用标签切片](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-slicing-with-labels) 和[包含端点](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced-endpoints-are-inclusive)。）
  > - 布尔数组（任何`NA`值都将被视为`False`）。
  > - `callable`具有一个参数（调用 Series 或 DataFrame）并返回索引有效输出（上述之一）的函数。
  > - 行（和列）索引的元组，其元素是上述输入之一。

  更多信息请参见[按标签选择](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-label)。

- `.iloc`主要是基于整数位置（从轴`0`到 `length-1`轴），但也可以与布尔数组一起使用。如果请求的索引器越界，`.iloc`则会引发该错误，但允许越界索引的*切片*索引器除外。 （这符合 Python/NumPy*切片* 语义）。允许的输入有：`IndexError`

  > - 一个整数，例如`5`。
  > - 整数列表或数组。`[4, 3, 0]`
  > - 带有 int 的切片对象`1:7`。
  > - 布尔数组（任何`NA`值都将被视为`False`）。
  > - `callable`具有一个参数（调用 Series 或 DataFrame）并返回索引有效输出（上述之一）的函数。
  > - 行（和列）索引的元组，其元素是上述输入之一。

  [请参阅按位置选择](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-integer)、 [高级索引](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced)和[高级层次结构](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced-advanced-hierarchical)了解更多信息。

- `.loc`、`.iloc`、 以及`[]`索引也可以接受 a`callable`作为索引器。请参阅[通过可调用选择](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-callable)来了解更多信息。

  笔记

  将元组键解构为行（和列）索引发生 在应用可调用对象*之前*，因此您无法从可调用对象返回元组来索引行和列。

从具有多轴选择的对象获取值使用以下表示法（`.loc`作为示例，但以下内容`.iloc`也适用）。任何轴访问器都可以是空切片`:`。规范中遗漏的轴被假定为`:`，例如，`p.loc['a']`相当于 。`p.loc['a', :]`

| 对象类型 | 索引器                               |
| -------- | ------------------------------------ |
| 系列     | `s.loc[indexer]`                     |
| 数据框   | `df.loc[row_indexer,column_indexer]` |



## 基本

[正如上一节](https://pandas.pydata.org/docs/user_guide/basics.html#basics)介绍数据结构时提到的，索引的主要功能`[]`（`__getitem__` 对于那些熟悉在 Python 中实现类行为的人来说）是选择低维切片。下表显示了使用 索引 pandas 对象时的返回类型值`[]`：

| 对象类型 | 选择             | 返回值类型            |
| -------- | ---------------- | --------------------- |
| 系列     | `series[label]`  | 标量值                |
| 数据框   | `frame[colname]` | `Series`对应于colname |

这里我们构建一个简单的时间序列数据集来用于说明索引功能：

```
In [1]: dates = pd.date_range('1/1/2000', periods=8)

In [2]: df = pd.DataFrame(np.random.randn(8, 4),
   ...:                   index=dates, columns=['A', 'B', 'C', 'D'])
   ...: 

In [3]: df
Out[3]: 
                   A         B         C         D
2000-01-01  0.469112 -0.282863 -1.509059 -1.135632
2000-01-02  1.212112 -0.173215  0.119209 -1.044236
2000-01-03 -0.861849 -2.104569 -0.494929  1.071804
2000-01-04  0.721555 -0.706771 -1.039575  0.271860
2000-01-05 -0.424972  0.567020  0.276232 -1.087401
2000-01-06 -0.673690  0.113648 -1.478427  0.524988
2000-01-07  0.404705  0.577046 -1.715002 -1.039268
2000-01-08 -0.370647 -1.157892 -1.344312  0.844885
```



笔记

除非特别说明，否则任何索引功能都不是特定于时间序列的。

因此，如上所述，我们使用最基本的索引`[]`：

```
In [4]: s = df['A']

In [5]: s[dates[5]]
Out[5]: -0.6736897080883706
```



您可以传递列列表以`[]`按该顺序选择列。如果 DataFrame 中不包含列，则会引发异常。也可以这样设置多列：

```
In [6]: df
Out[6]: 
                   A         B         C         D
2000-01-01  0.469112 -0.282863 -1.509059 -1.135632
2000-01-02  1.212112 -0.173215  0.119209 -1.044236
2000-01-03 -0.861849 -2.104569 -0.494929  1.071804
2000-01-04  0.721555 -0.706771 -1.039575  0.271860
2000-01-05 -0.424972  0.567020  0.276232 -1.087401
2000-01-06 -0.673690  0.113648 -1.478427  0.524988
2000-01-07  0.404705  0.577046 -1.715002 -1.039268
2000-01-08 -0.370647 -1.157892 -1.344312  0.844885

In [7]: df[['B', 'A']] = df[['A', 'B']]

In [8]: df
Out[8]: 
                   A         B         C         D
2000-01-01 -0.282863  0.469112 -1.509059 -1.135632
2000-01-02 -0.173215  1.212112  0.119209 -1.044236
2000-01-03 -2.104569 -0.861849 -0.494929  1.071804
2000-01-04 -0.706771  0.721555 -1.039575  0.271860
2000-01-05  0.567020 -0.424972  0.276232 -1.087401
2000-01-06  0.113648 -0.673690 -1.478427  0.524988
2000-01-07  0.577046  0.404705 -1.715002 -1.039268
2000-01-08 -1.157892 -0.370647 -1.344312  0.844885
```



您可能会发现这对于将转换（就地）应用于列的子集很有用。

警告

`Series`pandas 在设置和`DataFrame`时对齐所有轴`.loc`。

这**不会**修改，`df`因为列对齐是在赋值之前。

```
In [9]: df[['A', 'B']]
Out[9]: 
                   A         B
2000-01-01 -0.282863  0.469112
2000-01-02 -0.173215  1.212112
2000-01-03 -2.104569 -0.861849
2000-01-04 -0.706771  0.721555
2000-01-05  0.567020 -0.424972
2000-01-06  0.113648 -0.673690
2000-01-07  0.577046  0.404705
2000-01-08 -1.157892 -0.370647

In [10]: df.loc[:, ['B', 'A']] = df[['A', 'B']]

In [11]: df[['A', 'B']]
Out[11]: 
                   A         B
2000-01-01 -0.282863  0.469112
2000-01-02 -0.173215  1.212112
2000-01-03 -2.104569 -0.861849
2000-01-04 -0.706771  0.721555
2000-01-05  0.567020 -0.424972
2000-01-06  0.113648 -0.673690
2000-01-07  0.577046  0.404705
2000-01-08 -1.157892 -0.370647
```



交换列值的正确方法是使用原始值：

```
In [12]: df.loc[:, ['B', 'A']] = df[['A', 'B']].to_numpy()

In [13]: df[['A', 'B']]
Out[13]: 
                   A         B
2000-01-01  0.469112 -0.282863
2000-01-02  1.212112 -0.173215
2000-01-03 -0.861849 -2.104569
2000-01-04  0.721555 -0.706771
2000-01-05 -0.424972  0.567020
2000-01-06 -0.673690  0.113648
2000-01-07  0.404705  0.577046
2000-01-08 -0.370647 -1.157892
```



`Series`但是，pandas 在设置和`DataFrame`from时不会对齐 AXES，`.iloc` 因为它`.iloc`是按位置操作的。

这将会修改，`df`因为在赋值之前未完成列对齐。

```
In [14]: df[['A', 'B']]
Out[14]: 
                   A         B
2000-01-01  0.469112 -0.282863
2000-01-02  1.212112 -0.173215
2000-01-03 -0.861849 -2.104569
2000-01-04  0.721555 -0.706771
2000-01-05 -0.424972  0.567020
2000-01-06 -0.673690  0.113648
2000-01-07  0.404705  0.577046
2000-01-08 -0.370647 -1.157892

In [15]: df.iloc[:, [1, 0]] = df[['A', 'B']]

In [16]: df[['A','B']]
Out[16]: 
                   A         B
2000-01-01 -0.282863  0.469112
2000-01-02 -0.173215  1.212112
2000-01-03 -2.104569 -0.861849
2000-01-04 -0.706771  0.721555
2000-01-05  0.567020 -0.424972
2000-01-06  0.113648 -0.673690
2000-01-07  0.577046  0.404705
2000-01-08 -1.157892 -0.370647
```



## 属性访问

您可以直接将 a 上的索引`Series`或 a 上的列`DataFrame`作为属性访问：

```
In [17]: sa = pd.Series([1, 2, 3], index=list('abc'))

In [18]: dfa = df.copy()
```



```
In [19]: sa.b
Out[19]: 2

In [20]: dfa.A
Out[20]: 
2000-01-01   -0.282863
2000-01-02   -0.173215
2000-01-03   -2.104569
2000-01-04   -0.706771
2000-01-05    0.567020
2000-01-06    0.113648
2000-01-07    0.577046
2000-01-08   -1.157892
Freq: D, Name: A, dtype: float64
```



```
In [21]: sa.a = 5

In [22]: sa
Out[22]: 
a    5
b    2
c    3
dtype: int64

In [23]: dfa.A = list(range(len(dfa.index)))  # ok if A already exists

In [24]: dfa
Out[24]: 
            A         B         C         D
2000-01-01  0  0.469112 -1.509059 -1.135632
2000-01-02  1  1.212112  0.119209 -1.044236
2000-01-03  2 -0.861849 -0.494929  1.071804
2000-01-04  3  0.721555 -1.039575  0.271860
2000-01-05  4 -0.424972  0.276232 -1.087401
2000-01-06  5 -0.673690 -1.478427  0.524988
2000-01-07  6  0.404705 -1.715002 -1.039268
2000-01-08  7 -0.370647 -1.344312  0.844885

In [25]: dfa['A'] = list(range(len(dfa.index)))  # use this form to create a new column

In [26]: dfa
Out[26]: 
            A         B         C         D
2000-01-01  0  0.469112 -1.509059 -1.135632
2000-01-02  1  1.212112  0.119209 -1.044236
2000-01-03  2 -0.861849 -0.494929  1.071804
2000-01-04  3  0.721555 -1.039575  0.271860
2000-01-05  4 -0.424972  0.276232 -1.087401
2000-01-06  5 -0.673690 -1.478427  0.524988
2000-01-07  6  0.404705 -1.715002 -1.039268
2000-01-08  7 -0.370647 -1.344312  0.844885
```



警告

- 仅当索引元素是有效的 Python 标识符（例如`s.1`不允许）时，您才能使用此访问。[有关有效标识符的说明，](https://docs.python.org/3/reference/lexical_analysis.html#identifiers)请参阅此处。
- `s.min`如果该属性与现有方法名称冲突，例如不允许，但`s['min']`可以使用，则该属性将不可用。
- 同样，如果该属性与以下列表中的任何一个冲突，则该属性将不可用：`index`、 `major_axis`、`minor_axis`、`items`。
- 在任何这些情况下，标准索引仍然有效，例如`s['1']`，，，`s['min']`并将`s['index']`访问相应的元素或列。

如果您使用的是 IPython 环境，您还可以使用制表符补全来查看这些可访问的属性。

您还可以将 a 分配`dict`给 a 的一行`DataFrame`：

```
In [27]: x = pd.DataFrame({'x': [1, 2, 3], 'y': [3, 4, 5]})

In [28]: x.iloc[1] = {'x': 9, 'y': 99}

In [29]: x
Out[29]: 
   x   y
0  1   3
1  9  99
2  3   5
```



您可以使用属性访问来修改 Series 的现有元素或 DataFrame 的列，但要小心；如果您尝试使用属性访问来创建新列，它会创建一个新属性而不是新列，这会引发`UserWarning`：

```
In [30]: df_new = pd.DataFrame({'one': [1., 2., 3.]})

In [31]: df_new.two = [4, 5, 6]

In [32]: df_new
Out[32]: 
   one
0  1.0
1  2.0
2  3.0
```



## 切片范围

沿任意轴切片范围的最稳健且一致的方法在“[按位置选择”](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-integer)部分中详细介绍了该`.iloc`方法。现在，我们解释使用`[]`运算符进行切片的语义。

对于 Series，其语法与 ndarray 完全相同，返回值的切片和相应的标签：

```
In [33]: s[:5]
Out[33]: 
2000-01-01    0.469112
2000-01-02    1.212112
2000-01-03   -0.861849
2000-01-04    0.721555
2000-01-05   -0.424972
Freq: D, Name: A, dtype: float64

In [34]: s[::2]
Out[34]: 
2000-01-01    0.469112
2000-01-03   -0.861849
2000-01-05   -0.424972
2000-01-07    0.404705
Freq: 2D, Name: A, dtype: float64

In [35]: s[::-1]
Out[35]: 
2000-01-08   -0.370647
2000-01-07    0.404705
2000-01-06   -0.673690
2000-01-05   -0.424972
2000-01-04    0.721555
2000-01-03   -0.861849
2000-01-02    1.212112
2000-01-01    0.469112
Freq: -1D, Name: A, dtype: float64
```



请注意，设置也有效：

```
In [36]: s2 = s.copy()

In [37]: s2[:5] = 0

In [38]: s2
Out[38]: 
2000-01-01    0.000000
2000-01-02    0.000000
2000-01-03    0.000000
2000-01-04    0.000000
2000-01-05    0.000000
2000-01-06   -0.673690
2000-01-07    0.404705
2000-01-08   -0.370647
Freq: D, Name: A, dtype: float64
```



`[]` **使用 DataFrame，在切片**内部切片行。这在很大程度上是为了方便而提供的，因为这是一种常见的操作。

```
In [39]: df[:3]
Out[39]: 
                   A         B         C         D
2000-01-01 -0.282863  0.469112 -1.509059 -1.135632
2000-01-02 -0.173215  1.212112  0.119209 -1.044236
2000-01-03 -2.104569 -0.861849 -0.494929  1.071804

In [40]: df[::-1]
Out[40]: 
                   A         B         C         D
2000-01-08 -1.157892 -0.370647 -1.344312  0.844885
2000-01-07  0.577046  0.404705 -1.715002 -1.039268
2000-01-06  0.113648 -0.673690 -1.478427  0.524988
2000-01-05  0.567020 -0.424972  0.276232 -1.087401
2000-01-04 -0.706771  0.721555 -1.039575  0.271860
2000-01-03 -2.104569 -0.861849 -0.494929  1.071804
2000-01-02 -0.173215  1.212112  0.119209 -1.044236
2000-01-01 -0.282863  0.469112 -1.509059 -1.135632
```





## 按标签选择

警告

对于设置操作是否返回副本或引用可以取决于上下文。有时会出现这种情况，应该避免这种情况。请参阅[返回视图与副本](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-view-versus-copy)。`chained assignment`

警告

> `.loc`当您提供与索引类型不兼容（或可转换）的切片器时，它是严格的。例如在 a 中使用整数`DatetimeIndex`。这些将引发`TypeError`.
>
> ```
> In [41]: dfl = pd.DataFrame(np.random.randn(5, 4),
>    ....:                    columns=list('ABCD'),
>    ....:                    index=pd.date_range('20130101', periods=5))
>    ....: 
> 
> In [42]: dfl
> Out[42]: 
>                    A         B         C         D
> 2013-01-01  1.075770 -0.109050  1.643563 -1.469388
> 2013-01-02  0.357021 -0.674600 -1.776904 -0.968914
> 2013-01-03 -1.294524  0.413738  0.276662 -0.472035
> 2013-01-04 -0.013960 -0.362543 -0.006154 -0.923061
> 2013-01-05  0.895717  0.805244 -1.206412  2.565646
> 
> In [43]: dfl.loc[2:3]
> ---------------------------------------------------------------------------
> TypeError                                 Traceback (most recent call last)
> Cell In[43], line 1
> ----> 1 dfl.loc[2:3]
> 
> File ~/work/pandas/pandas/pandas/core/indexing.py:1191, in _LocationIndexer.__getitem__(self, key)
>    1189 maybe_callable = com.apply_if_callable(key, self.obj)
>    1190 maybe_callable = self._check_deprecated_callable_usage(key, maybe_callable)
> -> 1191 return self._getitem_axis(maybe_callable, axis=axis)
> 
> File ~/work/pandas/pandas/pandas/core/indexing.py:1411, in _LocIndexer._getitem_axis(self, key, axis)
>    1409 if isinstance(key, slice):
>    1410     self._validate_key(key, axis)
> -> 1411     return self._get_slice_axis(key, axis=axis)
>    1412 elif com.is_bool_indexer(key):
>    1413     return self._getbool_axis(key, axis=axis)
> 
> File ~/work/pandas/pandas/pandas/core/indexing.py:1443, in _LocIndexer._get_slice_axis(self, slice_obj, axis)
>    1440     return obj.copy(deep=False)
>    1442 labels = obj._get_axis(axis)
> -> 1443 indexer = labels.slice_indexer(slice_obj.start, slice_obj.stop, slice_obj.step)
>    1445 if isinstance(indexer, slice):
>    1446     return self.obj._slice(indexer, axis=axis)
> 
> File ~/work/pandas/pandas/pandas/core/indexes/datetimes.py:682, in DatetimeIndex.slice_indexer(self, start, end, step)
>     674 # GH#33146 if start and end are combinations of str and None and Index is not
>     675 # monotonic, we can not use Index.slice_indexer because it does not honor the
>     676 # actual elements, is only searching for start and end
>     677 if (
>     678     check_str_or_none(start)
>     679     or check_str_or_none(end)
>     680     or self.is_monotonic_increasing
>     681 ):
> --> 682     return Index.slice_indexer(self, start, end, step)
>     684 mask = np.array(True)
>     685 in_index = True
> 
> File ~/work/pandas/pandas/pandas/core/indexes/base.py:6662, in Index.slice_indexer(self, start, end, step)
>    6618 def slice_indexer(
>    6619     self,
>    6620     start: Hashable | None = None,
>    6621     end: Hashable | None = None,
>    6622     step: int | None = None,
>    6623 ) -> slice:
>    6624     """
>    6625     Compute the slice indexer for input labels and step.
>    6626 
>    (...)
>    6660     slice(1, 3, None)
>    6661     """
> -> 6662     start_slice, end_slice = self.slice_locs(start, end, step=step)
>    6664     # return a slice
>    6665     if not is_scalar(start_slice):
> 
> File ~/work/pandas/pandas/pandas/core/indexes/base.py:6879, in Index.slice_locs(self, start, end, step)
>    6877 start_slice = None
>    6878 if start is not None:
> -> 6879     start_slice = self.get_slice_bound(start, "left")
>    6880 if start_slice is None:
>    6881     start_slice = 0
> 
> File ~/work/pandas/pandas/pandas/core/indexes/base.py:6794, in Index.get_slice_bound(self, label, side)
>    6790 original_label = label
>    6792 # For datetime indices label may be a string that has to be converted
>    6793 # to datetime boundary according to its resolution.
> -> 6794 label = self._maybe_cast_slice_bound(label, side)
>    6796 # we need to look up the label
>    6797 try:
> 
> File ~/work/pandas/pandas/pandas/core/indexes/datetimes.py:642, in DatetimeIndex._maybe_cast_slice_bound(self, label, side)
>     637 if isinstance(label, dt.date) and not isinstance(label, dt.datetime):
>     638     # Pandas supports slicing with dates, treated as datetimes at midnight.
>     639     # https://github.com/pandas-dev/pandas/issues/31501
>     640     label = Timestamp(label).to_pydatetime()
> --> 642 label = super()._maybe_cast_slice_bound(label, side)
>     643 self._data._assert_tzawareness_compat(label)
>     644 return Timestamp(label)
> 
> File ~/work/pandas/pandas/pandas/core/indexes/datetimelike.py:378, in DatetimeIndexOpsMixin._maybe_cast_slice_bound(self, label, side)
>     376     return lower if side == "left" else upper
>     377 elif not isinstance(label, self._data._recognized_scalars):
> --> 378     self._raise_invalid_indexer("slice", label)
>     380 return label
> 
> File ~/work/pandas/pandas/pandas/core/indexes/base.py:4301, in Index._raise_invalid_indexer(self, form, key, reraise)
>    4299 if reraise is not lib.no_default:
>    4300     raise TypeError(msg) from reraise
> -> 4301 raise TypeError(msg)
> 
> TypeError: cannot do slice indexing on DatetimeIndex with these indexers [2] of type int
> ```

切片中的字符串*可以*转换为索引的类型并导致自然切片。

```
In [44]: dfl.loc['20130102':'20130104']
Out[44]: 
                   A         B         C         D
2013-01-02  0.357021 -0.674600 -1.776904 -0.968914
2013-01-03 -1.294524  0.413738  0.276662 -0.472035
2013-01-04 -0.013960 -0.362543 -0.006154 -0.923061
```



pandas 提供了一套方法来实现**纯粹基于标签的索引**。这是一个严格的基于包含的协议。要求的每个标签都必须在索引中，否则`KeyError`将引发 a 。切片时，如果索引中存在，则*包含*开始边界**和**停止边界。整数是有效的标签，但它们指的是标签**而不是位置**。

属性`.loc`是主要的访问方法。以下是有效的输入：

- 单个标签，例如`5`or `'a'`（请注意，它`5`被解释为索引的*标签。此用途***不是**沿索引的整数位置。）。
- 标签列表或数组。`['a', 'b', 'c']`
- 带有标签的切片对象`'a':'f'`（请注意，与通常的 Python 切片相反，当索引中存在时，起始点和终止点**都包含在内！请参阅**[使用标签切片](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-slicing-with-labels)。
- 一个布尔数组。
- A `callable`，请参阅[按可调用选择](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-callable)。

```
In [45]: s1 = pd.Series(np.random.randn(6), index=list('abcdef'))

In [46]: s1
Out[46]: 
a    1.431256
b    1.340309
c   -1.170299
d   -0.226169
e    0.410835
f    0.813850
dtype: float64

In [47]: s1.loc['c':]
Out[47]: 
c   -1.170299
d   -0.226169
e    0.410835
f    0.813850
dtype: float64

In [48]: s1.loc['b']
Out[48]: 1.3403088497993827
```



请注意，设置也有效：

```
In [49]: s1.loc['c':] = 0

In [50]: s1
Out[50]: 
a    1.431256
b    1.340309
c    0.000000
d    0.000000
e    0.000000
f    0.000000
dtype: float64
```



使用数据框：

```
In [51]: df1 = pd.DataFrame(np.random.randn(6, 4),
   ....:                    index=list('abcdef'),
   ....:                    columns=list('ABCD'))
   ....: 

In [52]: df1
Out[52]: 
          A         B         C         D
a  0.132003 -0.827317 -0.076467 -1.187678
b  1.130127 -1.436737 -1.413681  1.607920
c  1.024180  0.569605  0.875906 -2.211372
d  0.974466 -2.006747 -0.410001 -0.078638
e  0.545952 -1.219217 -1.226825  0.769804
f -1.281247 -0.727707 -0.121306 -0.097883

In [53]: df1.loc[['a', 'b', 'd'], :]
Out[53]: 
          A         B         C         D
a  0.132003 -0.827317 -0.076467 -1.187678
b  1.130127 -1.436737 -1.413681  1.607920
d  0.974466 -2.006747 -0.410001 -0.078638
```



通过标签切片访问：

```
In [54]: df1.loc['d':, 'A':'C']
Out[54]: 
          A         B         C
d  0.974466 -2.006747 -0.410001
e  0.545952 -1.219217 -1.226825
f -1.281247 -0.727707 -0.121306
```



要使用标签获取横截面（相当于`df.xs('a')`）：

```
In [55]: df1.loc['a']
Out[55]: 
A    0.132003
B   -0.827317
C   -0.076467
D   -1.187678
Name: a, dtype: float64
```



使用布尔数组获取值：

```
In [56]: df1.loc['a'] > 0
Out[56]: 
A     True
B    False
C    False
D    False
Name: a, dtype: bool

In [57]: df1.loc[:, df1.loc['a'] > 0]
Out[57]: 
          A
a  0.132003
b  1.130127
c  1.024180
d  0.974466
e  0.545952
f -1.281247
```



布尔数组中的 NA 值传播为`False`：

```
In [58]: mask = pd.array([True, False, True, False, pd.NA, False], dtype="boolean")

In [59]: mask
Out[59]: 
<BooleanArray>
[True, False, True, False, <NA>, False]
Length: 6, dtype: boolean

In [60]: df1[mask]
Out[60]: 
          A         B         C         D
a  0.132003 -0.827317 -0.076467 -1.187678
c  1.024180  0.569605  0.875906 -2.211372
```



为了显式获取值：

```
# this is also equivalent to ``df1.at['a','A']``
In [61]: df1.loc['a', 'A']
Out[61]: 0.13200317033032932
```





### 带标签切片

与切片一起使用时`.loc`，如果索引中同时存在开始和停止标签，则返回位于两者之间（包括它们）*的元素：*

```
In [62]: s = pd.Series(list('abcde'), index=[0, 3, 2, 5, 4])

In [63]: s.loc[3:5]
Out[63]: 
3    b
2    c
5    d
dtype: object
```



如果两者中至少有一个不存在，但索引已排序，并且可以与开始和停止标签进行比较，则通过选择*排名*在两者之间的标签，切片仍将按预期工作：

```
In [64]: s.sort_index()
Out[64]: 
0    a
2    c
3    b
4    e
5    d
dtype: object

In [65]: s.sort_index().loc[1:6]
Out[65]: 
2    c
3    b
4    e
5    d
dtype: object
```



但是，如果两者中至少有一个不存在*并且*索引未排序，则会引发错误（因为否则计算量会很大，并且对于混合类型索引可能不明确）。例如，在上面的例子中，`s.loc[1:6]`会引发`KeyError`.

有关此行为背后的基本原理，请参阅 [端点具有包容性](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced-endpoints-are-inclusive)。

```
In [66]: s = pd.Series(list('abcdef'), index=[0, 3, 2, 5, 4, 2])

In [67]: s.loc[3:5]
Out[67]: 
3    b
2    c
5    d
dtype: object
```



此外，如果索引具有重复的标签*，并且*开始或停止标签重复，则会引发错误。例如，在上面的例子中，`s.loc[2:5]`会引发一个`KeyError`.

有关重复标签的更多信息，请参阅 [重复标签](https://pandas.pydata.org/docs/user_guide/duplicates.html#duplicates)。



## 按位置

警告

对于设置操作是否返回副本或引用可以取决于上下文。有时会出现这种情况，应该避免这种情况。请参阅[返回视图与副本](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-view-versus-copy)。`chained assignment`

pandas 提供了一套方法来获得**纯粹基于整数的索引**。语义紧密遵循 Python 和 NumPy 切片。这些都是`0-based`索引。切片时，*包含*起始边界，而*排除*上限。尝试使用非整数，即使是**有效的**标签也会引发`IndexError`.

属性`.iloc`是主要的访问方法。以下是有效的输入：

- 一个整数，例如`5`。
- 整数列表或数组。`[4, 3, 0]`
- 带有 int 的切片对象`1:7`。
- 一个布尔数组。
- A `callable`，请参阅[按可调用选择](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-callable)。
- 行（和列）索引的元组，其元素是上述类型之一。

```
In [68]: s1 = pd.Series(np.random.randn(5), index=list(range(0, 10, 2)))

In [69]: s1
Out[69]: 
0    0.695775
2    0.341734
4    0.959726
6   -1.110336
8   -0.619976
dtype: float64

In [70]: s1.iloc[:3]
Out[70]: 
0    0.695775
2    0.341734
4    0.959726
dtype: float64

In [71]: s1.iloc[3]
Out[71]: -1.110336102891167
```



请注意，设置也有效：

```
In [72]: s1.iloc[:3] = 0

In [73]: s1
Out[73]: 
0    0.000000
2    0.000000
4    0.000000
6   -1.110336
8   -0.619976
dtype: float64
```



使用数据框：

```
In [74]: df1 = pd.DataFrame(np.random.randn(6, 4),
   ....:                    index=list(range(0, 12, 2)),
   ....:                    columns=list(range(0, 8, 2)))
   ....: 

In [75]: df1
Out[75]: 
           0         2         4         6
0   0.149748 -0.732339  0.687738  0.176444
2   0.403310 -0.154951  0.301624 -2.179861
4  -1.369849 -0.954208  1.462696 -1.743161
6  -0.826591 -0.345352  1.314232  0.690579
8   0.995761  2.396780  0.014871  3.357427
10 -0.317441 -1.236269  0.896171 -0.487602
```



通过整数切片选择：

```
In [76]: df1.iloc[:3]
Out[76]: 
          0         2         4         6
0  0.149748 -0.732339  0.687738  0.176444
2  0.403310 -0.154951  0.301624 -2.179861
4 -1.369849 -0.954208  1.462696 -1.743161

In [77]: df1.iloc[1:5, 2:4]
Out[77]: 
          4         6
2  0.301624 -2.179861
4  1.462696 -1.743161
6  1.314232  0.690579
8  0.014871  3.357427
```



通过整数列表选择：

```
In [78]: df1.iloc[[1, 3, 5], [1, 3]]
Out[78]: 
           2         6
2  -0.154951 -2.179861
6  -0.345352  0.690579
10 -1.236269 -0.487602
```



```
In [79]: df1.iloc[1:3, :]
Out[79]: 
          0         2         4         6
2  0.403310 -0.154951  0.301624 -2.179861
4 -1.369849 -0.954208  1.462696 -1.743161
```



```
In [80]: df1.iloc[:, 1:3]
Out[80]: 
           2         4
0  -0.732339  0.687738
2  -0.154951  0.301624
4  -0.954208  1.462696
6  -0.345352  1.314232
8   2.396780  0.014871
10 -1.236269  0.896171
```



```
# this is also equivalent to ``df1.iat[1,1]``
In [81]: df1.iloc[1, 1]
Out[81]: -0.1549507744249032
```



要使用整数位置获取横截面（相当于`df.xs(1)`）：

```
In [82]: df1.iloc[1]
Out[82]: 
0    0.403310
2   -0.154951
4    0.301624
6   -2.179861
Name: 2, dtype: float64
```



超出范围的切片索引会像在 Python/NumPy 中一样得到妥善处理。

```
# these are allowed in Python/NumPy.
In [83]: x = list('abcdef')

In [84]: x
Out[84]: ['a', 'b', 'c', 'd', 'e', 'f']

In [85]: x[4:10]
Out[85]: ['e', 'f']

In [86]: x[8:10]
Out[86]: []

In [87]: s = pd.Series(x)

In [88]: s
Out[88]: 
0    a
1    b
2    c
3    d
4    e
5    f
dtype: object

In [89]: s.iloc[4:10]
Out[89]: 
4    e
5    f
dtype: object

In [90]: s.iloc[8:10]
Out[90]: Series([], dtype: object)
```



请注意，使用超出范围的切片可能会导致空轴（例如返回空的 DataFrame）。

```
In [91]: dfl = pd.DataFrame(np.random.randn(5, 2), columns=list('AB'))

In [92]: dfl
Out[92]: 
          A         B
0 -0.082240 -2.182937
1  0.380396  0.084844
2  0.432390  1.519970
3 -0.493662  0.600178
4  0.274230  0.132885

In [93]: dfl.iloc[:, 2:3]
Out[93]: 
Empty DataFrame
Columns: []
Index: [0, 1, 2, 3, 4]

In [94]: dfl.iloc[:, 1:3]
Out[94]: 
          B
0 -2.182937
1  0.084844
2  1.519970
3  0.600178
4  0.132885

In [95]: dfl.iloc[4:6]
Out[95]: 
         A         B
4  0.27423  0.132885
```



超出范围的单个索引器将引发`IndexError`.任何元素超出范围的索引器列表都会引发 `IndexError`.

```
In [96]: dfl.iloc[[4, 5, 6]]
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
File ~/work/pandas/pandas/pandas/core/indexing.py:1714, in _iLocIndexer._get_list_axis(self, key, axis)
   1713 try:
-> 1714     return self.obj._take_with_is_copy(key, axis=axis)
   1715 except IndexError as err:
   1716     # re-raise with different error message, e.g. test_getitem_ndarray_3d

File ~/work/pandas/pandas/pandas/core/generic.py:4150, in NDFrame._take_with_is_copy(self, indices, axis)
   4141 """
   4142 Internal version of the `take` method that sets the `_is_copy`
   4143 attribute to keep track of the parent dataframe (using in indexing
   (...)
   4148 See the docstring of `take` for full explanation of the parameters.
   4149 """
-> 4150 result = self.take(indices=indices, axis=axis)
   4151 # Maybe set copy if we didn't actually change the index.

File ~/work/pandas/pandas/pandas/core/generic.py:4130, in NDFrame.take(self, indices, axis, **kwargs)
   4126     indices = np.arange(
   4127         indices.start, indices.stop, indices.step, dtype=np.intp
   4128     )
-> 4130 new_data = self._mgr.take(
   4131     indices,
   4132     axis=self._get_block_manager_axis(axis),
   4133     verify=True,
   4134 )
   4135 return self._constructor_from_mgr(new_data, axes=new_data.axes).__finalize__(
   4136     self, method="take"
   4137 )

File ~/work/pandas/pandas/pandas/core/internals/managers.py:891, in BaseBlockManager.take(self, indexer, axis, verify)
    890 n = self.shape[axis]
--> 891 indexer = maybe_convert_indices(indexer, n, verify=verify)
    893 new_labels = self.axes[axis].take(indexer)

File ~/work/pandas/pandas/pandas/core/indexers/utils.py:282, in maybe_convert_indices(indices, n, verify)
    281     if mask.any():
--> 282         raise IndexError("indices are out-of-bounds")
    283 return indices

IndexError: indices are out-of-bounds

The above exception was the direct cause of the following exception:

IndexError                                Traceback (most recent call last)
Cell In[96], line 1
----> 1 dfl.iloc[[4, 5, 6]]

File ~/work/pandas/pandas/pandas/core/indexing.py:1191, in _LocationIndexer.__getitem__(self, key)
   1189 maybe_callable = com.apply_if_callable(key, self.obj)
   1190 maybe_callable = self._check_deprecated_callable_usage(key, maybe_callable)
-> 1191 return self._getitem_axis(maybe_callable, axis=axis)

File ~/work/pandas/pandas/pandas/core/indexing.py:1743, in _iLocIndexer._getitem_axis(self, key, axis)
   1741 # a list of integers
   1742 elif is_list_like_indexer(key):
-> 1743     return self._get_list_axis(key, axis=axis)
   1745 # a single integer
   1746 else:
   1747     key = item_from_zerodim(key)

File ~/work/pandas/pandas/pandas/core/indexing.py:1717, in _iLocIndexer._get_list_axis(self, key, axis)
   1714     return self.obj._take_with_is_copy(key, axis=axis)
   1715 except IndexError as err:
   1716     # re-raise with different error message, e.g. test_getitem_ndarray_3d
-> 1717     raise IndexError("positional indexers are out-of-bounds") from err

IndexError: positional indexers are out-of-bounds
```



```
In [97]: dfl.iloc[:, 4]
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
Cell In[97], line 1
----> 1 dfl.iloc[:, 4]

File ~/work/pandas/pandas/pandas/core/indexing.py:1184, in _LocationIndexer.__getitem__(self, key)
   1182     if self._is_scalar_access(key):
   1183         return self.obj._get_value(*key, takeable=self._takeable)
-> 1184     return self._getitem_tuple(key)
   1185 else:
   1186     # we by definition only have the 0th axis
   1187     axis = self.axis or 0

File ~/work/pandas/pandas/pandas/core/indexing.py:1690, in _iLocIndexer._getitem_tuple(self, tup)
   1689 def _getitem_tuple(self, tup: tuple):
-> 1690     tup = self._validate_tuple_indexer(tup)
   1691     with suppress(IndexingError):
   1692         return self._getitem_lowerdim(tup)

File ~/work/pandas/pandas/pandas/core/indexing.py:966, in _LocationIndexer._validate_tuple_indexer(self, key)
    964 for i, k in enumerate(key):
    965     try:
--> 966         self._validate_key(k, i)
    967     except ValueError as err:
    968         raise ValueError(
    969             "Location based indexing can only have "
    970             f"[{self._valid_types}] types"
    971         ) from err

File ~/work/pandas/pandas/pandas/core/indexing.py:1592, in _iLocIndexer._validate_key(self, key, axis)
   1590     return
   1591 elif is_integer(key):
-> 1592     self._validate_integer(key, axis)
   1593 elif isinstance(key, tuple):
   1594     # a tuple should already have been caught by this point
   1595     # so don't treat a tuple as a valid indexer
   1596     raise IndexingError("Too many indexers")

File ~/work/pandas/pandas/pandas/core/indexing.py:1685, in _iLocIndexer._validate_integer(self, key, axis)
   1683 len_axis = len(self.obj._get_axis(axis))
   1684 if key >= len_axis or key < -len_axis:
-> 1685     raise IndexError("single positional indexer is out-of-bounds")

IndexError: single positional indexer is out-of-bounds
```





## 进行选择

`.loc`、`.iloc`、 以及`[]`索引也可以接受 a`callable`作为索引器。必须`callable`是一个带有一个参数（调用 Series 或 DataFrame）的函数，该函数返回有效的索引输出。

笔记

对于`.iloc`索引，不支持从可调用对象返回元组，因为行和列索引的元组解构发生在应用可调用对象*之前*。

```
In [98]: df1 = pd.DataFrame(np.random.randn(6, 4),
   ....:                    index=list('abcdef'),
   ....:                    columns=list('ABCD'))
   ....: 

In [99]: df1
Out[99]: 
          A         B         C         D
a -0.023688  2.410179  1.450520  0.206053
b -0.251905 -2.213588  1.063327  1.266143
c  0.299368 -0.863838  0.408204 -1.048089
d -0.025747 -0.988387  0.094055  1.262731
e  1.289997  0.082423 -0.055758  0.536580
f -0.489682  0.369374 -0.034571 -2.484478

In [100]: df1.loc[lambda df: df['A'] > 0, :]
Out[100]: 
          A         B         C         D
c  0.299368 -0.863838  0.408204 -1.048089
e  1.289997  0.082423 -0.055758  0.536580

In [101]: df1.loc[:, lambda df: ['A', 'B']]
Out[101]: 
          A         B
a -0.023688  2.410179
b -0.251905 -2.213588
c  0.299368 -0.863838
d -0.025747 -0.988387
e  1.289997  0.082423
f -0.489682  0.369374

In [102]: df1.iloc[:, lambda df: [0, 1]]
Out[102]: 
          A         B
a -0.023688  2.410179
b -0.251905 -2.213588
c  0.299368 -0.863838
d -0.025747 -0.988387
e  1.289997  0.082423
f -0.489682  0.369374

In [103]: df1[lambda df: df.columns[0]]
Out[103]: 
a   -0.023688
b   -0.251905
c    0.299368
d   -0.025747
e    1.289997
f   -0.489682
Name: A, dtype: float64
```



您可以在`Series`.

```
In [104]: df1['A'].loc[lambda s: s > 0]
Out[104]: 
c    0.299368
e    1.289997
Name: A, dtype: float64
```



使用这些方法/索引器，您可以链接数据选择操作，而无需使用临时变量。

```
In [105]: bb = pd.read_csv('data/baseball.csv', index_col='id')

In [106]: (bb.groupby(['year', 'team']).sum(numeric_only=True)
   .....:    .loc[lambda df: df['r'] > 100])
   .....: 
Out[106]: 
           stint    g    ab    r    h  X2b  ...     so   ibb   hbp    sh    sf  gidp
year team                                   ...                                     
2007 CIN       6  379   745  101  203   35  ...  127.0  14.0   1.0   1.0  15.0  18.0
     DET       5  301  1062  162  283   54  ...  176.0   3.0  10.0   4.0   8.0  28.0
     HOU       4  311   926  109  218   47  ...  212.0   3.0   9.0  16.0   6.0  17.0
     LAN      11  413  1021  153  293   61  ...  141.0   8.0   9.0   3.0   8.0  29.0
     NYN      13  622  1854  240  509  101  ...  310.0  24.0  23.0  18.0  15.0  48.0
     SFN       5  482  1305  198  337   67  ...  188.0  51.0   8.0  16.0   6.0  41.0
     TEX       2  198   729  115  200   40  ...  140.0   4.0   5.0   2.0   8.0  16.0
     TOR       4  459  1408  187  378   96  ...  265.0  16.0  12.0   4.0  16.0  38.0

[8 rows x 18 columns]
```





## 结合位置索引和基于标签的索引

如果您希望从“A”列中的索引获取第 0 个和第 2 个元素，您可以执行以下操作：

```
In [107]: dfd = pd.DataFrame({'A': [1, 2, 3],
   .....:                     'B': [4, 5, 6]},
   .....:                    index=list('abc'))
   .....: 

In [108]: dfd
Out[108]: 
   A  B
a  1  4
b  2  5
c  3  6

In [109]: dfd.loc[dfd.index[[0, 2]], 'A']
Out[109]: 
a    1
c    3
Name: A, dtype: int64
```



这也可以使用 来表达`.iloc`，通过显式获取索引器上的位置，并使用 *位置*索引来选择事物。

```
In [110]: dfd.iloc[[0, 2], dfd.columns.get_loc('A')]
Out[110]: 
a    1
c    3
Name: A, dtype: int64
```



要获取*多个*索引器，请使用`.get_indexer`：

```
In [111]: dfd.iloc[[0, 2], dfd.columns.get_indexer(['A', 'B'])]
Out[111]: 
   A  B
a  1  4
c  3  6
```



### 重新索引

实现选择可能未找到的元素的惯用方法是通过`.reindex()`。另请参阅有关[重新索引](https://pandas.pydata.org/docs/user_guide/basics.html#basics-reindexing)的部分。

```
In [112]: s = pd.Series([1, 2, 3])

In [113]: s.reindex([1, 2, 3])
Out[113]: 
1    2.0
2    3.0
3    NaN
dtype: float64
```



或者，如果您只想选择*有效的*键，以下是惯用且高效的；它保证保留选择的数据类型。

```
In [114]: labels = [1, 2, 3]

In [115]: s.loc[s.index.intersection(labels)]
Out[115]: 
1    2
2    3
dtype: int64
```



拥有重复的索引将会引发`.reindex()`：

```
In [116]: s = pd.Series(np.arange(4), index=['a', 'a', 'b', 'c'])

In [117]: labels = ['c', 'd']

In [118]: s.reindex(labels)
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[118], line 1
----> 1 s.reindex(labels)

File ~/work/pandas/pandas/pandas/core/series.py:5144, in Series.reindex(self, index, axis, method, copy, level, fill_value, limit, tolerance)
   5127 @doc(
   5128     NDFrame.reindex,  # type: ignore[has-type]
   5129     klass=_shared_doc_kwargs["klass"],
   (...)
   5142     tolerance=None,
   5143 ) -> Series:
-> 5144     return super().reindex(
   5145         index=index,
   5146         method=method,
   5147         copy=copy,
   5148         level=level,
   5149         fill_value=fill_value,
   5150         limit=limit,
   5151         tolerance=tolerance,
   5152     )

File ~/work/pandas/pandas/pandas/core/generic.py:5607, in NDFrame.reindex(self, labels, index, columns, axis, method, copy, level, fill_value, limit, tolerance)
   5604     return self._reindex_multi(axes, copy, fill_value)
   5606 # perform the reindex on the axes
-> 5607 return self._reindex_axes(
   5608     axes, level, limit, tolerance, method, fill_value, copy
   5609 ).__finalize__(self, method="reindex")

File ~/work/pandas/pandas/pandas/core/generic.py:5630, in NDFrame._reindex_axes(self, axes, level, limit, tolerance, method, fill_value, copy)
   5627     continue
   5629 ax = self._get_axis(a)
-> 5630 new_index, indexer = ax.reindex(
   5631     labels, level=level, limit=limit, tolerance=tolerance, method=method
   5632 )
   5634 axis = self._get_axis_number(a)
   5635 obj = obj._reindex_with_indexers(
   5636     {axis: [new_index, indexer]},
   5637     fill_value=fill_value,
   5638     copy=copy,
   5639     allow_dups=False,
   5640 )

File ~/work/pandas/pandas/pandas/core/indexes/base.py:4429, in Index.reindex(self, target, method, level, limit, tolerance)
   4426     raise ValueError("cannot handle a non-unique multi-index!")
   4427 elif not self.is_unique:
   4428     # GH#42568
-> 4429     raise ValueError("cannot reindex on an axis with duplicate labels")
   4430 else:
   4431     indexer, _ = self.get_indexer_non_unique(target)

ValueError: cannot reindex on an axis with duplicate labels
```



一般来说，您可以将所需的标签与当前轴相交，然后重新索引。

```
In [119]: s.loc[s.index.intersection(labels)].reindex(labels)
Out[119]: 
c    3.0
d    NaN
dtype: float64
```



但是，如果生成的索引重复，这*仍然会引发。*

```
In [120]: labels = ['a', 'd']

In [121]: s.loc[s.index.intersection(labels)].reindex(labels)
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[121], line 1
----> 1 s.loc[s.index.intersection(labels)].reindex(labels)

File ~/work/pandas/pandas/pandas/core/series.py:5144, in Series.reindex(self, index, axis, method, copy, level, fill_value, limit, tolerance)
   5127 @doc(
   5128     NDFrame.reindex,  # type: ignore[has-type]
   5129     klass=_shared_doc_kwargs["klass"],
   (...)
   5142     tolerance=None,
   5143 ) -> Series:
-> 5144     return super().reindex(
   5145         index=index,
   5146         method=method,
   5147         copy=copy,
   5148         level=level,
   5149         fill_value=fill_value,
   5150         limit=limit,
   5151         tolerance=tolerance,
   5152     )

File ~/work/pandas/pandas/pandas/core/generic.py:5607, in NDFrame.reindex(self, labels, index, columns, axis, method, copy, level, fill_value, limit, tolerance)
   5604     return self._reindex_multi(axes, copy, fill_value)
   5606 # perform the reindex on the axes
-> 5607 return self._reindex_axes(
   5608     axes, level, limit, tolerance, method, fill_value, copy
   5609 ).__finalize__(self, method="reindex")

File ~/work/pandas/pandas/pandas/core/generic.py:5630, in NDFrame._reindex_axes(self, axes, level, limit, tolerance, method, fill_value, copy)
   5627     continue
   5629 ax = self._get_axis(a)
-> 5630 new_index, indexer = ax.reindex(
   5631     labels, level=level, limit=limit, tolerance=tolerance, method=method
   5632 )
   5634 axis = self._get_axis_number(a)
   5635 obj = obj._reindex_with_indexers(
   5636     {axis: [new_index, indexer]},
   5637     fill_value=fill_value,
   5638     copy=copy,
   5639     allow_dups=False,
   5640 )

File ~/work/pandas/pandas/pandas/core/indexes/base.py:4429, in Index.reindex(self, target, method, level, limit, tolerance)
   4426     raise ValueError("cannot handle a non-unique multi-index!")
   4427 elif not self.is_unique:
   4428     # GH#42568
-> 4429     raise ValueError("cannot reindex on an axis with duplicate labels")
   4430 else:
   4431     indexer, _ = self.get_indexer_non_unique(target)

ValueError: cannot reindex on an axis with duplicate labels
```





## 选择随机样本

使用该方法从 Series 或 DataFrame 中随机选择行或列[`sample()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sample.html#pandas.DataFrame.sample)。该方法默认会对行进行采样，并接受要返回的特定数量的行/列或行的一部分。

```
In [122]: s = pd.Series([0, 1, 2, 3, 4, 5])

# When no arguments are passed, returns 1 row.
In [123]: s.sample()
Out[123]: 
4    4
dtype: int64

# One may specify either a number of rows:
In [124]: s.sample(n=3)
Out[124]: 
0    0
4    4
1    1
dtype: int64

# Or a fraction of the rows:
In [125]: s.sample(frac=0.5)
Out[125]: 
5    5
3    3
1    1
dtype: int64
```



默认情况下，`sample`每行最多返回一次，但也可以使用以下`replace`选项进行替换采样：

```
In [126]: s = pd.Series([0, 1, 2, 3, 4, 5])

# Without replacement (default):
In [127]: s.sample(n=6, replace=False)
Out[127]: 
0    0
1    1
5    5
3    3
2    2
4    4
dtype: int64

# With replacement:
In [128]: s.sample(n=6, replace=True)
Out[128]: 
0    0
4    4
3    3
2    2
4    4
4    4
dtype: int64
```



默认情况下，每行被选择的概率相等，但如果您希望行具有不同的概率，可以将`sample`函数采样权重传递为 `weights`。这些权重可以是列表、NumPy 数组或系列，但它们的长度必须与要采样的对象的长度相同。缺失值将被视为权重为零，并且不允许使用 inf 值。如果权重总和不等于 1，则将通过将所有权重除以权重总和来重新标准化它们。例如：

```
In [129]: s = pd.Series([0, 1, 2, 3, 4, 5])

In [130]: example_weights = [0, 0, 0.2, 0.2, 0.2, 0.4]

In [131]: s.sample(n=3, weights=example_weights)
Out[131]: 
5    5
4    4
3    3
dtype: int64

# Weights will be re-normalized automatically
In [132]: example_weights2 = [0.5, 0, 0, 0, 0, 0]

In [133]: s.sample(n=1, weights=example_weights2)
Out[133]: 
0    0
dtype: int64
```



当应用于 DataFrame 时，您可以通过简单地将列名称作为字符串传递来使用 DataFrame 的列作为采样权重（假设您对行而不是列进行采样）。

```
In [134]: df2 = pd.DataFrame({'col1': [9, 8, 7, 6],
   .....:                     'weight_column': [0.5, 0.4, 0.1, 0]})
   .....: 

In [135]: df2.sample(n=3, weights='weight_column')
Out[135]: 
   col1  weight_column
1     8            0.4
0     9            0.5
2     7            0.1
```



`sample`还允许用户使用参数对列而不是行进行采样`axis`。

```
In [136]: df3 = pd.DataFrame({'col1': [1, 2, 3], 'col2': [2, 3, 4]})

In [137]: df3.sample(n=1, axis=1)
Out[137]: 
   col1
0     1
1     2
2     3
```



`sample`最后，还可以使用参数为 的随机数生成器设置种子`random_state`，该参数将接受整数（作为种子）或 NumPy RandomState 对象。

```
In [138]: df4 = pd.DataFrame({'col1': [1, 2, 3], 'col2': [2, 3, 4]})

# With a given seed, the sample will always draw the same rows.
In [139]: df4.sample(n=2, random_state=2)
Out[139]: 
   col1  col2
2     3     4
1     2     3

In [140]: df4.sample(n=2, random_state=2)
Out[140]: 
   col1  col2
2     3     4
1     2     3
```



## 放大设置

`.loc/[]`当为该轴设置不存在的关键点时，操作可以执行放大。

在这种`Series`情况下，这实际上是一个附加操作。

```
In [141]: se = pd.Series([1, 2, 3])

In [142]: se
Out[142]: 
0    1
1    2
2    3
dtype: int64

In [143]: se[5] = 5.

In [144]: se
Out[144]: 
0    1.0
1    2.0
2    3.0
5    5.0
dtype: float64
```



A`DataFrame`可以通过 沿任一轴放大`.loc`。

```
In [145]: dfi = pd.DataFrame(np.arange(6).reshape(3, 2),
   .....:                    columns=['A', 'B'])
   .....: 

In [146]: dfi
Out[146]: 
   A  B
0  0  1
1  2  3
2  4  5

In [147]: dfi.loc[:, 'C'] = dfi.loc[:, 'A']

In [148]: dfi
Out[148]: 
   A  B  C
0  0  1  0
1  2  3  2
2  4  5  4
```



这就像`append`对 进行操作`DataFrame`。

```
In [149]: dfi.loc[3] = 5

In [150]: dfi
Out[150]: 
   A  B  C
0  0  1  0
1  2  3  2
2  4  5  4
3  5  5  5
```





## 快速标量值获取和设置

由于索引`[]`必须处理很多情况（单标签访问、切片、布尔索引等），因此它需要一些开销才能弄清楚您所要求的内容。如果只想访问标量值，最快的方法是使用`at`和`iat`方法，它们在所有数据结构上实现。

与 类似`loc`，`at`提供基于**标签的**标量查找，同时，`iat`提供基于**整数的**查找，类似于`iloc`

```
In [151]: s.iat[5]
Out[151]: 5

In [152]: df.at[dates[5], 'A']
Out[152]: 0.1136484096888855

In [153]: df.iat[3, 0]
Out[153]: -0.7067711336300845
```



您还可以使用这些相同的索引器进行设置。

```
In [154]: df.at[dates[5], 'E'] = 7

In [155]: df.iat[3, 0] = 7
```



`at`如果索引器丢失，可能会按上述方式就地放大对象。

```
In [156]: df.at[dates[-1] + pd.Timedelta('1 day'), 0] = 7

In [157]: df
Out[157]: 
                   A         B         C         D    E    0
2000-01-01 -0.282863  0.469112 -1.509059 -1.135632  NaN  NaN
2000-01-02 -0.173215  1.212112  0.119209 -1.044236  NaN  NaN
2000-01-03 -2.104569 -0.861849 -0.494929  1.071804  NaN  NaN
2000-01-04  7.000000  0.721555 -1.039575  0.271860  NaN  NaN
2000-01-05  0.567020 -0.424972  0.276232 -1.087401  NaN  NaN
2000-01-06  0.113648 -0.673690 -1.478427  0.524988  7.0  NaN
2000-01-07  0.577046  0.404705 -1.715002 -1.039268  NaN  NaN
2000-01-08 -1.157892 -0.370647 -1.344312  0.844885  NaN  NaN
2000-01-09       NaN       NaN       NaN       NaN  NaN  7.0
```



## 布尔索引

另一个常见的操作是使用布尔向量来过滤数据。运算符是：`|`for `or`、`&`for`and`和`~`for `not`。这些**必须**使用括号进行分组，因为默认情况下 Python 将计算诸如as 之类 的表达式，而所需的计算顺序是 。`df['A'] > 2 & df['B'] < 3``df['A'] > (2 & df['B']) < 3``(df['A'] > 2) & (df['B'] < 3)`

使用布尔向量来索引 Series 的工作方式与 NumPy ndarray 中的工作方式完全相同：

```
In [158]: s = pd.Series(range(-3, 4))

In [159]: s
Out[159]: 
0   -3
1   -2
2   -1
3    0
4    1
5    2
6    3
dtype: int64

In [160]: s[s > 0]
Out[160]: 
4    1
5    2
6    3
dtype: int64

In [161]: s[(s < -1) | (s > 0.5)]
Out[161]: 
0   -3
1   -2
4    1
5    2
6    3
dtype: int64

In [162]: s[~(s < 0)]
Out[162]: 
3    0
4    1
5    2
6    3
dtype: int64
```



您可以使用与 DataFrame 索引长度相同的布尔向量从 DataFrame 中选择行（例如，从 DataFrame 的某一列派生的内容）：

```
In [163]: df[df['A'] > 0]
Out[163]: 
                   A         B         C         D    E   0
2000-01-04  7.000000  0.721555 -1.039575  0.271860  NaN NaN
2000-01-05  0.567020 -0.424972  0.276232 -1.087401  NaN NaN
2000-01-06  0.113648 -0.673690 -1.478427  0.524988  7.0 NaN
2000-01-07  0.577046  0.404705 -1.715002 -1.039268  NaN NaN
```



列表推导式和`map`Series 方法也可用于生成更复杂的标准：

```
In [164]: df2 = pd.DataFrame({'a': ['one', 'one', 'two', 'three', 'two', 'one', 'six'],
   .....:                     'b': ['x', 'y', 'y', 'x', 'y', 'x', 'x'],
   .....:                     'c': np.random.randn(7)})
   .....: 

# only want 'two' or 'three'
In [165]: criterion = df2['a'].map(lambda x: x.startswith('t'))

In [166]: df2[criterion]
Out[166]: 
       a  b         c
2    two  y  0.041290
3  three  x  0.361719
4    two  y -0.238075

# equivalent but slower
In [167]: df2[[x.startswith('t') for x in df2['a']]]
Out[167]: 
       a  b         c
2    two  y  0.041290
3  three  x  0.361719
4    two  y -0.238075

# Multiple criteria
In [168]: df2[criterion & (df2['b'] == 'x')]
Out[168]: 
       a  b         c
3  three  x  0.361719
```



通过选择方法[Selection by Label](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-label)、[Selection by Position](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-integer)和[Advanced Indexing ，](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced)您可以使用布尔向量与其他索引表达式相结合沿多个轴进行选择。

```
In [169]: df2.loc[criterion & (df2['b'] == 'x'), 'b':'c']
Out[169]: 
   b         c
3  x  0.361719
```



警告

```
iloc`支持两种布尔索引。如果索引器是 boolean `Series`，则会引发错误。比如下面的例子，就可以了。布尔索引器是一个数组。但会提高。`df.iloc[s.values, 1]``df.iloc[s, 1]``ValueError
In [170]: df = pd.DataFrame([[1, 2], [3, 4], [5, 6]],
   .....:                   index=list('abc'),
   .....:                   columns=['A', 'B'])
   .....: 

In [171]: s = (df['A'] > 2)

In [172]: s
Out[172]: 
a    False
b     True
c     True
Name: A, dtype: bool

In [173]: df.loc[s, 'B']
Out[173]: 
b    4
c    6
Name: B, dtype: int64

In [174]: df.iloc[s.values, 1]
Out[174]: 
b    4
c    6
Name: B, dtype: int64
```





## 使用 isin 建立索引

考虑[`isin()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.isin.html#pandas.Series.isin)的方法`Series`，它返回一个布尔向量，只要元素`Series`存在于传递的列表中，该向量就为 true。这允许您选择其中一列或多列具有所需值的行：

```
In [175]: s = pd.Series(np.arange(5), index=np.arange(5)[::-1], dtype='int64')

In [176]: s
Out[176]: 
4    0
3    1
2    2
1    3
0    4
dtype: int64

In [177]: s.isin([2, 4, 6])
Out[177]: 
4    False
3    False
2     True
1    False
0     True
dtype: bool

In [178]: s[s.isin([2, 4, 6])]
Out[178]: 
2    2
0    4
dtype: int64
```



相同的方法可用于`Index`对象，并且对于您不知道哪些所寻找的标签实际上存在的情况非常有用：

```
In [179]: s[s.index.isin([2, 4, 6])]
Out[179]: 
4    0
2    2
dtype: int64

# compare it to the following
In [180]: s.reindex([2, 4, 6])
Out[180]: 
2    2.0
4    0.0
6    NaN
dtype: float64
```



除此之外，`MultiIndex`还允许选择在成员资格检查中使用的单独级别：

```
In [181]: s_mi = pd.Series(np.arange(6),
   .....:                  index=pd.MultiIndex.from_product([[0, 1], ['a', 'b', 'c']]))
   .....: 

In [182]: s_mi
Out[182]: 
0  a    0
   b    1
   c    2
1  a    3
   b    4
   c    5
dtype: int64

In [183]: s_mi.iloc[s_mi.index.isin([(1, 'a'), (2, 'b'), (0, 'c')])]
Out[183]: 
0  c    2
1  a    3
dtype: int64

In [184]: s_mi.iloc[s_mi.index.isin(['a', 'c', 'e'], level=1)]
Out[184]: 
0  a    0
   c    2
1  a    3
   c    5
dtype: int64
```



DataFrame也有一个[`isin()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.isin.html#pandas.DataFrame.isin)方法。调用时`isin`，以数组或字典的形式传递一组值。如果 value 是数组，`isin`则返回与原始 DataFrame 形状相同的布尔值 DataFrame，无论元素位于值序列中的任何位置，都返回 True。

```
In [185]: df = pd.DataFrame({'vals': [1, 2, 3, 4], 'ids': ['a', 'b', 'f', 'n'],
   .....:                    'ids2': ['a', 'n', 'c', 'n']})
   .....: 

In [186]: values = ['a', 'b', 1, 3]

In [187]: df.isin(values)
Out[187]: 
    vals    ids   ids2
0   True   True   True
1  False   True  False
2   True  False  False
3  False  False  False
```



通常，您需要将某些值与某些列进行匹配。只需将值设为 a，`dict`其中键是列，值是要检查的项目列表。

```
In [188]: values = {'ids': ['a', 'b'], 'vals': [1, 3]}

In [189]: df.isin(values)
Out[189]: 
    vals    ids   ids2
0   True   True  False
1  False   True  False
2   True  False  False
3  False  False  False
```



要返回布尔值的 DataFrame（其中值不在*原始*DataFrame 中），请使用`~`运算符：

```
In [190]: values = {'ids': ['a', 'b'], 'vals': [1, 3]}

In [191]: ~df.isin(values)
Out[191]: 
    vals    ids  ids2
0  False  False  True
1   True  False  True
2  False   True  True
3   True   True  True
```



将 DataFrame`isin`与`any()`和`all()`方法结合起来，可以快速选择满足给定条件的数据子集。要选择每列满足其自身标准的行：

```
In [192]: values = {'ids': ['a', 'b'], 'ids2': ['a', 'c'], 'vals': [1, 3]}

In [193]: row_mask = df.isin(values).all(1)

In [194]: df[row_mask]
Out[194]: 
   vals ids ids2
0     1   a    a
```





## 方法[`where()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.where.html#pandas.DataFrame.where)和掩蔽

使用布尔向量从系列中选择值通常会返回数据的子集。为了保证选择输出与原始数据具有相同的形状，可以使用和`where`中的方法。`Series``DataFrame`

仅返回选定的行：

```
In [195]: s[s > 0]
Out[195]: 
3    1
2    2
1    3
0    4
dtype: int64
```



要返回与原始形状相同的系列：

```
In [196]: s.where(s > 0)
Out[196]: 
4    NaN
3    1.0
2    2.0
1    3.0
0    4.0
dtype: float64
```



现在，使用布尔标准从 DataFrame 中选择值也可以保留输入数据的形状。`where`在幕后使用作为实现。下面的代码相当于.`df.where(df < 0)`

```
In [197]: dates = pd.date_range('1/1/2000', periods=8)

In [198]: df = pd.DataFrame(np.random.randn(8, 4),
   .....:                   index=dates, columns=['A', 'B', 'C', 'D'])
   .....: 

In [199]: df[df < 0]
Out[199]: 
                   A         B         C         D
2000-01-01 -2.104139 -1.309525       NaN       NaN
2000-01-02 -0.352480       NaN -1.192319       NaN
2000-01-03 -0.864883       NaN -0.227870       NaN
2000-01-04       NaN -1.222082       NaN -1.233203
2000-01-05       NaN -0.605656 -1.169184       NaN
2000-01-06       NaN -0.948458       NaN -0.684718
2000-01-07 -2.670153 -0.114722       NaN -0.048048
2000-01-08       NaN       NaN -0.048788 -0.808838
```



此外，在返回的副本中，`where`采用可选`other`参数来替换条件为 False 的值。

```
In [200]: df.where(df < 0, -df)
Out[200]: 
                   A         B         C         D
2000-01-01 -2.104139 -1.309525 -0.485855 -0.245166
2000-01-02 -0.352480 -0.390389 -1.192319 -1.655824
2000-01-03 -0.864883 -0.299674 -0.227870 -0.281059
2000-01-04 -0.846958 -1.222082 -0.600705 -1.233203
2000-01-05 -0.669692 -0.605656 -1.169184 -0.342416
2000-01-06 -0.868584 -0.948458 -2.297780 -0.684718
2000-01-07 -2.670153 -0.114722 -0.168904 -0.048048
2000-01-08 -0.801196 -1.392071 -0.048788 -0.808838
```



您可能希望根据某些布尔标准设置值。这可以直观地完成，如下所示：

```
In [201]: s2 = s.copy()

In [202]: s2[s2 < 0] = 0

In [203]: s2
Out[203]: 
4    0
3    1
2    2
1    3
0    4
dtype: int64

In [204]: df2 = df.copy()

In [205]: df2[df2 < 0] = 0

In [206]: df2
Out[206]: 
                   A         B         C         D
2000-01-01  0.000000  0.000000  0.485855  0.245166
2000-01-02  0.000000  0.390389  0.000000  1.655824
2000-01-03  0.000000  0.299674  0.000000  0.281059
2000-01-04  0.846958  0.000000  0.600705  0.000000
2000-01-05  0.669692  0.000000  0.000000  0.342416
2000-01-06  0.868584  0.000000  2.297780  0.000000
2000-01-07  0.000000  0.000000  0.168904  0.000000
2000-01-08  0.801196  1.392071  0.000000  0.000000
```



`where`返回数据的修改副本。

笔记

的签名[`DataFrame.where()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.where.html#pandas.DataFrame.where)不同于[`numpy.where()`](https://numpy.org/doc/stable/reference/generated/numpy.where.html#numpy.where)。大致相当于。`df1.where(m, df2)``np.where(m, df1, df2)`

```
In [207]: df.where(df < 0, -df) == np.where(df < 0, df, -df)
Out[207]: 
               A     B     C     D
2000-01-01  True  True  True  True
2000-01-02  True  True  True  True
2000-01-03  True  True  True  True
2000-01-04  True  True  True  True
2000-01-05  True  True  True  True
2000-01-06  True  True  True  True
2000-01-07  True  True  True  True
2000-01-08  True  True  True  True
```



**结盟**

此外，`where`对齐输入布尔条件（ndarray 或 DataFrame），以便可以通过设置进行部分选择。这类似于通过部分设置`.loc`（但在内容而不是轴标签上）。

```
In [208]: df2 = df.copy()

In [209]: df2[df2[1:4] > 0] = 3

In [210]: df2
Out[210]: 
                   A         B         C         D
2000-01-01 -2.104139 -1.309525  0.485855  0.245166
2000-01-02 -0.352480  3.000000 -1.192319  3.000000
2000-01-03 -0.864883  3.000000 -0.227870  3.000000
2000-01-04  3.000000 -1.222082  3.000000 -1.233203
2000-01-05  0.669692 -0.605656 -1.169184  0.342416
2000-01-06  0.868584 -0.948458  2.297780 -0.684718
2000-01-07 -2.670153 -0.114722  0.168904 -0.048048
2000-01-08  0.801196  1.392071 -0.048788 -0.808838
```



执行 时， Where 还可以接受`axis`和`level`参数来对齐输入`where`。

```
In [211]: df2 = df.copy()

In [212]: df2.where(df2 > 0, df2['A'], axis='index')
Out[212]: 
                   A         B         C         D
2000-01-01 -2.104139 -2.104139  0.485855  0.245166
2000-01-02 -0.352480  0.390389 -0.352480  1.655824
2000-01-03 -0.864883  0.299674 -0.864883  0.281059
2000-01-04  0.846958  0.846958  0.600705  0.846958
2000-01-05  0.669692  0.669692  0.669692  0.342416
2000-01-06  0.868584  0.868584  2.297780  0.868584
2000-01-07 -2.670153 -2.670153  0.168904 -2.670153
2000-01-08  0.801196  1.392071  0.801196  0.801196
```



这相当于（但比）以下内容更快。

```
In [213]: df2 = df.copy()

In [214]: df.apply(lambda x, y: x.where(x > 0, y), y=df['A'])
Out[214]: 
                   A         B         C         D
2000-01-01 -2.104139 -2.104139  0.485855  0.245166
2000-01-02 -0.352480  0.390389 -0.352480  1.655824
2000-01-03 -0.864883  0.299674 -0.864883  0.281059
2000-01-04  0.846958  0.846958  0.600705  0.846958
2000-01-05  0.669692  0.669692  0.669692  0.342416
2000-01-06  0.868584  0.868584  2.297780  0.868584
2000-01-07 -2.670153 -2.670153  0.168904 -2.670153
2000-01-08  0.801196  1.392071  0.801196  0.801196
```



`where`可以接受可调用作为条件和`other`参数。该函数必须带有一个参数（调用 Series 或 DataFrame），并且返回有效的输出作为条件和`other`参数。

```
In [215]: df3 = pd.DataFrame({'A': [1, 2, 3],
   .....:                     'B': [4, 5, 6],
   .....:                     'C': [7, 8, 9]})
   .....: 

In [216]: df3.where(lambda x: x > 4, lambda x: x + 10)
Out[216]: 
    A   B  C
0  11  14  7
1  12   5  8
2  13   6  9
```



### 面具

[`mask()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.mask.html#pandas.DataFrame.mask)是 的逆布尔运算`where`。

```
In [217]: s.mask(s >= 0)
Out[217]: 
4   NaN
3   NaN
2   NaN
1   NaN
0   NaN
dtype: float64

In [218]: df.mask(df >= 0)
Out[218]: 
                   A         B         C         D
2000-01-01 -2.104139 -1.309525       NaN       NaN
2000-01-02 -0.352480       NaN -1.192319       NaN
2000-01-03 -0.864883       NaN -0.227870       NaN
2000-01-04       NaN -1.222082       NaN -1.233203
2000-01-05       NaN -0.605656 -1.169184       NaN
2000-01-06       NaN -0.948458       NaN -0.684718
2000-01-07 -2.670153 -0.114722       NaN -0.048048
2000-01-08       NaN       NaN -0.048788 -0.808838
```





## 有条件地设置放大`numpy()`

的替代方法[`where()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.where.html#pandas.DataFrame.where)是使用[`numpy.where()`](https://numpy.org/doc/stable/reference/generated/numpy.where.html#numpy.where).与设置新列相结合，您可以使用它来扩大 DataFrame，其中的值是有条件确定的。

假设您在以下 DataFrame 中有两个选择可供选择。当第二列具有“Z”时，您希望将新列颜色设置为“绿色”。您可以执行以下操作：

```
In [219]: df = pd.DataFrame({'col1': list('ABBC'), 'col2': list('ZZXY')})

In [220]: df['color'] = np.where(df['col2'] == 'Z', 'green', 'red')

In [221]: df
Out[221]: 
  col1 col2  color
0    A    Z  green
1    B    Z  green
2    B    X    red
3    C    Y    red
```



如果您有多个条件，则可以使用[`numpy.select()`](https://numpy.org/doc/stable/reference/generated/numpy.select.html#numpy.select)它来实现。假设对应于三个条件有三种颜色选择，用第四种颜色作为后备，您可以执行以下操作。

```
In [222]: conditions = [
   .....:     (df['col2'] == 'Z') & (df['col1'] == 'A'),
   .....:     (df['col2'] == 'Z') & (df['col1'] == 'B'),
   .....:     (df['col1'] == 'B')
   .....: ]
   .....: 

In [223]: choices = ['yellow', 'blue', 'purple']

In [224]: df['color'] = np.select(conditions, choices, default='black')

In [225]: df
Out[225]: 
  col1 col2   color
0    A    Z  yellow
1    B    Z    blue
2    B    X  purple
3    C    Y   black
```





## 方法[`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)

[`DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame)对象有一个[`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query) 允许使用表达式进行选择的方法。

`b`您可以获得列的值介于 columns`a`和之间的框架的值`c`。例如：

```
In [226]: n = 10

In [227]: df = pd.DataFrame(np.random.rand(n, 3), columns=list('abc'))

In [228]: df
Out[228]: 
          a         b         c
0  0.438921  0.118680  0.863670
1  0.138138  0.577363  0.686602
2  0.595307  0.564592  0.520630
3  0.913052  0.926075  0.616184
4  0.078718  0.854477  0.898725
5  0.076404  0.523211  0.591538
6  0.792342  0.216974  0.564056
7  0.397890  0.454131  0.915716
8  0.074315  0.437913  0.019794
9  0.559209  0.502065  0.026437

# pure python
In [229]: df[(df['a'] < df['b']) & (df['b'] < df['c'])]
Out[229]: 
          a         b         c
1  0.138138  0.577363  0.686602
4  0.078718  0.854477  0.898725
5  0.076404  0.523211  0.591538
7  0.397890  0.454131  0.915716

# query
In [230]: df.query('(a < b) & (b < c)')
Out[230]: 
          a         b         c
1  0.138138  0.577363  0.686602
4  0.078718  0.854477  0.898725
5  0.076404  0.523211  0.591538
7  0.397890  0.454131  0.915716
```



执行相同的操作，但如果没有具有 name 的列，则使用命名索引`a`。

```
In [231]: df = pd.DataFrame(np.random.randint(n / 2, size=(n, 2)), columns=list('bc'))

In [232]: df.index.name = 'a'

In [233]: df
Out[233]: 
   b  c
a      
0  0  4
1  0  1
2  3  4
3  4  3
4  1  4
5  0  3
6  0  1
7  3  4
8  2  3
9  1  1

In [234]: df.query('a < b and b < c')
Out[234]: 
   b  c
a      
2  3  4
```



如果您不想或无法命名索引，则可以 `index`在查询表达式中使用该名称：

```
In [235]: df = pd.DataFrame(np.random.randint(n, size=(n, 2)), columns=list('bc'))

In [236]: df
Out[236]: 
   b  c
0  3  1
1  3  0
2  5  6
3  5  2
4  7  4
5  0  1
6  2  5
7  0  1
8  6  0
9  7  9

In [237]: df.query('index < b < c')
Out[237]: 
   b  c
2  5  6
```



笔记

如果索引名称与列名称重叠，则列名称优先。例如，

```
In [238]: df = pd.DataFrame({'a': np.random.randint(5, size=5)})

In [239]: df.index.name = 'a'

In [240]: df.query('a > 2')  # uses the column 'a', not the index
Out[240]: 
   a
a   
1  3
3  3
```



您仍然可以通过使用特殊标识符“index”在查询表达式中使用索引：

```
In [241]: df.query('index > 2')
Out[241]: 
   a
a   
3  3
4  2
```



如果由于某种原因您有一个名为 的列`index`，那么您也可以引用索引`ilevel_0`，但此时您应该考虑将列重命名为不太模糊的名称。

### [`MultiIndex`](https://pandas.pydata.org/docs/reference/api/pandas.MultiIndex.html#pandas.MultiIndex) [`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)句法

您还可以将 a 的级别`DataFrame`与 a 一起 使用[`MultiIndex`](https://pandas.pydata.org/docs/reference/api/pandas.MultiIndex.html#pandas.MultiIndex)，就好像它们是框架中的列一样：

```
In [242]: n = 10

In [243]: colors = np.random.choice(['red', 'green'], size=n)

In [244]: foods = np.random.choice(['eggs', 'ham'], size=n)

In [245]: colors
Out[245]: 
array(['red', 'red', 'red', 'green', 'green', 'green', 'green', 'green',
       'green', 'green'], dtype='<U5')

In [246]: foods
Out[246]: 
array(['ham', 'ham', 'eggs', 'eggs', 'eggs', 'ham', 'ham', 'eggs', 'eggs',
       'eggs'], dtype='<U4')

In [247]: index = pd.MultiIndex.from_arrays([colors, foods], names=['color', 'food'])

In [248]: df = pd.DataFrame(np.random.randn(n, 2), index=index)

In [249]: df
Out[249]: 
                   0         1
color food                    
red   ham   0.194889 -0.381994
      ham   0.318587  2.089075
      eggs -0.728293 -0.090255
green eggs -0.748199  1.318931
      eggs -2.029766  0.792652
      ham   0.461007 -0.542749
      ham  -0.305384 -0.479195
      eggs  0.095031 -0.270099
      eggs -0.707140 -0.773882
      eggs  0.229453  0.304418

In [250]: df.query('color == "red"')
Out[250]: 
                   0         1
color food                    
red   ham   0.194889 -0.381994
      ham   0.318587  2.089075
      eggs -0.728293 -0.090255
```



如果级别`MultiIndex`未命名，您可以使用特殊名称来引用它们：

```
In [251]: df.index.names = [None, None]

In [252]: df
Out[252]: 
                   0         1
red   ham   0.194889 -0.381994
      ham   0.318587  2.089075
      eggs -0.728293 -0.090255
green eggs -0.748199  1.318931
      eggs -2.029766  0.792652
      ham   0.461007 -0.542749
      ham  -0.305384 -0.479195
      eggs  0.095031 -0.270099
      eggs -0.707140 -0.773882
      eggs  0.229453  0.304418

In [253]: df.query('ilevel_0 == "red"')
Out[253]: 
                 0         1
red ham   0.194889 -0.381994
    ham   0.318587  2.089075
    eggs -0.728293 -0.090255
```



约定为`ilevel_0`，表示 的第 0 级为“索引级 0” `index`。

### [`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)用例

一个用例是当您拥有一组具有公共列名称（或索引级别/名称）子[`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)集的 对象时。[`DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame)您可以将相同的查询传递给两个框架，*而无需* 指定您有兴趣查询哪个框架

```
In [254]: df = pd.DataFrame(np.random.rand(n, 3), columns=list('abc'))

In [255]: df
Out[255]: 
          a         b         c
0  0.224283  0.736107  0.139168
1  0.302827  0.657803  0.713897
2  0.611185  0.136624  0.984960
3  0.195246  0.123436  0.627712
4  0.618673  0.371660  0.047902
5  0.480088  0.062993  0.185760
6  0.568018  0.483467  0.445289
7  0.309040  0.274580  0.587101
8  0.258993  0.477769  0.370255
9  0.550459  0.840870  0.304611

In [256]: df2 = pd.DataFrame(np.random.rand(n + 2, 3), columns=df.columns)

In [257]: df2
Out[257]: 
           a         b         c
0   0.357579  0.229800  0.596001
1   0.309059  0.957923  0.965663
2   0.123102  0.336914  0.318616
3   0.526506  0.323321  0.860813
4   0.518736  0.486514  0.384724
5   0.190804  0.505723  0.614533
6   0.891939  0.623977  0.676639
7   0.480559  0.378528  0.460858
8   0.420223  0.136404  0.141295
9   0.732206  0.419540  0.604675
10  0.604466  0.848974  0.896165
11  0.589168  0.920046  0.732716

In [258]: expr = '0.0 <= a <= c <= 0.5'

In [259]: map(lambda frame: frame.query(expr), [df, df2])
Out[259]: <map at 0x7fac68ab7580>
```



### [`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)Python 与 pandas 语法比较

完整的类似 numpy 的语法：

```
In [260]: df = pd.DataFrame(np.random.randint(n, size=(n, 3)), columns=list('abc'))

In [261]: df
Out[261]: 
   a  b  c
0  7  8  9
1  1  0  7
2  2  7  2
3  6  2  2
4  2  6  3
5  3  8  2
6  1  7  2
7  5  1  5
8  9  8  0
9  1  5  0

In [262]: df.query('(a < b) & (b < c)')
Out[262]: 
   a  b  c
0  7  8  9

In [263]: df[(df['a'] < df['b']) & (df['b'] < df['c'])]
Out[263]: 
   a  b  c
0  7  8  9
```



去掉括号会好一点（比较运算符比`&`and绑定得更紧`|`）：

```
In [264]: df.query('a < b & b < c')
Out[264]: 
   a  b  c
0  7  8  9
```



使用英文代替符号：

```
In [265]: df.query('a < b and b < c')
Out[265]: 
   a  b  c
0  7  8  9
```



非常接近你在纸上写的方式：

```
In [266]: df.query('a < b < c')
Out[266]: 
   a  b  c
0  7  8  9
```



### 和运算`in`符`not in`

[`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)还支持 Python`in`和 比较运算符的特殊使用，为调用 a或的方法提供简洁的语法。`not in``isin``Series``DataFrame`

```
# get all rows where columns "a" and "b" have overlapping values
In [267]: df = pd.DataFrame({'a': list('aabbccddeeff'), 'b': list('aaaabbbbcccc'),
   .....:                    'c': np.random.randint(5, size=12),
   .....:                    'd': np.random.randint(9, size=12)})
   .....: 

In [268]: df
Out[268]: 
    a  b  c  d
0   a  a  2  6
1   a  a  4  7
2   b  a  1  6
3   b  a  2  1
4   c  b  3  6
5   c  b  0  2
6   d  b  3  3
7   d  b  2  1
8   e  c  4  3
9   e  c  2  0
10  f  c  0  6
11  f  c  1  2

In [269]: df.query('a in b')
Out[269]: 
   a  b  c  d
0  a  a  2  6
1  a  a  4  7
2  b  a  1  6
3  b  a  2  1
4  c  b  3  6
5  c  b  0  2

# How you'd do it in pure Python
In [270]: df[df['a'].isin(df['b'])]
Out[270]: 
   a  b  c  d
0  a  a  2  6
1  a  a  4  7
2  b  a  1  6
3  b  a  2  1
4  c  b  3  6
5  c  b  0  2

In [271]: df.query('a not in b')
Out[271]: 
    a  b  c  d
6   d  b  3  3
7   d  b  2  1
8   e  c  4  3
9   e  c  2  0
10  f  c  0  6
11  f  c  1  2

# pure Python
In [272]: df[~df['a'].isin(df['b'])]
Out[272]: 
    a  b  c  d
6   d  b  3  3
7   d  b  2  1
8   e  c  4  3
9   e  c  2  0
10  f  c  0  6
11  f  c  1  2
```



您可以将其与其他表达式结合起来进行非常简洁的查询：

```
# rows where cols a and b have overlapping values
# and col c's values are less than col d's
In [273]: df.query('a in b and c < d')
Out[273]: 
   a  b  c  d
0  a  a  2  6
1  a  a  4  7
2  b  a  1  6
4  c  b  3  6
5  c  b  0  2

# pure Python
In [274]: df[df['b'].isin(df['a']) & (df['c'] < df['d'])]
Out[274]: 
    a  b  c  d
0   a  a  2  6
1   a  a  4  7
2   b  a  1  6
4   c  b  3  6
5   c  b  0  2
10  f  c  0  6
11  f  c  1  2
```



笔记

请注意，`in`和在 Python 中进行计算，因为 没有与此操作等效的操作。但是，在普通 Python 中，**仅**计算/**表达式本身**。例如，在表达式中`not in``numexpr` `in``not in`

```
df.query('a in b + c + d')
```



`(b + c + d)`由 求值，`numexpr`然后*用*`in` 普通 Python 求值。一般来说，任何可以使用`numexpr`will 进行评估的操作都是如此。

### `==`对象`list`运算符的特殊使用

使用/将一`list`组值与一列进行比较的方式与/类似。`==``!=``in``not in`

```
In [275]: df.query('b == ["a", "b", "c"]')
Out[275]: 
    a  b  c  d
0   a  a  2  6
1   a  a  4  7
2   b  a  1  6
3   b  a  2  1
4   c  b  3  6
5   c  b  0  2
6   d  b  3  3
7   d  b  2  1
8   e  c  4  3
9   e  c  2  0
10  f  c  0  6
11  f  c  1  2

# pure Python
In [276]: df[df['b'].isin(["a", "b", "c"])]
Out[276]: 
    a  b  c  d
0   a  a  2  6
1   a  a  4  7
2   b  a  1  6
3   b  a  2  1
4   c  b  3  6
5   c  b  0  2
6   d  b  3  3
7   d  b  2  1
8   e  c  4  3
9   e  c  2  0
10  f  c  0  6
11  f  c  1  2

In [277]: df.query('c == [1, 2]')
Out[277]: 
    a  b  c  d
0   a  a  2  6
2   b  a  1  6
3   b  a  2  1
7   d  b  2  1
9   e  c  2  0
11  f  c  1  2

In [278]: df.query('c != [1, 2]')
Out[278]: 
    a  b  c  d
1   a  a  4  7
4   c  b  3  6
5   c  b  0  2
6   d  b  3  3
8   e  c  4  3
10  f  c  0  6

# using in/not in
In [279]: df.query('[1, 2] in c')
Out[279]: 
    a  b  c  d
0   a  a  2  6
2   b  a  1  6
3   b  a  2  1
7   d  b  2  1
9   e  c  2  0
11  f  c  1  2

In [280]: df.query('[1, 2] not in c')
Out[280]: 
    a  b  c  d
1   a  a  4  7
4   c  b  3  6
5   c  b  0  2
6   d  b  3  3
8   e  c  4  3
10  f  c  0  6

# pure Python
In [281]: df[df['c'].isin([1, 2])]
Out[281]: 
    a  b  c  d
0   a  a  2  6
2   b  a  1  6
3   b  a  2  1
7   d  b  2  1
9   e  c  2  0
11  f  c  1  2
```



### 布尔运算符

您可以使用单词`not`或`~`运算符来否定布尔表达式。

```
In [282]: df = pd.DataFrame(np.random.rand(n, 3), columns=list('abc'))

In [283]: df['bools'] = np.random.rand(len(df)) > 0.5

In [284]: df.query('~bools')
Out[284]: 
          a         b         c  bools
2  0.697753  0.212799  0.329209  False
7  0.275396  0.691034  0.826619  False
8  0.190649  0.558748  0.262467  False

In [285]: df.query('not bools')
Out[285]: 
          a         b         c  bools
2  0.697753  0.212799  0.329209  False
7  0.275396  0.691034  0.826619  False
8  0.190649  0.558748  0.262467  False

In [286]: df.query('not bools') == df[~df['bools']]
Out[286]: 
      a     b     c  bools
2  True  True  True   True
7  True  True  True   True
8  True  True  True   True
```



当然，表达式也可以任意复杂：

```
# short query syntax
In [287]: shorter = df.query('a < b < c and (not bools) or bools > 2')

# equivalent in pure Python
In [288]: longer = df[(df['a'] < df['b'])
   .....:             & (df['b'] < df['c'])
   .....:             & (~df['bools'])
   .....:             | (df['bools'] > 2)]
   .....: 

In [289]: shorter
Out[289]: 
          a         b         c  bools
7  0.275396  0.691034  0.826619  False

In [290]: longer
Out[290]: 
          a         b         c  bools
7  0.275396  0.691034  0.826619  False

In [291]: shorter == longer
Out[291]: 
      a     b     c  bools
7  True  True  True   True
```



### 的表演[`query()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.query.html#pandas.DataFrame.query)

`DataFrame.query()``numexpr`对于大型框架，使用速度比 Python 稍快。

![../_images/query-perf.png](https://pandas.pydata.org/docs/_images/query-perf.png)

仅当您的框架具有超过大约 100,000 行时，您才会看到使用该`numexpr`引擎的性能优势。`DataFrame.query()`

该图是使用`DataFrame`3 列创建的，每列包含使用 生成的浮点值`numpy.random.randn()`。

```
In [292]: df = pd.DataFrame(np.random.randn(8, 4),
   .....:                   index=dates, columns=['A', 'B', 'C', 'D'])
   .....: 

In [293]: df2 = df.copy()
```



## 重复数据

如果您想识别并删除 DataFrame 中的重复行，有两种方法可以提供帮助：`duplicated`和`drop_duplicates`。每个都将用于识别重复行的列作为参数。

- `duplicated`返回一个布尔向量，其长度为行数，指示行是否重复。
- `drop_duplicates`删除重复的行。

默认情况下，重复集的第一个观察到的行被认为是唯一的，但每个方法都有一个`keep`参数来指定要保留的目标。

- `keep='first'`（默认）：标记/删除除第一次出现之外的重复项。
- `keep='last'`：标记/删除除最后一次出现之外的重复项。
- `keep=False`：标记/删除所有重复项。

```
In [294]: df2 = pd.DataFrame({'a': ['one', 'one', 'two', 'two', 'two', 'three', 'four'],
   .....:                     'b': ['x', 'y', 'x', 'y', 'x', 'x', 'x'],
   .....:                     'c': np.random.randn(7)})
   .....: 

In [295]: df2
Out[295]: 
       a  b         c
0    one  x -1.067137
1    one  y  0.309500
2    two  x -0.211056
3    two  y -1.842023
4    two  x -0.390820
5  three  x -1.964475
6   four  x  1.298329

In [296]: df2.duplicated('a')
Out[296]: 
0    False
1     True
2    False
3     True
4     True
5    False
6    False
dtype: bool

In [297]: df2.duplicated('a', keep='last')
Out[297]: 
0     True
1    False
2     True
3     True
4    False
5    False
6    False
dtype: bool

In [298]: df2.duplicated('a', keep=False)
Out[298]: 
0     True
1     True
2     True
3     True
4     True
5    False
6    False
dtype: bool

In [299]: df2.drop_duplicates('a')
Out[299]: 
       a  b         c
0    one  x -1.067137
2    two  x -0.211056
5  three  x -1.964475
6   four  x  1.298329

In [300]: df2.drop_duplicates('a', keep='last')
Out[300]: 
       a  b         c
1    one  y  0.309500
4    two  x -0.390820
5  three  x -1.964475
6   four  x  1.298329

In [301]: df2.drop_duplicates('a', keep=False)
Out[301]: 
       a  b         c
5  three  x -1.964475
6   four  x  1.298329
```



此外，您还可以传递列列表来识别重复项。

```
In [302]: df2.duplicated(['a', 'b'])
Out[302]: 
0    False
1    False
2    False
3    False
4     True
5    False
6    False
dtype: bool

In [303]: df2.drop_duplicates(['a', 'b'])
Out[303]: 
       a  b         c
0    one  x -1.067137
1    one  y  0.309500
2    two  x -0.211056
3    two  y -1.842023
5  three  x -1.964475
6   four  x  1.298329
```



要按索引值删除重复项，请使用`Index.duplicated`then 执行切片。同一组选项可用于该`keep`参数。

```
In [304]: df3 = pd.DataFrame({'a': np.arange(6),
   .....:                     'b': np.random.randn(6)},
   .....:                    index=['a', 'a', 'b', 'c', 'b', 'a'])
   .....: 

In [305]: df3
Out[305]: 
   a         b
a  0  1.440455
a  1  2.456086
b  2  1.038402
c  3 -0.894409
b  4  0.683536
a  5  3.082764

In [306]: df3.index.duplicated()
Out[306]: array([False,  True, False, False,  True,  True])

In [307]: df3[~df3.index.duplicated()]
Out[307]: 
   a         b
a  0  1.440455
b  2  1.038402
c  3 -0.894409

In [308]: df3[~df3.index.duplicated(keep='last')]
Out[308]: 
   a         b
c  3 -0.894409
b  4  0.683536
a  5  3.082764

In [309]: df3[~df3.index.duplicated(keep=False)]
Out[309]: 
   a         b
c  3 -0.894409
```





## 类似字典的[`get()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.get.html#pandas.DataFrame.get)方法

每个 Series 或 DataFrame 都有一个`get`可以返回默认值的方法。

```
In [310]: s = pd.Series([1, 2, 3], index=['a', 'b', 'c'])

In [311]: s.get('a')  # equivalent to s['a']
Out[311]: 1

In [312]: s.get('x', default=-1)
Out[312]: -1
```





## 通过索引/列标签查找值

有时，您想要在给定行标签和列标签序列的情况下提取一组值，这可以通过`pandas.factorize`NumPy 索引来实现。例如：

```
In [313]: df = pd.DataFrame({'col': ["A", "A", "B", "B"],
   .....:                    'A': [80, 23, np.nan, 22],
   .....:                    'B': [80, 55, 76, 67]})
   .....: 

In [314]: df
Out[314]: 
  col     A   B
0   A  80.0  80
1   A  23.0  55
2   B   NaN  76
3   B  22.0  67

In [315]: idx, cols = pd.factorize(df['col'])

In [316]: df.reindex(cols, axis=1).to_numpy()[np.arange(len(df)), idx]
Out[316]: array([80., 23., 76., 67.])
```



以前，这可以通过专用方法来实现`DataFrame.lookup`，该方法在版本 1.2.0 中已弃用并在版本 2.0.0 中删除。



## 索引对象

pandas[`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)类及其子类可以被视为实现了*有序多重集*。允许重复。

[`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)还提供查找、数据对齐和重新索引所需的基础设施。直接创建 的最简单方法 [`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)是将一个`list`或其他序列传递给 [`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)：

```
In [317]: index = pd.Index(['e', 'd', 'a', 'b'])

In [318]: index
Out[318]: Index(['e', 'd', 'a', 'b'], dtype='object')

In [319]: 'd' in index
Out[319]: True
```



或使用数字：

```
In [320]: index = pd.Index([1, 5, 12])

In [321]: index
Out[321]: Index([1, 5, 12], dtype='int64')

In [322]: 5 in index
Out[322]: True
```



如果未给出 dtype，`Index`则尝试从数据推断 dtype。实例化时也可以给出显式的数据类型[`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)：

```
In [323]: index = pd.Index(['e', 'd', 'a', 'b'], dtype="string")

In [324]: index
Out[324]: Index(['e', 'd', 'a', 'b'], dtype='string')

In [325]: index = pd.Index([1, 5, 12], dtype="int8")

In [326]: index
Out[326]: Index([1, 5, 12], dtype='int8')

In [327]: index = pd.Index([1, 5, 12], dtype="float32")

In [328]: index
Out[328]: Index([1.0, 5.0, 12.0], dtype='float32')
```



您还可以传递 a`name`来存储在索引中：

```
In [329]: index = pd.Index(['e', 'd', 'a', 'b'], name='something')

In [330]: index.name
Out[330]: 'something'
```



该名称（如果设置）将显示在控制台显示屏中：

```
In [331]: index = pd.Index(list(range(5)), name='rows')

In [332]: columns = pd.Index(['A', 'B', 'C'], name='cols')

In [333]: df = pd.DataFrame(np.random.randn(5, 3), index=index, columns=columns)

In [334]: df
Out[334]: 
cols         A         B         C
rows                              
0     1.295989 -1.051694  1.340429
1    -2.366110  0.428241  0.387275
2     0.433306  0.929548  0.278094
3     2.154730 -0.315628  0.264223
4     1.126818  1.132290 -0.353310

In [335]: df['A']
Out[335]: 
rows
0    1.295989
1   -2.366110
2    0.433306
3    2.154730
4    1.126818
Name: A, dtype: float64
```





### 设置元数据

索引“大部分是不可变的”，但可以设置和更改它们的 `name`属性。您可以使用`rename`,`set_names`直接设置这些属性，它们默认返回副本。

有关多重索引的使用，请参阅[高级索引。](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced)

```
In [336]: ind = pd.Index([1, 2, 3])

In [337]: ind.rename("apple")
Out[337]: Index([1, 2, 3], dtype='int64', name='apple')

In [338]: ind
Out[338]: Index([1, 2, 3], dtype='int64')

In [339]: ind = ind.set_names(["apple"])

In [340]: ind.name = "bob"

In [341]: ind
Out[341]: Index([1, 2, 3], dtype='int64', name='bob')
```



`set_names`, `set_levels`, 并且`set_codes`还采用可选 `level`参数

```
In [342]: index = pd.MultiIndex.from_product([range(3), ['one', 'two']], names=['first', 'second'])

In [343]: index
Out[343]: 
MultiIndex([(0, 'one'),
            (0, 'two'),
            (1, 'one'),
            (1, 'two'),
            (2, 'one'),
            (2, 'two')],
           names=['first', 'second'])

In [344]: index.levels[1]
Out[344]: Index(['one', 'two'], dtype='object', name='second')

In [345]: index.set_levels(["a", "b"], level=1)
Out[345]: 
MultiIndex([(0, 'a'),
            (0, 'b'),
            (1, 'a'),
            (1, 'b'),
            (2, 'a'),
            (2, 'b')],
           names=['first', 'second'])
```





### 对 Index 对象进行设置操作

两个主要操作是`union`和`intersection`。差异是通过该`.difference()`方法提供的。

```
In [346]: a = pd.Index(['c', 'b', 'a'])

In [347]: b = pd.Index(['c', 'e', 'd'])

In [348]: a.difference(b)
Out[348]: Index(['a', 'b'], dtype='object')
```



还可以使用该`symmetric_difference`操作，该操作返回出现在 或 中的元素`idx1`，`idx2`但不会返回出现在两者中的元素。这相当于由`idx1.difference(idx2).union(idx2.difference(idx1))`删除重复项创建的索引。

```
In [349]: idx1 = pd.Index([1, 2, 3, 4])

In [350]: idx2 = pd.Index([2, 3, 4, 5])

In [351]: idx1.symmetric_difference(idx2)
Out[351]: Index([1, 5], dtype='int64')
```



笔记

集合操作的结果索引将按升序排序。

在具有不同数据类型的索引之间执行时[`Index.union()`](https://pandas.pydata.org/docs/reference/api/pandas.Index.union.html#pandas.Index.union)，必须将索引转换为通用数据类型。通常（但并非总是如此），这是对象数据类型。例外情况是在整数和浮点数据之间执行并集时。在这种情况下，整数值将转换为浮点数

```
In [352]: idx1 = pd.Index([0, 1, 2])

In [353]: idx2 = pd.Index([0.5, 1.5])

In [354]: idx1.union(idx2)
Out[354]: Index([0.0, 0.5, 1.0, 1.5, 2.0], dtype='float64')
```





### 缺失值

重要的

尽管`Index`可以保存缺失值 ( `NaN`)，但如果您不希望出现任何意外结果，则应避免使用它。例如，某些操作隐式排除缺失值。

`Index.fillna`用指定的标量值填充缺失值。

```
In [355]: idx1 = pd.Index([1, np.nan, 3, 4])

In [356]: idx1
Out[356]: Index([1.0, nan, 3.0, 4.0], dtype='float64')

In [357]: idx1.fillna(2)
Out[357]: Index([1.0, 2.0, 3.0, 4.0], dtype='float64')

In [358]: idx2 = pd.DatetimeIndex([pd.Timestamp('2011-01-01'),
   .....:                          pd.NaT,
   .....:                          pd.Timestamp('2011-01-03')])
   .....: 

In [359]: idx2
Out[359]: DatetimeIndex(['2011-01-01', 'NaT', '2011-01-03'], dtype='datetime64[ns]', freq=None)

In [360]: idx2.fillna(pd.Timestamp('2011-01-02'))
Out[360]: DatetimeIndex(['2011-01-01', '2011-01-02', '2011-01-03'], dtype='datetime64[ns]', freq=None)
```



## 设置/重置索引

有时，您会将数据集加载或创建到 DataFrame 中，并希望在完成此操作后添加索引。有几种不同的方法。



### 设置索引

DataFrame 有一个[`set_index()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.set_index.html#pandas.DataFrame.set_index)方法，它采用列名（对于常规`Index`）或列名列表（对于 a `MultiIndex`）。要创建一个新的、重新索引的 DataFrame：

```
In [361]: data = pd.DataFrame({'a': ['bar', 'bar', 'foo', 'foo'],
   .....:                      'b': ['one', 'two', 'one', 'two'],
   .....:                      'c': ['z', 'y', 'x', 'w'],
   .....:                      'd': [1., 2., 3, 4]})
   .....: 

In [362]: data
Out[362]: 
     a    b  c    d
0  bar  one  z  1.0
1  bar  two  y  2.0
2  foo  one  x  3.0
3  foo  two  w  4.0

In [363]: indexed1 = data.set_index('c')

In [364]: indexed1
Out[364]: 
     a    b    d
c               
z  bar  one  1.0
y  bar  two  2.0
x  foo  one  3.0
w  foo  two  4.0

In [365]: indexed2 = data.set_index(['a', 'b'])

In [366]: indexed2
Out[366]: 
         c    d
a   b          
bar one  z  1.0
    two  y  2.0
foo one  x  3.0
    two  w  4.0
```



关键字`append`选项允许您保留现有索引并将给定列附加到 MultiIndex：

```
In [367]: frame = data.set_index('c', drop=False)

In [368]: frame = frame.set_index(['a', 'b'], append=True)

In [369]: frame
Out[369]: 
           c    d
c a   b          
z bar one  z  1.0
y bar two  y  2.0
x foo one  x  3.0
w foo two  w  4.0
```



其他选项`set_index`允许您不删除索引列。

```
In [370]: data.set_index('c', drop=False)
Out[370]: 
     a    b  c    d
c                  
z  bar  one  z  1.0
y  bar  two  y  2.0
x  foo  one  x  3.0
w  foo  two  w  4.0
```



### 重置索引

为了方便起见，DataFrame 上有一个名为 的新函数 [`reset_index()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reset_index.html#pandas.DataFrame.reset_index)，它将索引值传输到 DataFrame 的列中并设置一个简单的整数索引。这是 的逆运算[`set_index()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.set_index.html#pandas.DataFrame.set_index)。

```
In [371]: data
Out[371]: 
     a    b  c    d
0  bar  one  z  1.0
1  bar  two  y  2.0
2  foo  one  x  3.0
3  foo  two  w  4.0

In [372]: data.reset_index()
Out[372]: 
   index    a    b  c    d
0      0  bar  one  z  1.0
1      1  bar  two  y  2.0
2      2  foo  one  x  3.0
3      3  foo  two  w  4.0
```



输出更类似于 SQL 表或记录数组。从索引派生的列的名称是存储在`names`属性中的名称。

您可以使用`level`关键字仅删除索引的一部分：

```
In [373]: frame
Out[373]: 
           c    d
c a   b          
z bar one  z  1.0
y bar two  y  2.0
x foo one  x  3.0
w foo two  w  4.0

In [374]: frame.reset_index(level=1)
Out[374]: 
         a  c    d
c b               
z one  bar  z  1.0
y two  bar  y  2.0
x one  foo  x  3.0
w two  foo  w  4.0
```



`reset_index`采用一个可选参数`drop`，如果为 true，则简单地丢弃索引，而不是将索引值放入 DataFrame 的列中。

### 添加临时索引

您可以为属性分配自定义索引`index`：

```
In [375]: df_idx = pd.DataFrame(range(4))

In [376]: df_idx.index = pd.Index([10, 20, 30, 40], name="a")

In [377]: df_idx
Out[377]: 
    0
a    
10  0
20  1
30  2
40  3
```





## 返回视图与副本

警告

[Copy-on-Write](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write) 将成为 pandas 3.0 中新的默认设置。这意味着链式索引永远不会起作用。结果，`SettingWithCopyWarning`不再需要了。请参阅[本节](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write-chained-assignment) 了解更多背景信息。我们建议打开 Copy-on-Write 以利用以下方面的改进

```
 pd.options.mode.copy_on_write = True 
```

甚至在 pandas 3.0 可用之前。

在 pandas 对象中设置值时，必须小心避免所谓的 .这是一个例子。`chained indexing`

```
In [378]: dfmi = pd.DataFrame([list('abcd'),
   .....:                      list('efgh'),
   .....:                      list('ijkl'),
   .....:                      list('mnop')],
   .....:                     columns=pd.MultiIndex.from_product([['one', 'two'],
   .....:                                                         ['first', 'second']]))
   .....: 

In [379]: dfmi
Out[379]: 
    one          two       
  first second first second
0     a      b     c      d
1     e      f     g      h
2     i      j     k      l
3     m      n     o      p
```



比较这两种访问方式：

```
In [380]: dfmi['one']['second']
Out[380]: 
0    b
1    f
2    j
3    n
Name: second, dtype: object
```



```
In [381]: dfmi.loc[:, ('one', 'second')]
Out[381]: 
0    b
1    f
2    j
3    n
Name: (one, second), dtype: object
```



这两者都会产生相同的结果，那么您应该使用哪一个呢？了解这些操作的顺序以及为什么方法 2 ( ) 比方法 1（链式）`.loc`更受欢迎是有启发性的。`[]`

`dfmi['one']`选择第一级列并返回单索引的 DataFrame。然后另一个 Python 操作`dfmi_with_one['second']`选择由 索引的系列`'second'`。这是由变量指示的，`dfmi_with_one`因为 pandas 将这些操作视为单独的事件。例如，对 的单独调用`__getitem__`，因此必须将它们视为线性操作，它们接连发生。

将此与`df.loc[:,('one','second')]`将嵌套元组传递`(slice(None),('one','second'))`给单个调用 进行对比`__getitem__`。这使得 pandas 能够将其作为一个实体来处理。此外，这种操作顺序*可以*明显更快，并且如果需要的话可以对*两个轴进行索引。*

### 为什么使用链式索引时赋值会失败？

警告

[Copy-on-Write](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write) 将成为 pandas 3.0 中新的默认设置。这意味着链式索引永远不会起作用。结果，`SettingWithCopyWarning`不再需要了。请参阅[本节](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write-chained-assignment) 了解更多背景信息。我们建议打开 Copy-on-Write 以利用以下方面的改进

```
 pd.options.mode.copy_on_write = True 
```

甚至在 pandas 3.0 可用之前。

上一节的问题只是性能问题。出现`SettingWithCopy`警告是怎么回事？当您执行可能花费额外几毫秒的操作时，我们**通常**不会发出警告！

但事实证明，分配给链式索引的产品本质上会产生不可预测的结果。要看到这一点，请考虑 Python 解释器如何执行此代码：

```
dfmi.loc[:, ('one', 'second')] = value
# becomes
dfmi.loc.__setitem__((slice(None), ('one', 'second')), value)
```



但这段代码的处理方式有所不同：

```
dfmi['one']['second'] = value
# becomes
dfmi.__getitem__('one').__setitem__('second', value)
```



看到`__getitem__`那里了吗？除了简单的情况之外，很难预测它是否会返回视图或副本（这取决于数组的内存布局，pandas 对此不做保证），因此很难预测它是否`__setitem__`会修改`dfmi`或获取的临时对象之后立即被扔出去。**这就是**警告`SettingWithCopy`你的！

笔记

您可能想知道我们是否应该关注`loc` 第一个示例中的属性。但`dfmi.loc`保证`dfmi` 其本身具有修改后的索引行为，因此`dfmi.loc.__getitem__`/ 可以直接`dfmi.loc.__setitem__`进行操作。`dfmi`当然， `dfmi.loc.__getitem__(idx)`可能是视图或副本`dfmi`。

`SettingWithCopy`有时，当没有明显的链式索引进行时，有时会出现警告。**这些**`SettingWithCopy`是旨在捕获的错误 ！ pandas 可能试图警告您已经这样做了：

```
def do_something(df):
    foo = df[['bar', 'baz']]  # Is foo a view? A copy? Nobody knows!
    # ... many lines here ...
    # We don't know whether this will modify df or not!
    foo['quux'] = value
    return foo
```



哎呀！



### 评估顺序很重要

警告

[Copy-on-Write](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write) 将成为 pandas 3.0 中新的默认设置。这意味着链式索引永远不会起作用。结果，`SettingWithCopyWarning`不再需要了。请参阅[本节](https://pandas.pydata.org/docs/user_guide/copy_on_write.html#copy-on-write-chained-assignment) 了解更多背景信息。我们建议打开 Copy-on-Write 以利用以下方面的改进

```
 pd.options.mode.copy_on_write = True 
```

甚至在 pandas 3.0 可用之前。

使用链式索引时，索引操作的顺序和类型部分决定结果是原始对象的切片还是切片的副本。

pandas 之所以存在这种情况，是`SettingWithCopyWarning`因为分配给切片的副本通常不是有意的，而是由链式索引返回预期切片的副本引起的错误。

如果您希望 pandas 或多或少信任链式索引表达式的分配，您可以将该[选项](https://pandas.pydata.org/docs/user_guide/options.html#options) `mode.chained_assignment`设置为以下值之一：

- `'warn'`，默认值，表示`SettingWithCopyWarning`打印 a。
- `'raise'`意味着熊猫会养育一个`SettingWithCopyError` 你必须处理的动物。
- `None`将完全抑制警告。

```
In [382]: dfb = pd.DataFrame({'a': ['one', 'one', 'two',
   .....:                           'three', 'two', 'one', 'six'],
   .....:                     'c': np.arange(7)})
   .....: 

# This will show the SettingWithCopyWarning
# but the frame values will be set
In [383]: dfb['c'][dfb['a'].str.startswith('o')] = 42
```



然而，这在副本上运行并且不起作用。

```
In [384]: with pd.option_context('mode.chained_assignment','warn'):
   .....:     dfb[dfb['a'].str.startswith('o')]['c'] = 42
   .....: 
```



链式分配也可能出现在混合数据类型框架的设置中。

笔记

这些设置规则适用于所有`.loc/.iloc`.

以下是`.loc`针对多个项目（使用`mask`）和使用固定索引的单个项目的推荐访问方法：

```
In [385]: dfc = pd.DataFrame({'a': ['one', 'one', 'two',
   .....:                           'three', 'two', 'one', 'six'],
   .....:                     'c': np.arange(7)})
   .....: 

In [386]: dfd = dfc.copy()

# Setting multiple items using a mask
In [387]: mask = dfd['a'].str.startswith('o')

In [388]: dfd.loc[mask, 'c'] = 42

In [389]: dfd
Out[389]: 
       a   c
0    one  42
1    one  42
2    two   2
3  three   3
4    two   4
5    one  42
6    six   6

# Setting a single item
In [390]: dfd = dfc.copy()

In [391]: dfd.loc[2, 'a'] = 11

In [392]: dfd
Out[392]: 
       a  c
0    one  0
1    one  1
2     11  2
3  three  3
4    two  4
5    one  5
6    six  6
```



以下内容有时*可能*有效，但不能保证有效，因此应避免：

```
In [393]: dfd = dfc.copy()

In [394]: dfd['a'][2] = 111

In [395]: dfd
Out[395]: 
       a  c
0    one  0
1    one  1
2    111  2
3  three  3
4    two  4
5    one  5
6    six  6
```



最后，后面的例子根本**不起作用**，所以应该避免：

```
In [396]: with pd.option_context('mode.chained_assignment','raise'):
   .....:     dfd.loc[0]['a'] = 1111
   .....: 
---------------------------------------------------------------------------
SettingWithCopyError                      Traceback (most recent call last)
<ipython-input-396-32ce785aaa5b> in ?()
      1 with pd.option_context('mode.chained_assignment','raise'):
----> 2     dfd.loc[0]['a'] = 1111

~/work/pandas/pandas/pandas/core/series.py in ?(self, key, value)
   1275                 )
   1276 
   1277         check_dict_or_set_indexers(key)
   1278         key = com.apply_if_callable(key, self)
-> 1279         cacher_needs_updating = self._check_is_chained_assignment_possible()
   1280 
   1281         if key is Ellipsis:
   1282             key = slice(None)

~/work/pandas/pandas/pandas/core/series.py in ?(self)
   1480             ref = self._get_cacher()
   1481             if ref is not None and ref._is_mixed_type:
   1482                 self._check_setitem_copy(t="referent", force=True)
   1483             return True
-> 1484         return super()._check_is_chained_assignment_possible()

~/work/pandas/pandas/pandas/core/generic.py in ?(self)
   4392         single-dtype meaning that the cacher should be updated following
   4393         setting.
   4394         """
   4395         if self._is_copy:
-> 4396             self._check_setitem_copy(t="referent")
   4397         return False

~/work/pandas/pandas/pandas/core/generic.py in ?(self, t, force)
   4466                 "indexing.html#returning-a-view-versus-a-copy"
   4467             )
   4468 
   4469         if value == "raise":
-> 4470             raise SettingWithCopyError(t)
   4471         if value == "warn":
   4472             warnings.warn(t, SettingWithCopyWarning, stacklevel=find_stack_level())

SettingWithCopyError: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
```



警告

链接分配警告/异常旨在通知用户可能无效的分配。可能存在误报；无意中报告链式分配的情况。