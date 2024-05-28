
``` python
Pandas.set_option('display.max_columns', 500)
Pandas.set_option('display.width', 1000)
data[data.payment_item.map(lambda x:any(ele in x for ele in ['行李箱']))]
data[data.payment_item.apply(lambda x:any(ele in x for ele in ['行李箱']))]
pd.set_option('display.max_rows', None)  # 显示所有行
pd.set_option('display.max_columns', None)  # 显示所有列
pd.set_option('display.width', None)  # 不折叠单元格
pd.set_option('display.max_colwidth', None)  # 显示完整的单元格内容
```



### 数据的排序
- 基于标签的排序
``` python
Series.sort_index(ascending=False,na_posstion='last',ignore_index=True,inplace=True)

DataFrame.sort_index(axis=0,ascending=False,na_posstion='last',ignore_index=True,inplace=True)
```
- 基于元素的排序
``` python
# sort_values方法
Series.sort_values(ascending=False,na_posstion='last',ignore_index=True,inplace=True)
DataFrame.sort_values(by,ascending=False,na_posstion='last',ignore_index=True,inplace=True)

# nlargest方法
Series.nlargest(n)
DataFrame.nlargest(n,columns)

# nsmallest方法
Series.nsmallest(n)
DataFrame.nsmallest(n,columns)
```
