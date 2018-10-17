## 基础
- python的变量在内存中只是一个标签，变量改变内容时，其内存的值不会改变，而是将标签贴到其他内存位置上。`type`可以查看对象的类型
- python3之后没有溢位问题，即存储整数大小无上限，取决自身内存限制
- 可以使用乘号快速建立重复字符串。如：`a = 'a'*5`
- `a = 'abcde'; print(a[::-1])` 输出edcba
- 字符串`name='Henny'`不可以使用`name[0]='P'`来替换，应该使用`name.replace('H', 'P')`。因为变量内的字符串属于不能替换内容，类似tuples
- `len(string)` `string.split(',')`分割字符串，默认参数会切割换行、tab和空格三种。
- 按照某个分割符将字符串列表合成一个字符串  使用了string.join(list)方法
  ```python
  s = ['aaa', 'bb', 'ccc', 'ddd']
  print(', '.join(s))    # 输出 aaa, bb, ccc, ddd
  ```
- string.startswith(str) string.endswith(str) 可以检查开头结束字串是否为特定字串。
- string.find(str) string.rfind(str) string.count(str)
- 其他string内建函数, 都不改变调用的string对象。　isalnum() strip('.') capitalize() title() upper() lower() swapcase() center(int) ljust(int) rjust(int) replace(str1, str2) replace(str1, str2, int count) 
## 容器
- list可以改变内容以及增减长度,tuples赋值之后就不能修改. 但性能和内存使用量来说, tuple更好
- list的内建函数  exten()或者+=, insert(), del Object 删除某个位置的元素,剩余元素会自动往前填补, remove(), pop, index(), in Object, count()
- list.sort()为list排序方法, sorted为通用的排序函数. 区别是前者会直接改变输入list, 后者会回传一个排序好的object
- list设定变量使用=是传地址. 若要赋值的话可以使用copy() 或 list() 或 list[:]. 如`c = a.copy() d = list(a) e = a[:]`
- tuples创建时可以省略括号. 但是若只有一个元素的时候, 逗号不能省略. 如 `a = 'tm', `
- dict的内建函数  update() 合并不同的dict,  del Object,  in Obeject, D.keys(), D.values(), D.items(), D.copy(), D.clear()
- 创建例子 `dic = {'a':1, 'b':2}`
- 集合就是没有value的dict 使用大扩号, dict转成set只会保留key值
## 基础2
- 使用\可以连接多行
- while语句后面使用else 可以在没有使用break的情况时进入
- zip()可以对多组Object同时进行循环迭代. `list(zip(list1, list2)), dict(zip(list1, list2))` 且zip会自动取多个容器中的最短长度. zip可以不止两个Object参数.
- 推导式
  1. list `listObj = [expression for item in iterable if condition]`
        `c = [(r,c) for r in range(3) for c in range(2)]` 该列表有6个元素 
  2. dict `dictionaryObj = { key_expression : value_expression for item in iterable if condition}`
  3. set `set_obj = {expression for item in iterable if condidion}`
  4. tuples没有推导式用法, 用()包起来的是generator推导式用法. 且只能用一次.
     ```python
     number_thing = (number*3 for number in range(3))
     print(number_thing)  # 输出不是tuples, 而是一个generator对象
     # l1 = list(number_thing) 可以把generator转换成列表
     for number in number_thing:
         print(number)    # 输出 0 3 6
     for number in number_thing:
         print(number)    # 什么也不输出, 因为generator只能用一次
     
     ```
- Function
  1. 使用list作默认参数可能会和想象的不一样 原因是: 函数的定义相当于类型构造, 将执行默认参数的构造, 而函数调用不会重新执行默认参数的构造
  ```python
    def buggy(arg, result = [])  # 使用list作默认值可能会发生意料外的事
        result.append(arg)
        print(result)
    buggy('a') # 输出[a]
    buggy('b') # 输出[a, b]
  ```
  2. *收集的参数以tuples储存, **收集的参数以dictionary储存. dictionary输入的格式为`print_kwargs(wine='dsff', fds='fsd')`
  3. 说明文字. 可以在函数第二行直接加入字符串, 为说明文字. 可以使用`help(function_name)`查看  或者`print(function_name.__doc__)`只显示文字.
  4. function也是对象,可以将function当作参数传入函数, 也可以将function当成结果回传
  5. function内部也可以再定义function
  6. **闭包**. 可以动态生成函数, 用来记录外部传入的变量  [闭包](https://foofish.net/python-closure.html)
  ```python
  def knight(saying):
      def inner():
          return "Here is %s" % saying
      return inner
  a = knight('XDD')
  print(a())
  ```
  7. lambda 如: `lambda word: word.capitalize() + '!'`
  8. generator 生成器式用来创建一个序列Object，但是又可以不用事前将一整个序列存到内存中，会随着每一次执行而改变数值
     内建的range()就是一个生成器. 生成器其实也是使用定义函数的方式定义, 只是将return改成yield. 则当执行到yield的位置时返回, 当再次调用生成器时, 从yield位置继续执行
  9. decorators [装饰器](https://foofish.net/python-decorator.html)
- main函数中的变量称为全局变量, 可以在函数中调用, 但不能改变它, 若试图改变会报错. 若想在函数内部改变全局变量, 可以使用global
- try:..........except:...........
## 模块 包 程序
- 命令行参数  `import sys` `prin(sys.argv[1])`
- import会默认搜索主程序相同路径的目录, 没有则搜索安装目录的/lib目录
- packages打包. 将函数库用文件夹管理. 比如一个sources目录, 其中有三个文件: __init__.py, daily.py, weekly.py 其中后两个中定义了对应函数, __init__.py时一个空文件, 目的是让python将sources视为一个函数库使用. 然后主程序可以使用import导入sources中的函数
- python标准库........
## 类
-  `class Person():` 小括号里应该为继承的类
- 继承时可以使用super()来调用父类的函数. 如在__init__函数中调用super().__init__
- 使用property() 来隐藏属性值, getter setter **还不太懂**
- 在属性前面加上__来重整名称, 虽不能完全防止修改私有属性, 但可以降低无意的修改. 如类A中的变量__name, 则调用时应使用_A__name
- 类的属性和方法. 类的属性声明直接写在类中, 调用使用`类.属性`的方式. 类的函数在声明时前面加`@classmethod` 或者 `@staticmethod`, 差异是前者第一个参数需为cls参数, 后者不需要, 所以后者调用该类的属性只能直接用名称取得, 后者因为有cls参数传入, 所以不需要.
- 多态, 不管object本身的类型是什么, 只要拥有相同的方法就可以调用到
- 特殊方法(以__开头和结尾的方法)
  |方法名称|调用|
  |:-:|:-:|
  |__eq__(self, other)|self == other |
  |__ne__(self, other)|self != other |
  |__lt__(self, other)|self < other |
  |__gt__(self, other)|self > other |
  |__le__(self, other)|self <= other |
  |__ge__(self, other)|self >= other |
  |__add__(self, other)|self + other |
  |__sub__(self, other)|self - other |
  |__mul__(self, other)|self * other |
  |__floordiv__(self, other)|self // other |
  |__truediv__(self, other)|self / other |
  |__mod__(self, other)|self % other |
  |__pow__(self, other)|self ** other |
  |__str__(self) | str(self) |
  |__repr__(self) | repr(self) |
  |__len__(self) | len(self) |
## 数据
- 编码
- 格式化
  1. `string % data`  其中有 %s %d %x十六进制 %o八进制 %f %e科学计数法表示的浮点数 %g十进制或科学计数法表示的浮点数 %%(转义%本身)
  2. {}.format() 新的写法,推荐. format中传入参数, {0}表示第一个参数, {1}表示第二个参数. 如 `'{} {} {}'.format(4,5,6)` 与`'{0} {1} {2}'.format(4,5,6)`等价. 另外可以使用类似 `'{0:10d}.format(4)`来格式化输出, 表示占10位
- 正则表达式 需要import re. 函数都是第一个参数为pattern, 第二个参数为source
  ```python
  import re
  # 1. match
  result = re.match('You', 'Young fkdsj') # 查看字串是否以规定的规则开头, 输出result.group()为'You'
  youpattern = re.compile('You') # 可以先编译一个模式来匹配或其它操作
  result = youpattern.match('Young fkdsj') # 与第一个相同
  m = re.search('Frank', 'Young Frankeelkj') # 查看是否有该规则的子串, m.group()为'Frank'
  m = re.findall('n', 'Young Frankeelkj') # 找到所有符合的, 返回一个列表. 共找到个数为 len(m)
  m = re.split('n', 'Young Frankeelkj') # 按照规则来分割, 返回一个列表
  m = re.sub('n', '?', 'Young Frankeelkj') # 按照规则来替换, 返回一个列表
  ```
  |特殊字元 | 表示 |
  | :-: | :-: |
  | . | 表示任意除了\n外的字符 |
  | * | 表示任意多个字符, 包括0个 |
  | ? | 表示一个或0个字符 |
  | \d | 等价[0-9] |
  | \D | 等价[^0-9] |
  | \w | 等价[A-Za-z0-9_] |
  | \W | 等价[^A-Za-z0-9_] |
  | \s | 空白字元, 等价[\f\n\r\t\v] |
  | \S | 空白字元, 等价[^ \f\n\r\t\v] |
  | \b | 单词边界, 一个 \w与\W之间的范围, 顺序可逆 |
  | \B | 非单词边界 |

  | 规则 | 功能 |
  | :-: | :-: |
  | abc | 文本abc |
  | (expr) | expr |
  | expr1|expr2 | expr1或expr2 |
  | ^ | 源字符串的开头 |
  | $ | 源字符串的结尾 |
  | prev+ | 一个或多个prev |
  | prev{m} | m个连续的prev |
  | prev{m,n} | m到n个连续的prev, 尽可能多的匹配 |

- bytes bytearray 前者不可变, 后者可变

## numpy
- ndarray n维数组对象 其所有元素必须是相同类型, 且有两个属性: shape表示维度, dtype表示数组类型. 可以使用np.array(list1)创建ndarray
- np.random.randn(2, 3) 返回2*3的随机矩阵
- np.zeros(10) np.zeros((3, 6)) np.empty((2,3,2)) np.arange(15), ones, ones_like以另一个数组为参数根据其形状和dtype创建全1的数组, zeros_like, empty_like, full, full_like, eye, identity
- dtype  例: `np.array([1,2,3], dtype = np.float64)`
- ndarray 的astype方法可以转换dtype 如: `arr.astype(np.float64)`
- 数组的广播 numpy可以对维数不同的数组进行广播, 以便形状兼容 [广播](https://www.cnblogs.com/jiaxin359/p/9021726.html)
- 索引和切片
  1. 将一个标量值赋值给一个切片时, 该值会自动传播到整个选区. 与list不同的是, 切片是原始数据的视图, 意味着数据不会被复制, 视图上的任何修改都会反映到源数组上. 若想得到numpy的副本而不是视图, 则要明确的复制 如: `arr[5:].copy()`
  2. 对于二维ndarray, 两种方式等价 `arr2d[0][2]` `arr2d[0,2]`
  3. 多维数组中, 若省略后面的索引, 返回对象是一个维度低一点的ndarray(它有高一级维度上的所有数据) 如对于一个4*3的数组arr, arr[2]的形状是(3,); 而对于切片arr[2,:]形状是(1,3)
  4. **布尔型索引** 即在索引的位置上放布尔型数组, 要求该数组长度必须与索引轴长度一致. 如对于一个`7*4`的arr, `arr[0,BoolArr]`则会输出第0行, BoolArr为true的对应位置的值. 布尔型数组可以不仅仅是一维的. 如`arr[arr<0] = 0` 此时的布尔型数组为7*4的, 它将arr数组中所有小于0的数变成0. 但若使用`data = arr[arr<0]`, 则与切片不一样, 会将数据复制到新数组里
  5. 花式索引, 类似布尔值索引. 就是将一个数组放在对应索引位置, 数组中的0表示第一个, -1表示最后一个. 但一次传入多个索引数组会比较特别, 将返回一个一维数组, 如arr为8*4的数组, 则`arr[[1,5,7], [0,3,1]]`返回一个(3,)数组, 对应元素为(1,0) (5,3) (7,1). 这与我们期待的不太一样. 我们希望的是得到一个矩形区域, 若想得到我们期望的, 应该为`arr[[1,5,7]][:, [0,3,1]]`. 使用一个变量指向花式索引时,同样是将数据复制到新数组中.
- 数组转置和轴对换
  1. 数组有一个特殊的T属性, 为最简单的转置. 
  2. transpose需要得到一个由轴编号组成的元祖才能转置. 如arr为`2*3*4`的数组, `arr.transpose((1,0,2))`得到`3*2*4`的数组
  3. swapaxes 接受一对轴编号, 进行两个轴的对换.
  4. 都是返回源数据的视图, 不会进行任何复制操作
- 通用函数(针对单个元素级别的) 即**ufuncs**
  1. np.sqrt(arr) np.exp(arr)
  2. np.maximum(arr1, arr2) arr1和arr2形状相同, 并返回一个形状相同的数组
  3. modf返回多个数组, 如`ermainder, whole_part = np.modf(arr)`返回浮点数数组的小数和整数部分
  4. ufuncs都可以多接收一个out参数, 如np.sqrt(arr, arr) 则开方后的结果直接保存在arr中
  5. 一元ufuncs有: abs可以计算整数 浮点数 复数; fabs更快的计算非复数值; sqrt; square; exp; log log10 log2 log1p为log(1+x); sign 正数为1, 负数为-1; ceil floor; arccos arccosh双曲余弦 arcsin arcsinh arctan arctanh; logical_not 计算各元素not x的真值
  6. 二元ufuncs有: add subtract multiply divide floor_divide power maximum fmax忽略NaN minimum fmin忽略NaN mod copysign将第二个数组中的值的符号复制给第一个数组中并返回(第一个数组不改变)
- 数组统计方法
  1. numpy.where 是函数三元表达式, 表示x if conidtion else y. 如`result = np.where(cond, xarr, yarr)` cond为bool数组, xarr和yarr可以是不一定是数组可以是标量. 如np.where(arr>0, 2, -2) 将所有正值变成2, 负值变成-2
  2. 聚合计算arr.mean() arr.sum() 这类的函数可以接受一个axis选项参数, 用于计算该轴上的统计值. 最终结果是少一维的数组.
  3. cumsum() cumprod() 累加和累乘 如`arr.cumsum()`若arr为[1,2,3], 结果为[1,3,6]. 多维数组中, 接受axis参数, 返回同样大小的数组, 在axis对应轴上进行操作. 
  4. 基本数组统计方法. sum; mean; std标准差 var方差; min max; argmin argmax最小最大元素索引; cumsum cumprod
  5. 上面的方法中, bool值会转换成0和1 所以`(arr>0).sum()`可以得到arr中大于0元素的个数
  6. any()检测数组是否至少一个为true all()检测是否都为true
  7. 排序. np.sort(a, axis, kind, order) a为数组, axis沿着它排序的轴,若没有 沿着最后的轴排序, kind为排序方式 默认'quicksort' 还有'mergesort' 'heapsort', order 若数组包含字段 为要排序的字段.
  8. np.argsort() 间接排序, `x[np.argsort(x)]`
  9. np.lexsort() 使用键序列执行间接排序,返回一个索引数组. 最后一个键恰好是sort的主键
  10. np.unique() 去掉重复值并排序, 相当于 `sorted(set(arr))`
  11. np中的集合函数还有, unique(x) intersect1d(x, y) union1d(x,y)都返回有序结果; in1d(x,y) 表示x元素是否包含于y; setdiff1d(x,y); setxor1d(x,y)集合的对称差 相当于`np.union1d(np.setdiff1d(x,y), np.setdiff1d(y,x))`
- np的文件输入输出
  1. np.save('filename', arr) 默认以未压缩的原始二进制格式保存在扩展名的.npy文件中; arr = np.load('filename.npy')
  2. np.savez可以将多个数组保存在一个未压缩文件中, np.savez('file.npz', a = arr1, b = arr2); arch = np.load('file.npz') arr2=arch['b']
  3. 若要将数据压缩, 可以使用np.savez_compressed  用法`np.savez_compressed('file.npz', a = arr1, b = arr2)`
- 线性代数
  1. np.dot(x,y) 等价于 x.dot(y)
  2. 一个二维数组和一个大小合适的一维数组的矩阵点积运算后得到一个一维数组
  3. 