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