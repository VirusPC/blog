---
title: Pandas使用指南
categories: ['Python']
tags: [‘pandas’， ‘数据处理’]
resource_path: /blog/assets/2010/
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

Pandas使用指南
===

1. [简介](#简介)
2. [基本概念](#基本概念)
3. [访问数据](#访问数据)
4. [操控数据](#操控数据)
5. [索引](#索引)
6. [常用命令整理](#常用命令整理)

---

简介
---

1. 学习目标  
    * 大致了解 pandas 库的 DataFrame 和 Series 数据结构  
    * 存取和处理 DataFrame 和 Series 中的数据  
    * 将 CSV 数据导入 pandas 库的 DataFrame  
    * 对 DataFrame 重建索引来随机打乱数据  
2. 简介  
    * pandas 是一种列存数据分析 API。它是用于处理和分析输入数据的强大工具，很多机器学习  
     框架都支持将 pandas 数据结构作为输入。
    * pandas 文档网站：http://pandas.pydata.org/pandas-docs/stable/index.html


基本概念
---

1. 版本  
    以下行导入了 pandas API 并输出了相应的 API 版本：  
    ```python
    import pandas as pd
    ```
2. 数据结构  
    * DataFrame，您可以将它想象成一个关系型数据表格，其中包含多个行和已命名的列。
    * Series，它是单一列。DataFrame 中包含一个或多个 Series，每个 Series 均有一个名称
    * 数据框架是用于数据操控的一种常用抽象实现形式。Spark 和 R 中也有类似的实现。

3. 创建数据结构  
    * 创建Series  
        ```python
        pd.Series(['San Francisco', 'San Jose', 'Sacramento'])
        ```
    * 创建DataFrame  
        * 将映射 string 列名称的 dict 传递到它们各自的 Series，从而创建DataFrame对象。如果 Series 在长度上不一致，系统会用特殊的 NA/NaN 值填充缺失的值。  
            ```python
            city_names = pd.Series(['San Francisco', 'San Jose', 'Sacramento'])
            population = pd.Series([852469, 1015785, 485199])
            pd.DataFrame 'City name': city_names, 'Population': population }
            ```
        * 将整个文件加载到 DataFrame 中  
            ```python
            california_housing_dataframe = pd.read_csv("ch.csv", sep=",")
            california_housing_dataframe.describe()
            ```

4. DataFrame方法  
    * 显示关于 DataFrame 的有趣统计信息  
        ```python
        california_housing_dataframe.describe()
        ```
    * 显示 DataFrame 的前几个记录  
        ```python
        california_housing_dataframe.head()  # 可传入一个数量限制大小参数
        ```
    * 绘制图表  
        ```python
        california_housing_dataframe.head() # 与matplotlib包配合使用
        ```


三.访问数据
1. 您可以使用熟悉的 Python dict/list 指令访问 DataFrame 数据  
    ```python
    cities = pd.DataFrame({'City name': city_names, 'Population': population})
    print(type(cities))  # <class 'pandas.core.frame.DataFrame'>
    print(type(cities['City name']))  # <class 'pandas.core.series.Series'>
    print(type(cities['City name'][1]))  # <class 'str'>
    print(type(cities[0:2]))  # <class 'pandas.core.frame.DataFrame'>
    ```
2. 此外，pandas 针对高级索引和选择提供了极其丰富的 API  


四.操控数据  
1. 可以向 Series 应用 Python的基本运算指令。  
    * ``` population / 1000.  ```
    * 布尔值 Series 是使用“按位”而非传统布尔值“运算符”组合的。例如，执行逻辑与时，应使
     用 &，而不是 and。 

2. NumPy  
    Numpy是一种用于进行科学计算的常用工具包。pandas Series 可用作大多数 NumPy 函数的参数  
    ```python 
    import numpy as np
    np.log(population)
    ```

3. Series.apply  
    像 Python 映射函数一样，Series.apply 将以参数形式接受 lambda 函数，而该函数会应
     用于每个值。下面的示例创建了一个指明 population 是否超过 100 万的新 Series：  
    ```python
    population.apply(lambda val: val > 1000000)
    ```

4. 向DataFrame添加Serires  
    ```python
    cities['Area square miles'] = pd.Series([46.87, 176.53, 97.92])
    cities['Population density'] = cities['Population'] / cities['Area square miles']
    ```

索引
---

1. 简介  
    * Series 和 DataFrame 对象也定义了 index 属性，该属性会向每个 Series 项或
     DataFrame 行赋一个标识符值。
    * 默认情况下，在构造时，pandas 会赋可反映源数据顺序的索引值。索引值在创建后是稳定
     的；也就是说，它们不会因为数据重新排序而发生改变。

2. DataFrame.reindex  
    * 调用 DataFrame.reindex 以手动重新排列各行的顺序。例如，以下方式与按城市名称排序
     具有相同的效果：  
        ```python
        cities.reindex28([2, 0, 1])
        ```
    * 重建索引是一种随机排列 DataFrame 的绝佳方式。在下面的示例中，我们会取用类似数组的索引，然后将其传递至 NumPy 的 random.permutation 函数，该函数会随机排列其值的位置。
        ```python
        cities.reindex(np.random.permutation(cities.index))
        ```
    * reindex 方法允许使用未包含在原始 DataFrame 索引值中的索引值。如果您的 reindex
     输入数组包含原始 DataFrame 索引值中没有的值，reindex 会为此类“丢失的”索引添加新
     行，并在所有对应列中填充 NaN 值：


常用命令整理
---

1. 导入数据  
    * ```pd.read_csv(filename)```：从CSV文件导入数据
    * ```pd.read_table(filename)```：从限定分隔符的文本文件导入数据
    * ```pd.read_excel(filename)```：从Excel文件导入数据
    * ```pd.read_json(json_string)```：从JSON格式的字符串导入数据
    * ```pd.DataFrame(dict)```：从字典对象导入数据，Key是列名，Value是数据
2. 导出数据
    * ```df.to_csv(filename)```：导出数据到CSV文件
    * ```df.to_excel(filename)```：导出数据到Excel文件
    * ```df.to_json(filename)```：以Json格式导出数据到文本文件

3. 创建测试对象
    * ```pd.DataFrame(np.random.rand(20,5))```：创建20行5列的随机数组成的DataFrame对象
    * ```pd.Series(my_list)```：从可迭代对象my_list创建一个Series对象
    * ```df.index = pd.date_range('1900/1/30', periods=df.shape[0])```：增加一个日期索引
4. 查看、检查数据
    * ```df.head(n)```：查看DataFrame对象的前n行
    * ```df.tail(n)```：查看DataFrame对象的最后n行
    * ```df.shape()```：查看行数和列数
    * ```df.info()```：查看索引、数据类型和内存信息
    * ```df.describe()```：查看数值型列的汇总统计
    * ```s.value_counts(dropna=False)```：查看Series对象的唯一值和计数
    * ```df.apply(pd.Series.value_counts)```：查看DataFrame对象中每一列的唯一值和计数

5. 数据选取
    * ```df[col]```：根据列名，并以Series的形式返回列
    * ```df[[col1, col2]]```：以DataFrame形式返回多列
    * ```s.iloc[0]```：按位置选取数据
    * ```s.loc['index_one']```：按索引选取数据
    * ```df.iloc[0,:]```：返回第一行
    * ```df.iloc[0,0]```：返回第一列的第一个元素

6. 数据清理
    * ```df.columns``` = ['a','b','c']：重命名列名
    * ```pd.isnull()```：检查DataFrame对象中的空值，并返回一个Boolean数组
    * ```pd.notnull()```：检查DataFrame对象中的非空值，并返回一个Boolean数组
    * ```df.dropna()```：删除所有包含空值的行
    * ```df.dropna(axis=1)```：删除所有包含空值的列
    * ```df.dropna(axis=1,thresh=n)```：删除所有小于n个非空值的行
    * ```df.fillna(x)```：用x替换DataFrame对象中所有的空值
    * ```s.astype(float)```：将Series中的数据类型更改为float类型
    * ```s.replace(1,'one')```：用‘one’代替所有等于1的值
    * ```s.replace([1,3],['one','three'])```：用'one'代替1，用'three'代替3
    * ```df.rename(columns=lambda x: x + 1)```：批量更改列名
    * ```df.rename(columns={'old_name': 'new_ name'})```：选择性更改列名
    * ```df.set_index('column_one')```：更改索引列
    * ```df.rename(index=lambda x: x + 1)```：批量重命名索引

7. 数据处理：Filter、Sort和GroupBy
    * ```df[df[col] > 0.5]：```选择col列的值大于0.5的行
    * d```f.sort_values(col1)：```按照列col1排序数据，默认升序排列
    * d```f.sort_values(col2, ascending=False)：```按照列col1降序排列数据
    * ```df.sort_values([col1,col2], ascending=[True,False])```：先按列col1升序排列，后
     按col2降序排列数据
    * ```df.groupby(col)```：返回一个按列col进行分组的Groupby对象
    * ```df.groupby([col1,col2])```：返回一个按多列进行分组的Groupby对象
    * ```df.groupby(col1)[col2]```：返回按列col1进行分组后，列col2的均值
    * ```df.pivot_table(index=col1, values=[col2,col3], aggfunc=max)```：创建一个按列
     col1进行分组，并计算col2和col3的最大值的数据透视表
    * ```df.groupby(col1).agg(np.mean)```：返回按列col1分组的所有列的均值
    * ```data.apply(np.mean)```：对DataFrame中的每一列应用函数np.mean
    * ```data.apply(np.max,axis=1)```：对DataFrame中的每一行应用函数np.max

8. 数据合并
    * ```df1.append(df2)```：将df2中的行添加到df1的尾部
    * ```df.concat([df1, df2],axis=1)```：将df2中的列添加到df1的尾部
    * ```df1.join(df2,on=col1,how='inner')```:对df1的列和df2的列执行SQL形式的join数据统计
    * ```df.describe()：查看数据值列的汇总统计df.mean()```：返回所有列的均值
    * ```df.corr()```：返回列与列之间的相关系数
    * ```df.count()```：返回每一列中的非空值的个数
    * ```df.max()```：返回每一列的最大值df.min()：返回每一列的最小值
    * ```df.median()```：返回每一列的中位数
    * ```df.std()```：返回每一列的标准差
9. 数据统计
    * ```df.describe()```：查看数据值列的汇总统计
    * ```df.mean()```：返回所有列的均值
    * ```df.corr()```：返回列与列之间的相关系数
    * ```df.count()```：返回每一列中的非空值的个数
    * ```df.max()```：返回每一列的最大值
    * ```df.min()```：返回每一列的最小值
    * ```df.median()```：返回每一列的中位数
    * ```df.std()```：返回每一列的标准差

---

部分参考资料
----
[Pandas速查手册中文版](https://zhuanlan.zhihu.com/p/25630700)

