- 处理缺失值的函数.  
  1. dropna 设定how='all' 仅删除全为null的行, thresh规定有几个null才删除
  2. fillna 传入一个dict，可以给不同列替换不同的值. fillna返回一个新对象, 可以使用in-place来直接更改原有的数据; 还有一个method属性 ffill为用前一个有效值替换, bfill用后一个有效值替换
  3. isnull notnull
- 重复值
  1. data.duplicated() 返回bool数组, 是否重复为true
  2. data.drop_duplicates() 删除重复的行, 可以指定观测的列, 如`data.drop_duplicates(['k1'])`
- 用于series的map方法接受一个函数，或是一个字典，包含着映射关系
- 用replace，会产生一个新series（除非使用inplace=True） 如: `data.replace([-999, -1000], np.nan)data.replace(-999, np.nan)`将-999换成nan, `data.replace([-999, -1000], [np.nan, 0])`, `data.replace({-999: np.nan, -1000: 0})`
- rename 不直接改变数据, 可以用inplace直接修改原始数据
- 离散化和装箱 如:  
  1. 
  ```python
  ages = [20, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
  bins = [18, 25, 35, 60, 100]
  cats = pd.cut(ages, bins)
  print(pd.value_counts(cats))
  # 可以用一个list或数组给labels选项来设定bin的名字
  # group_names = ['Youth', 'YoungAdult', 'MiddleAged', 'Senior']
  # pd.cut(ages, bins, labels=group_names)
  ```
  返回的是一个特殊的Categorical object. 按照(18,25] (25,35].....的方式分组  
  cats.codes输出当前数字属于哪一组, 组号从0开始  
  cats.categories为组名,即labels, 默认组名为分组的方式,如(18,25]  
  2. 也可以不自己设定bins. 如 `pd.cut(data, 4, precision=2)`  表示分成4组, 精确到小数点后2位.
  3. qcut, 会按照数据的分布来分箱, 得到每个bins具有相同数量的点. 如 `pd.qcut(data, 4)` 分成4个数据个数相同的组.
  4. cut中我们可以自己指定百分比 如: `pd.cut(data, [0, 0.1, 0.5, 0.9, 1.])`
- numpy.random.permutation 随机排序函数. 
  ```python
  sampler = np.random.permutation(5)  # 假设生成的随机序列为 [4,2,3,1,0]
  df.take(sampler) # 行的顺序按照该序列.
  ```
- sample函数可以返回一个随机子集. 如`df.sample(4)` 返回4*4的子集. 默认是不重复的, 若想允许重复可以设置参数replace=True
- pd.get_dummies
- df.columns.get_indexer
- df.add_prefix
- pd.get_dummies(pd.cut(values, bins))
- strip() 去除字符串的空格．
- 可以把一些字符串方法和正则表达式（用lambda或其他函数）用于每一个value上，通过data.map，但是这样会得到NA(null)值。为了解决这个问题，series有一些数组导向的方法可以用于字符串操作，来跳过NA值。这些方法可以通过series的str属性．　如: `s1.str.contains('gmail')`检查每个元素是否包含gmail, 若元素为NaN, 则返回也为NaN
- Hierarchical Indexing. 给出一个简单的例子，构建一个series，其index是a list of lists:
  ```python
  data = pd.Series(np.random.randn(9),
                 index=[['a', 'a', 'a', 'b', 'b', 'c', 'c', 'd', 'd'],
                        [1, 2, 3, 1, 3, 1, 2, 2, 3]])
  print(data['b']) # 对于这种分层索引, 可以进行部分索引, 如此时输出: 1 随机数  2 随机数
  ```
- 可以用unstack来把分层索引进行重新排列，产生一个DataFrame. 同样可以用stack()把DataFrame变成分层索引
- `pd.MultiIndex.from_arrays([['Ohio', 'Ohio', 'Colorado'], ['Green', 'Red', 'Green']], names=['state', 'color'])`
- swaplever
- sort_index是在一个层级上，按数值进行排序. 在交换层级的时候，通常也会使用sort_index，来让结果按指示的层级进行排序
- 在DataFrame和Series中，一些描述和归纳统计数据都是有一个level选项的，这里我们可以指定在某个axis下，按某个level（层级）来汇总
- 把DataFrame里的一列或多列作为行索引. 如: `frame2 = frame.set_index(['c', 'd'])` 将c和d列作为行索引. reset_index()功能与set_index相反, 将行索引变成列.
- 注意：当我们讲列和列进行连接时，DataFrame中的index对象会被丢弃。
- pd.merge  on left_on right_on how suffixes;  right_index left_index是否使用行索引作为键.对于多级行索引作为键, 如right_index=True, 则left_on应该是一个与多级行索引一致的列表
- DataFrame有一个很便利的join函数, 可以直接用index来连接. 这个也可以用于与其他DataFrame进行连接，要有一样的index但不能有重叠的列 
- concat 参数:　join join_axes keys names ignore_index
- combine_first 类似where. 即 = if else
- pivot   前两个传入的值是列，分别被用于作为行索引和列索引(date是行索引, item是列索引), 最后是一个是可选的value column(值列), 用于填充DataFrame
- 与pivot相反的操作是pandas.melt


## 画图
- `fig = plt.figure()`   fig.subplot(2,2,1)
- 因为创建一个带有多个subplot的figure是很常见的操作，所以matplotlib添加了一个方法，plt.subplots 如: `f, axes = plt.subplots(2, 3)`
- df.plot() df.plot.bar() df.plot.barh()
- pd.crosstab()  交叉表是用于统计分组频率的特殊透视表, 按类别分组,统计各个分组中产地的频数. 如 `pd.crosstab(df['类别'],df['产地'])`
- seaborn.......

## 10
- groupby 参数可以是列名, 也可以是与Series或df长度一致的数组.
- 可以`for name, group in df.groupby('key1'):` 对应组名和数据块.
- `pieces = dict(list(df.groupby('key1')))`  一个便利的用法是, 用一个含有数据片段（data pieces）的dict来作为单行指令(one-liner)
- goupby也可以传dict 用于映射, 如`mapping = {'a': 'red', 'b': 'red', 'c': 'blue', 'd': 'blue', 'e': 'red', 'f': 'blue'}` 则a  b e为一组red, cdf为一组blue
- 类似于使用dict映射, 也可以使用函数来映射. 
- 要想按层级分组，传入层级的数字或者名字，通过使用level关键字
- 想用自己设计的聚合函数，把用于聚合数组的函数传入到aggregate或agg方法即可
- 可以把多个聚合函数作为一个list传递给agg, 然后会得到一个df, 列名是自动给出的, 若想指定列名, 可以传入一个由tuple组成的list, 每个tuple的格式是(name, function)
- 若想把不同的函数用到多个列中, 可以传递一个dict给agg, dict包含一个列名和函数的映射关系.
- 在调用groupby的时候设定as_index=False 可以取消行索引.
- apply和agg的区别.
- 