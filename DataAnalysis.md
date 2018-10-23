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

