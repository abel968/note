# GoLang
## Windows下配置Go 以及vscode环境
1. 在官网上下载最新Go语言msi安装包，并安装，安装完毕后，go.exe所在路径被加入PATH环境变量中，`${GOROOT}`设置为Go的安装路径，`{GOPATH}`为用户开发目录。
2. vscode中安装Go插件，并在用户设置文件中输入：
    ```
    "go.buildOnSave": "workspace",
    "go.lintOnSave": "package",
    "go.vetOnSave": "package",
    "go.buildTags": "",
    "go.buildFlags": [],
    "go.lintFlags": [],
    "go.vetFlags": [],
    "go.coverOnSave": false,
    "go.useCodeSnippetsOnFunctionSuggest": false,
    "go.formatOnSave": true,
    "go.formatTool": "goreturns",
    "go.goroot": "C:\\Go",
    "go.gopath": "D:\\GoPath",
    "go.gocodeAutoBuild": false
   ```
3. 打开任意Go文件，vscode中会提示安装Go第三方插件，选择Install All，其安装位置为用户设置中的go.gopath。其中guru和gorename需要翻墙，可以在github上下载[镜像](https://github.com/golang/tools),git clone在`${GOPATH}/golang.org/x/`中；然后再目录tools/cmd/guru 和 tools/cmd/gorename下分别运行go install命令安装。其它插件可能会下载到本地但未安装的情况，需要按照vscode输出提示，去对应的目录下执行go install命令安装。
## 简介
```Go
package main
import "fmt"
func main(){
    fmt.Printf("Hello World")
}
```
- 所有Go文件以`package <something>`开头，表明源代码所属代码包
- 同一个路径下只能存在一个package。要生成可执行程序，必须有main的package包下的main()函数
- main包导入其他包，将顺序导入。若导入的包依赖别的包（包B），会先导入包B，初始化包B中的常量和全局变量，若B中有init函数，会自动执行init()。若一个包被导入多次则该包只会被导入一次。
- import 别名的用法。如`import test "fmt"`则可以用test来代替fmt。`import . "fmt"`调用的时候可以省略包名。`import _ "fmt"`将会执行包中的init()函数，但不会调用包内的函数。
- 若将多条语句放在同一行，需要添加分号，否则不需要
### 变量、类型、关键字
- 变量的声明为`var a int`声明变量a为int类型  
- byte是uint8的别名，rune是int32的别名，uintptr是无符号整型，用于存放一个指针··。浮点类型有float32和float64(**没有float类型**),int和uint类型后面可以跟8,16,32,64。如：`int8`等。
- 若声明且赋值可以使用`a:=5` **（这一形式只能用于函数中）**
- 全局变量的声明必须使用var关键字，局部变量可以被省略
- 变量的可变性规则：全局变量大写字母开头是其他包可以读取的，相当于公有变量；小写字母是不可导出，是私有变量
- 多个var声明可以合并，const和import也同样允许，如：
  ```Go
  var (
      x int
      b bool
  )
  ```
- 别的用法有:
  ```Go
  var x, y int
  a, b := 10, 20
  ```
- 特殊常量iota，只能在常量定义时使用。在const关键字出现时被置0， 在const组合形式中，每新增一行常量iota计数一次。
- 有一个特殊的变量为`_`，任何赋给它的值将被丢弃，如：`_,b := 34, 35`  Go编译器对声明但未使用的变量报错
- 类型都是独立的，混用类型赋值会报错，就算将int型赋值给int32型也会报错
- 赋值可以使用八进制（`077`）、十六进制（0xFF）、科学计数法（1e3）
- Go中的字符串是不可变的
- 多行字符串，如：
  ```Go
  s := "fdsfds" +
       "dfsfsdl"
  //或者
  s := `dsfldksjf
        fdsfdsfdsfdsf`
  ```
- Go原生支持复数 如complex128（64位虚数）或者 complex64，使用方式为`var c complex64 = 5+5i`
### 控制结构
- if-else
  与c相比，无需圆括号，语句体必须包含在大括号内。且{必须与if在同一行  
  Go中的nil相当于c中的NULL  
- goto
  goto跳转到的一定是当前函数内定义的标签，标签大小写敏感，与汇编的标签方法一样。
- for
  1. for init; condition; post { } //和C的for一样
  2. for condition { }             //和while一样
  3. for { }                       //死循环  
  Go中没有逗号表达式，所以若想在for中使用多个变量，需用**平行赋值**  
  **循环嵌套时，想用break退出，可以在break后面指定标签，用标签决定哪个被终止**  
- range
  range关键字可用于循环中，可以在slice,array,string,map,channel中。当被调用时，从它循环的内容返回一个键值对。如：
  ```Go
  list := []string{"a", "b", "c"}
  for k,v := range list {

  }
  ```
- switch
  switch表达式不必是常量或整数，若switch没有表达式，会匹配true（**据此可以用swich写if-elseif-else序列**）。  
  匹配成功之后不会自动向下尝试，相当于每个语句体内自动有break。若要继续向下执行，需要在语句体中加入fallthrough，加入fallthrogh后不会判断下一个case的表达式，会直接执行下一个语句体  
  使用default可以指定所有分支都不匹配的行为  
  分支可以使用逗号分隔的列表，如：`case ' ','?','=','+'` 逗号相当于or  
  switch可以用来判断类型，如：
  ```Go
  var a interface{}
  a = 32
  switch a.(type) {
    case int:
         //do something
    case string:
         //do something
    default:
         //do something
  }
### 内建函数
close new panic complex delete make recover real len append print imag cap copy println
- array 
  由[n]<type>定义 如：`var arr [100]int; arr[0]=100`  
  大小是类型的一部分，所以不同大小的数组是不同类型  
  将一个数组赋值给别的数组会复制所有元素，向函数传递数组时，会获得数组的副本而不是指针  
  `a := [3]int{1,2,3}`也可以写成`a := [...]int{1,2,3}`  
  若使用二维数组，则`a := [3][2]int{ [...]int{1,2}, [...]int{3,4}, [...]int{5,6}}`。这个语法可以进一步被简化为`a := [3][2]int{ {1,2} {3,4} {5,6}}` 元素复合声明的类型与外部一致时可以省略
- slice
  slice与array类似，但其长度可以被改变。slice总是指向底层的一个array，是引用类型。所以赋值某个slice到另一个变量，两个引用会指向同一array。函数调用也是传递引用。  
  `s := make([]int, 10)`创建了长度为10的slice  
  给定一个array或者slice，一个新slice可以通过`slice := arr[I:J]`创建，指向a+I,长度为J-I。可以使用`arr[:]`的形式。`len(slice)`表示当前slice的长度，`cap(slice)`表示slice所指的位置，到底层array末尾的长度  
- append
  可以向slice追加，如：
  ```Go
  s0 := []int{0,0}
  s1 := append(s0,2)
  s2 := append(s1,3,5,6)
  s3 := append(s2,s1...) //注意追加slice时，后面须有三个点
  ```
- copy
  从源slice src复制元素到目标dst，并返回复制元素个数。复制的数量为len(src)和len(dst)的最小值。源和目标可能重叠，如：
  ```Go
  a := []int{0,1,2,3,4,5,6,7}
  s := make([]int, 6)
  n1 := copy(s, a[:])  //n1=6,  s={0,1,2,3,4,5}
  n2 := copy(s, s[2])  //n2=4,  s={2,3,4,5,4,5}
  ```
- map
  一般定义map的方法是：map[<from type>]<to type>如：
  ```Go
  monthdays := map[string]int{
    "Jan": 31, "Feb": 28, "Mar": 31,       //最后一个逗号是必须的
  }
  ```
  若只需要声明一个map时，使用make形式，如：`monthdays := make(map[string]int)`  
  对array、slice、string或map循环遍历时，可以用range，它每次返回一对键和值。如：
  ```Go
  for _, days := range monthdays {   // 键不使用时，可以用_接收
    // do something
  }
  ```
  向map增加元素可以直接`monthdays["Undecim"] = 30`  
  检查某个元素是否在map存在时，可以使用：
  ```Go
  v, ok := monthdays["Jan"] //若存在，则ok为true
  ```
  从map中删除元素可以使用`delete(monthdays, "Mar")`
## 函数
函数定义,如：`func (p type) funcname(q int) (r,s,int) {return 0,0}`  
可以随便安排函数位置，编译器会在执行前扫描所有文件。






