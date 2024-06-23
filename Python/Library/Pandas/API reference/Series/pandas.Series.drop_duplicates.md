# pandas.Series.drop_duplicates

**Series.drop_duplicates**(\*, **keep**='first', **inplace**=False, **ignore_index**=False)
返回已删除重复值的系列。

- **Parameters:**
  **keep : {'first', 'last', `False`}, default 'first'**
  处理删除重复项的方法：
  - 'first' : 删除除第一次出现之外的重复项。
  - 'last' : 删除除第一次出现之外的重复项。
  - `False` : 删除除第一次出现之外的重复项。
- **inplace : bool, default `False`**

  如果`True`，则就地执行操作并返回 None。

- **ignore_indexbool, default False**
  如果`True`，则生成的轴将标记为 0, 1, …, n - 1。

- \***\*Returns\*\***:
  Series or None
  Series with duplicates dropped or None if inplace=True.

- **Examples**

生成具有重复条目的系列。

```
>>> s = pd.Series(['llama', 'cow', 'llama', 'beetle', 'llama', 'hippo'],
...               name='animal')
>>> s
0     llama
1       cow
2     llama
3    beetle
4     llama
5     hippo
Name: animal, dtype: object
```

使用“keep”参数，可以更改重复值的选择行为。值“first”保留每组重复条目的第一次出现。 keep 的默认值是“first”。

```
>>> s.drop_duplicates()
0     llama
1       cow
3    beetle
5     hippo
Name: animal, dtype: object
```

参数“keep”的值“last”保留每组重复条目的最后一次出现。

```
>>> s.drop_duplicates(keep='last')
1       cow
3    beetle
4     llama
5     hippo
Name: animal, dtype: object
```

参数“keep”的值`False`会丢弃所有重复条目集。

```
>>> s.drop_duplicates(keep=False)
1       cow
3    beetle
5     hippo
Name: animal, dtype: object
```

Examples

Generate a Series with duplicated entries.

s = pd.Series(['llama', 'cow', 'llama', 'beetle', 'llama', 'hippo'],
name='animal')
s
0 llama
1 cow
2 llama
3 beetle
4 llama
5 hippo
Name: animal, dtype: object
With the 'keep' parameter, the selection behaviour of duplicated values can be changed. The value 'first' keeps the first occurrence for each set of duplicated entries. The default value of keep is 'first'.

s.drop_duplicates()
0 llama
1 cow
3 beetle
5 hippo
Name: animal, dtype: object
The value 'last' for parameter 'keep' keeps the last occurrence for each set of duplicated entries.

s.drop_duplicates(keep='last')
1 cow
3 beetle
4 llama
5 hippo
Name: animal, dtype: object
The value False for parameter 'keep' discards all sets of duplicated entries.

s.drop_duplicates(keep=False)
1 cow
3 beetle
5 hippo
Name: animal, dtype: object
