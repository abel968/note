## 第７章 函数介绍
- c++函数返回类型不能是数组，但可以将数组作为结构或对象组成部分返回
- **二维数组作为函数的参数**
  ```c++
  int data[3][4];
  int sum(int (*ar2)[4], int size);//[]优先级比*高，括号不能少，因为是4个指向int的指针组成的数组，而不是指向由4个int组成的数组的指针
  int sum(int ar2[][4], int size); //另一种格式，可读性更强。
  ```
- 函数名就是函数的地址。声明指向特定类型的函数的指针，先编写该函数的原型，然后用`(*function)`代替函数名即可。也可以*使用c++11的自动推断功能**
  ```c++
  double pam(int);   // 函数声明
  double (*pf)(int);  // 函数指针声明
  pf = pam;  //此时pf指向pam
  (*pf)(4);  //使用指针调用函数，应使用(*pf), c++也允许像函数名一样使用函数指针，如pf(4)。但用(*pf)可以显示正在使用函数指针。
  auto pf2 = pam; //可以使用c++11的自动推断类型功能。
  ```
- 声明函数指针数组。此时不能用auto，auto只能用于单值初始化，而不能用于初始化列表
  ```c++
  const double * (*pa[3]) (int) = {f1, f2, f3} //包含初始化
  auto pb = pa;  //此时可以用auto
  ```
- typedef 应用于函数指针类型的别名，如：`typedef double (*pf_name)(int)` 。此时pf_name是该函数指针类型的别名
## 第8章 函数深入
- 内联函数与普通函数不同的地方在于：普通函数被调用时是跳到另一个函数地址，而内联函数是由编译器将使用的函数代码替代函数调用，即不需要跳。因此内联函数运行更快，但占更多内存，一般用于代码量少调用多的函数。内联函数不能递归，但和普通函数一样是值传递（这是与宏定义主要区别）。
- 函数参数是基本数据类型时应使用按值传递，数据比较大用引用。引用只有在const类型才能传递表达式的值,此时编译器创建临时变量。
- 一种似乎不错的函数定义方式  `A& func(A& a);` 即将void改为A的引用，在调用上更随意。**将const用于返回引用可能有挺好的限制**
- 函数重载时，若参数不与任何原型匹配，则c++尝试使用类型转换再匹配，若只匹配到一个则调用，若多个则出错。const类型可用于重载
- 模板并不创建任何函数，只是告诉编译器如何定义函数，当需要某个类型的函数时，编译器按照模板格式创建这样的函数。
- 显式具体化
  ```c++
  template <typename T>
  void Swap(T&, T&);

  template <> void Swap<job>(job&, job&); //显式具体化  或者 template <> void Swap(job&, job&);
  template <> void Swap<job>(job&, job&)   //显式具体化定义
  {

  }
  ```
- 编译器选择函数原型时，先找非模板版本，然后找显示具体化，最后找普通模板。
- 实例化，分为隐式实例化和显示实例化。隐式的为调用的时候编译器生成，显式的为`template void Swap<int> (int, int);`
- 若有一个`Add(T, T)`模板。a为double，b为int。此时用`Add(a,b)`会不匹配，但可用`Add<double>(a,b)`
- **decltype** c++11新增关键字 `decltype(x) y`可以使y具有x的类型。若x是函数调用，并不会调用函数，而是查看函数返回类型。`decltype ((x)) y = x` 此时y为x类型的引用
- c++提供另一种函数声明方式。`auto h(int x, float y) -> double` auto是一个占位符，表示后置返回类型。这次为了用于模板。`auto h(T1 x, T2 y) -> decltype(x+y)`
- 在类中定义的函数为内联函数  
## 第九章 内存模型和名称空间
- 静态存储持续性变量的３种链接性：　外部链接性，内部链接性，无链接性。**静态变量在程序执行期间一直存在**。无链接性的静态变量用于在两次函数调用之间，静态局部变量的值保持不变。即若初始化了一次，则程序只在启动时进行一次初始化。
  ```c++
  int global = 1;　　　　　　　　　//外部链接性，可在其他文件访问
  static int one_file = 2;　　　　//内部链接性，只能在当前文件访问
  void func()
  {
      static int count = 0;    //无链接性，只能在当前代码块访问
  }
  ```
- volatile　由于编译器会优化代码，比如在几条语句访问一个变量两次时，编译器不会让程序查这个变量两次，而volatile会阻止这样的优化
- mutable  可以用于在结构体中的成员变量，即使结构体被声明const，但其被mutable修饰的成员也能改变
- const全局变量，会将其链接性改为内部。即`const int a = 5; static const int a = 5;` 它们的效果是一样的。这样可以将一组常量放在头文件中，同一个程序的多个文件使用该头文件也不会出错。再加extern可以将其链接性改为外部。
- 可以使用static将函数的链接性设置为内部的。可以用extern指出某个函数是在外部定义的。
- 语言链接性。c语言和c++会将function函数翻译为不同的名字，为了在c++程序中使用c库，可以使用extern
  ```c++
  extern "C" void spiff(int);  // 使用c语言的约定来搜索
  extern void spiff(int);  // 使用c++的约定来搜索
  extern "C++" void spiff(int);  // 使用c++的约定来搜索, 和上一个是一致的。
  ```
- new失败时引发异常std::bad_alloc
- 定位new运算符。。可以指定申请的位置。   P456 也有new定位 关于对象的
- 名称空间可以是全局的，也可以在另一个名称空间中。**但不能位于代码块中**。默认情况下，名称空间的声明的链接性是外部的，除非用了const
- `using Jill::fetch` 可以直接使用fetch
- 若名称空间和声明区域定义了相同的名称，若用using声明会出错，而用using 编译指令，局部会隐藏名称空间变量
- 可以自己定义一个名称空间myth，在该空间中使用using编译指令和using声明。然后直接using namespace myth;可以导入其空间内的所有名称
- 名称空间可以有别名。如:`namespace my = myth::std::fdsf;`
- 可以省略名称来创建为命名的名称空间。与链接性为内部的静态变量是等价的。
  ```c++
  namespace
  {
      int a;
      int b;
  }
  ```
- using声明只使用了名称，所以若函数被重载，则一个using声明导入所有同名函数
- 若局部变量隐藏了全局变量，可以使用`::a`方式来访问全局变量
## 第10章 对象和类
- 隐式调用默认构造函数时，不要使用圆括号。如：`Stock s();`是函数声明，而不是构造函数
- 与结构体赋值一样，默认情况下，类对象赋值将会把一个对象的成员复制给另一个。
- c++11可以使用列表初始化用于类。只需要某个构造函数的参数与列表匹配。如`Stock s {1,2};`可以和Stock(int, int)匹配
- **只要类方法不修改对象，就将其声明为const**
- 创建有显式定义构造函数的类的数组
  ```c++
  Stock stocks[] = {
      Stock("1"),
      Stock("2"),
      Stock("3", 130),
      Stock(),
  }
  ```
- 在类中创建所有对象共享的常量，有两种方法（枚举和static）：
  ```c++
  class A
  {
      enum {Months1 = 12};            // 使用枚举方法
      static const int Months2 = 12;  // static方法
  }
  ```
- 传统的枚举可能会出现冲突，如：
  ```c++
  enum egg {Small, Large};   //直接 Small访问
  enum egg2 {Small, Large};  // 两个Small在同一个作用域内，此时会出现冲突
  // 可以使用类作用域
  enum class egg {Small, Large};
  enum class egg2 {Small, Large};  // 分别用egg::Small egg2::Small 来访问
  ```
- 常规枚举自动转换为整型，类的作用域枚举不能隐式的转换为整型
## 第11章 类深入
- 重载运算符。`operatorop(argument-list)`
- `A1+A2`调用的是`A1.operator+(A2)`
- 不能重载的运算符 `sizeof .  .*  ::  ?:  typeid   四个转换`
- 大多数运算符可以通过非成员函数重载，但以下几个只能通过成员函数重载。 `= () [] ->`
- 非成员函数重载的必要性：T是自己定义的类。T*double类型可以用成员函数，但double类型*T只能用非成员函数operator*(double,T)
- 创建友元函数(其访问权限与成员函数相同)：
  1. 在类中声明函数原型，在正常的函数原型前加上friend即可
  2. 编写函数定义，不要用::限定符
- 友元函数可以用于重载<<:
  ```c++
  ostream & operator<<(ostream & os, const Time& t)   // 该函数是类Time的友元
  {
      os << t.hours << "hours, " << t.minutes << " minutes";
      return os;
  }
  ```
- c++只接受一个参数的构造函数为该参数相同类型的值提供隐式转换。 如`Stone(int);` 则`Stone s=5;`相当于调用`Stone s(5);` 
- 如果给构造函数添加explicit关键字，可以关闭上述的隐式转换。即`explicit Stone(int)`。但显式转换还是可行
- 若只有原型`Stone(double);`,`Stone J = 7300;` 7300会被先转为double类型再构造。但若还有`Stone(long)`声明，则会报错，因为有二义性
- 转换函数。`operator typeName();` 
  1. 转换函数必须是类方法
  2. 转换函数不能指定返回类型
  3. 转换函数不能有参数。
  ```c++
  Stone::operator int() const
  {
      return num;
  }
  ```
- c++11可以允许关键字explicit用于转换函数。则转换必须是显式的。用于构造函数也一样.
- 将加法定义为友元可以让程序更容易适应自动类型转换
## 第12章 类和动态内存分配
- 类的静态成员变量。在类中只能做声明，在cpp文件中初始化，如`int A::num = 0;`。注意不能在头文件初始化，否则会链接错误。
- 若静态成员是const整型或枚举型，可以在类声明中初始化。
- **五种特殊的成员函数，当未定义时会自动生成**
  1. 默认构造函数，当未定义构造函数时
  2. 默认析构函数
  3. 复制构造函数
  4. 赋值运算符
  5. 地址运算符
- 新建一个对象并将其初始化为同类现有对象/函数按值传递和返回对象时，复制构造函数将被调用。以下四种(**中间两种可能会用复制构造函数直接创建，也可能先生成临时对象，然后赋值**)：
  ```c++
  StringBad ditto(motto);
  StringBad metto = motto;
  StringBad also = StringBad(motto);
  StringBad * pStringBad = new StringBad(motto);
  ```
- **若类中包含了用new初始化的指针成员，应当定义一个复制构造函数和一个赋值运算符，深拷贝**
- 类中将已有对象赋给另一个对象，将使用重载的赋值运算符。但初始化对象时，如`StringBad metto = knot;`可能使用可能不用赋值运算符。
- 类中包含用new初始化的指针成员的 赋值运算符声明应为： 
  ```c++
  StringBad & StringBad::operateor=(const StringBad & st)
  {
      if (this == &st)
          return *this;    //检查是否自己等于自己
      delete [] str;       //释放自己之前的指针所指内存
      // 下面深拷贝
      return *this;
  }
  ```
- c++98用0表示空指针，但在c++11支持用nullptr表示空指针，但0依然可以使用
- c++在重载时，区分常量和非常量函数的特征标。这是为了解决
  ```c++
  String s1("sdf");
  s1[0] = 6; //此时调用的是char & String::operator[](int i)  该数据位可以被改变
  const String s2("dsf");
  cout<<s2[0]<<endl;  //此时，因为s2为const型，无法调用上一个函数，应使用const char & String::operator[](int i) const;
  ```
- 静态成员函数。不能通过对象调用，不能用this指针，只能访问静态数据成员。
- 类的构造和析构函数，注意new和delete的兼容。即若有多个构造函数，要么都带中括号，要么都不带。
- delete无论带不带[]都可以用于空指针
- 初始化列表 必须用这种格式初始化非静态成员，初始化引用数据成员。数据成员初始化的顺序与其在类声明的顺序相同。
- c++11允许在类内初始化，这和使用初始化列表等价。
- 将复制构造函数和赋值构造函数声明为私有的。这可以避免编译器自动生成，造成不好的影响。如：
  ```c++
  class Q
  {
      private:
      Q(const Q& q) {}
      Q& operat一样or=(const Q& q) {return *this;}
  }
  ```
## 第13章 类继承
- 派生类构造函数需要利用初始化列表调用基类的构造函数, 若在初始化列表中省略基类的构造函数，将使用无参数的默认构造函数
- 基类 指针/引用 可以在不进行显示转换的情况下指向/引用派生类对象，但只能调用基类方法，不能调用派生类方法。
- 基类声明为vritual的方法，在派生类中自动成为虚方法。但建议派生类中也写明virtual
- 基类要声明虚析构函数，为了保证释放派生对象时正确释放。
- 在派生类函数需要调用基类函数时，需要使用作用域解析运算符。如`Basic::func()`
- 静态联编和动态联编。virtual是动态联编。静态联编的效率更高，因此若基类方法不需要重新定义应不使用virtual
- 编译器处理虚函数的方法：给每个对象添加一个隐藏成员保存指向一个函数地址数组的指针，叫虚函数表。基类指向一个虚函数表，派生类也指向另一个独立的虚函数表。
- 友元不能是虚函数，因为友元不是类成员
- 若在派生类中重新定义某个函数并不是重载，而是隐藏基类的同名函数，不管是否相同的特征标。如在基类中有`show(int)`函数，在派生类中`show()`函数，则基类中的该函数将被隐藏。**因此，若重新定义继承的方法，应确保和原来的原型完全相同，但若返回类型是基类引用或指针，可以改为派生类的引用或指针(这叫返回类型协变)。若基类声明被重载了，应在派生类中重新定义所有基类该声明函数。若不需要修改的话，可以在派生类中只调用基类版本。如`void Child::func() {Father::func();}`**
- c++使用纯虚函数提供未实现的函数。纯虚函数只需要在声明的结尾处=0。但纯虚函数允许有定义。
- 当派生类使用了new时,应向之前一样注意析构函数,复制构造函数和赋值运算符.
  ```c++
  // 析构函数只需要delete派生类的new即可, 基类的new会调用基类的析构函数释放
  // 复制构造函数, 使用初始化列表构造基类
  Father::Father(const Father & f) : Base(f)
  {
    // do something
  }
  // 赋值运算符.
  Father& Father::operator=(const Father & f)
  {
    if (this == &f) return *this;
    Base::operator=(f);   
    // delete 赋值操作.
  }
  ```
- 使用一个参数就可以调用的构造函数定义了从参数类型到类类型的转换.
- 构造函数, 析构函数, 赋值运算符是不能被继承的.
## 第14章 代码重用
- valarray用于处理数值,支持将数组中所有元素相加以及找到最大最小值等操作.`valarray<int> v1(8);`数组大小为8. `valarray<int> v1(10, 8);`数组大小为8, 全部初始化为10. 也可以使用初始化列表:`valarray<int> v3 = {1,2,3};`  其一些方法为: size() sum() max() min()
- 对于只有一个参数的构造函数应尽量使用explicit
- 构造函数初始化时,当一个成员的值作为另一个成员的初始化表达式的一部分时,初始化顺序非常重要
- 使用私有继承,基类的公有成员和保护成员变成私有成员. 但派生类中可以使用这些成员. 私有继承和包含类似,都是has-a关系s
- 使用多个基类的继承为多重继承, 即MI
- 包含使用对象名来调用方法,而私有继承使用类名和作用域解析运算符来调用方法, 使用强制类型转换和*this来访问基类对象 以及友元函数operator<<
- 通常使用包含来建立has-a关系, 若心类需要访问原有类的保护成员或重新定义虚函数, 则应使用私有继承
- using声明可以使派生类使用特性的基类成员函数
- 虚基类使多个基类相同的类,派生出的对象只继承一个基类对象.在类声明继承的public位置加入virtual. public和virtual顺序不重要
- 虚基类................??P558
- `template <class T, int>` 指出T为类型参数, n的类型为int. 表达式参数(即此处的int)可以是整型, 枚举, 引用或指针, 且模板代码不能修改该参数的值,也不能使用参数的地址. 实例化模板时,用作表达式参数的值必须是常量表达式
- 模板函数可以作为基类
- 默认类型模板参数 如`template <class T1, class T2=int>`
- 隐式实例化(创建类对象时), 显式实例化(`template class ArrayTP<string, 10>`), 显式具体化(`template <> class ArrayTP<string, 10>`是对特定类型的定义,替换模板中的泛型)
- 部分具体化 如对于`template <class T1, class T2> class Pair` 可以部分具体化,重新定义`template <class T1> class Pair<T1, int>`. 也可以通过指针进行部分具体化, 如对于`template <class T> Feeb` 可以使用`template <class T*> Feeb`重新定义
- 成员模板, 将模板用作参数, 模板类和友元 P586
- 模板别名, c++11允许将using=用于非模板和模板  `using pc = const char *`相当于typedef; 也可以`template<typename T> using arrtype = std::array<T,12>` 然后就可以使用`arrtype<int> days`
## 第15章 友元、异常和其他
- 友元类的所有方法可以访问原始类的私有成员和保护成员。
- 友元类声明可以位于公有、私有或保护部分，其位置无关紧要。声明如: `friend class Remote`  
- 嵌套类。声明位置确定作用域
- 异常 在头文件cstdlib中的`std::abort()`函数向标准错误流发送消息，然后终止程序，返回异常值，但是否刷新文件缓冲区取决于实现。也可以使用exit()，该函数刷新文件缓冲区，但不显示消息。
- throw的用法可以是`throw "bad xxxxx"` 执行throw语句类似于执行返回语句，结束函数执行。但throw语句会沿函数调用序列后退，知道找到包含try块的函数。 catch可以是`catch (char *s)`  s为之前throw的字符串
- 引发异常时， 编译器总是创建一个临时拷贝，即使catch中指定的是引用。而在catch中使用引用的主要原因是基类引用可以匹配派生类引用。
- `catch (...)`捕获任何异常
- 在函数声明后面加关键字`noexcept`表示该函数不会引发异常有助于编译器优化代码。若函数依旧抛出异常，则该异常不会被抛出函数，而是直接终止程序。
- `<exception>`头文件定义了exception类，有一个名为what()的虚成员函数返回字符串。一般由exception作为基类得到的派生类，都有一个以字符串为参数的构造函数，该字符串为what()方法返回的字符串
- 主要的logic_error派生的类型有domain_error, invalid_argument, length_error, out_of_bounds  一般都是编程问题
- 主要的runtime_error派生的类型有range_error, overflow_error, underflow_error
- c++对于new失败最新的处理方式是让new引发bad_alloc异常，以前是返回一个空指针。为了适应以前根据new失败返回空指针编写的代码，c++提供了`int * pi = new(std::nothrow) int[500]`来表示失败时返回空指针的new
- 对于未捕获的异常，程序不会立刻终止。首先程序会调用函数`terminate()`，默认情况下`terminate()`调用`abort()`函数，但我们可以指定terminate()应调用的函数。首先自定义一个没有参数和返回值为空的函数如`void myQuit()`，然后通过调用`set_terminate(myQuit)`即可。
- 若函数引发其异常规范中没有的异常，程序将调用`unexpected()`函数，该函数默认调用`terminate()` 类似于`set_terminate()`，也有`set_unexpected()`
- RTTI只适用于包含虚函数的类。c++中3个支持RTTI的元素为`dynamic_cast`, `typeid`, `type_info`
- dynamic_cast 不能回答“指针指向的是哪类对象”的问题，但能回答“是否可以安全的将对象地址赋给特定类型的指针”。如A是B的基类，B是C的基类，则`A* pa = new C; B* = dynamic_cast<B*>(pa);` 若pa能安全转换成B*类型，则返回对象地址，否则返回一个空指针。
- dynamic_cast也可以用于引用，同理于指针。但因为没有与空指针对应的引用值，所以当请求不正确时，引发bad_cast异常
- typeid运算符可以确定两个对象是否为同种类型。与sizeof类似，可以接受类名和对象两种参数。用法为`typeid(class)` 返回一个type_info对象的引用，该对象类型在头文件`<typeinfo>`中，且重载了==和!=运算符。所以可以用`typeid(String) == typeid(*sptr)`来比较是否为同一类型。其中type_info类一定包含一个name()成员，通常是类的名称。
- const_cast只用于改变值为const或volatile 其语法于dynamic_cast相同。如：`Hight bar; const High * pbar = &bar; High * pb = const_cast<High *> (pbar);`  只能改变是否为const，类型的转换是错误的。但const_cast不是万能的，可以修改指向一个值的指针，但修改const值的结果是不确定的。
- static_cast 语法与别的一样，为`static_cast <type-name> (expression)` 仅当type_name可被隐式转换为expression，或expression类型可以被隐式转换为type_name时才合法。
- reinterpres_cast 语法和别的一样。 不允许删除const。可以将指针类型转换成足以存储指针表示的整型，但不能将指针转换为更小的整数或浮点型。也不能将函数指针转换为数据指针。
## 第16章 string类和STL
- string类构造函数。 `string(const char* s, size_type n)`将string对象初始化为s指向的前n个字符。 `string((Iter begin, Iter end)`将string对象初始化为[begin,end)内的字符。`string(const string & str, string size_type pos = 0, size_type n = npos)`将string对象初始化为从pos开始的n个字符，string类将string::npos定义为字符串的最大长度。`string(string && str) noexcept`是移动构造函数，类似于复制构造函数，但与复制构造函数的区别是不保证将str视为const，有些情况下编译器用它替代复制构造函数来优化性能。 `string(initializer_list<char> il)`使列表初始化可以用于string类，即`string s = {'L', 'i'};`是合法的。
- 对于char数组info，`cin>>info`读一个单词，`cin.getline(info, 100)`读一行且忽视'\n'，`cin.get(info, 100)`读一行且保留'\n'
- 对于string类型struff，`cin>>stuff`读一个单词。 `getline(cin, stuff)`读一行忽视'\n' 
- 两个版本的getline都由一个可选参数，用于指定使用哪个字符来确定输入边界。
- string类的find()函数，若未找到返回std::npos。如`int pos = s.find(sub, size_type pos=0);`; `size_type find(const char* s, size_type pos = 0, size_type n);`查找字符串的前n个字符中是否由s
- 另外，还有一些与find()用法相同的函数
  1. rfind()找字符串最后一次出现的位置。 
  2. find_first_of() find_last_of() 
  3. find_first_not_of()查找第一个不包含在参数中的字符 find_last_not_of()查找最后一个不包含在参数中的字符
- string中使用capacity()返回当前分配给字符串的内存块的大小，reserve()方法请求内存块的最小长度
- 三种智能指针模板 auto_ptr, unique_ptr, shared_ptr. 其中auto_ptr是c++98提供的方案，c++11将其摒弃，提供了后两种方案。三种方案都需要头文件`<memory>`
- auto_ptr用法 `auto_ptr<double> ap(new double)` 另外两种方案用法类似
- 智能指针应该避免的一点是程序将delete运算符用于非堆内存。如:`string s("fds"); shared_ptr<string> sp(&s)` 这是错误的
- 为了避免程序同时删除同一个对象两次。auto_ptr和unique_ptr建立所有权概念，即对于特定对象，只能有一个智能指针可以拥有它，**赋值操作会转让所有权**；shared_ptr跟踪对象的智能指针数。赋值时计数加1， 指针过期时计数减1， 最后一个指针过期时才调用delete
- unique_ptr优于auto_ptr的原因是其在编译阶段就会出错，指出将p1赋值给p2时， p1不再指向有效数据的问题。但当p1是临时右值时(如p1是某个函数返回的unique_ptr)，编译器允许这么做。使用函数std::move()可以将一个unique_ptre赋给另一个，如`p2 = move(p1)`
- auto_ptr, share_ptr不能用于数组，因为其使用delete， 而unique有new[]和delete[]版本，所以允许使用`unique_ptr<double []> pda(new double[5])`
- 程序要使用多个指向同一个对象指针时，应用shared_ptr, STL容器中一般都使用shared_ptr, 因为STL算法常常有复制和赋值操作； 若程序不需要多个指向同一个对象的指针，可以使用unique_ptr， 特别是函数用new分配内存，并返回指向该内存的指针时建议用unique_ptr
- shared_ptr包含一个显示构造函数，可以将**右值**unique_ptr转换为shared_ptr
- 所有STL容器都提供的基本方法包括：size(), swap(), begin(), end()
- vector的函数有：
  1. push_back()
  2. erase()
  3. insert()
- 3个具有代表性的STL函数：for_each(), random_shuffle(), sort()  
  for_each接受3个参数，前两个为迭代器表示范围，最后一个是函数对象。**for_each所指向的函数不能修改容器元素的值，即使你在函数内部修改了**   
  random_shuffle接受两个迭代器参数，并随机排列该区间中的元素。**该函数要求容器允许随机访问，如vector**  
  sort函数也**要求容器类允许随机访问**，前两个参数为迭代器参数，最后一个默认为容器存储类型元素定义的<运算符，即默认升序排序。可以自定义函数，函数的参数为两个存储类型元素，返回bool类型。
- 与for_each类似的为基于范围的for循环`for (auto book : books) func(book);` 但区别是，基于范围的for循环可以改变容器的内容。
- 五种迭代器
  1. 输入迭代器：可以读取容器的值，但不一定能让程序修改值。需要输入迭代器的算法不会修改容器中的值。输入迭代器支持++运算符。但不能保证每次遍历的顺序相同，基于输入迭代器的算法应当是**单通行**的，不依赖于遍历中前面的迭代器值
  2. 输出迭代器：用++运算符遍历容器。可以解除引用让程序可以修改容器值，但不能读取。同样是**单通行**的
  3. 正向迭代器：只用++运算符遍历容器。但按相同顺序遍历容器，可以对前面的迭代器值解除引用，即**多通行**
  4. 双向迭代器：具有正向迭代器的所有特性，支持++和--
  5. 随机访问迭代器：有些要求可以直接跳到容器中任意元素。具有双向迭代器的所有特性，并且添加了支持随机访问的操作。
- copy()可以将数据从一个容器复制到另一个容器中，该算法以迭代器方式实现，甚至可以在数组之间复制。如：`int casts[] = {1,2,3,4}; vector<int> dice(10); copy(casts, casts+4, dice.begin());` 前两个迭代器参数表示复制的范围，最后一个迭代器参数表示将第一个元素复制到什么位置。**copy要求目标容器必须足够大**
- 迭代器深入:预定义迭代器 Ｐ690-695
- 容器中的类型必须是**可复制构造的和可赋值**的。即类定义没有将复制构造函数和赋值运算符声明为私有或保护的。
- Ｘ表示容器类型，则`X::valu_type`为存储在容器中的对象类型。ａ为容器对象，`(&a)->~X()`对容器中每个元素使用析构函数。
- 序列容器包括deque, forward_list, list, queue, priority_queue, stack, vector  
  序列要求迭代器至少是正向迭代器，保证元素按照特定顺序排列。因为有确定的顺秀，可以执行insert erase等操作  
  还有`T& front();T& back(); push_front() push_back() pop_front() pop_back() a[n] a.at(n)`   
  a[n] 和 a.at(n)的区别是：若n落在容器的有效区间外，则at将执行边界检查，并抛出out_of_range异常。
- vector 在头部和中间插入和删除元素的复杂度为线性时间
  1. vector是可反转容器概念的模型。rbegin()和rend()分别指向反转序列的第一个元素和超尾迭代器。如`for_each(vec.rbegin(), vec.rend(), Show);` 将反向显示。这两种方法返回的迭代器都是reverse_iterator类型，对该迭代器进行递增，将反向遍历可反转容器。
- deque 表示双端队列。实现类似于vector，支持随机访问，但其在开始位置插入和删除元素的时间固定。vector容器在执行随机访问以及在序列中部执行插入和删除操作更快一些
- list 表示双向链表。list强调的是元素的快速插入和删除。
  1. `void merge(list<T,Alloc>& x)` 将链表x与调用链表合并。要求两个链表必须已经排序，合并后经过排序的链表保存在调用链表中，x为空。
  2. `void remove(const T& val);` 从链表中删除val的所有实例
  3. `void srot` 使用<运算符对链表进行排序，复杂度为NlogN
  4. `void splice(iterator pos, list<T,Alloc> x)` 将链表x内容插入到pos的前面，x将为空
  5. `void unique()` 将连续的相同元素压缩为单个元素。
  6. sort(), merge(), unique()方法可以自定义用来比较元素的函数。另外algorithm的sort函数不能用于链表，因为它需要随机访问迭代器
- forward_list 表示单链表，因此只有正向迭代器，不可反转。
- queue是一个适配器类，让底层类(默认为deque)展示典型的队列接口。不允许随机访问队列元素，不允许遍历队列。`front() back() push() pop()`
- priority_queue是另一个适配器类，默认的底层类是vector。默认最大的元素被移到队首。其构造函数为：`priority_queue<int> pq1; priority_queue<int> pq2(greater<int>)`
- stack也是一个适配器类，其默认底层类是vector。`top() push() top()`
- array 长度固定的，很多标准STL用于array对象，如copy() for_each()
- `X::value_type` 指出存储在容器中的值类型， `X::key_type`指出了键的类型。
- 关联容器通常用某种树实现的，关联容器允许插入新元素，但不能指定元素的位置。4种关联容器：set multiset map multimap
- 最简单的关联容器set，其值与键相同，值就是键。multiset类似于set，但其可能有多个值的键相同。map中键是唯一的，multimap一个键可以与多个值关联。
- `set<stringm less<string> >`第二个参数是可选的，用来对键排序。set也有将迭代器区间作为参数的构造函数。STL也为集合提供了数学的标准操作。这些操作是通用函数，不是属于set的方法。如：
  1. `set_union(A.begin(), A.end(), B.begin(), B.end(), ostream_iterator<string, char> out(cout, " "))`或者`set_union(A.begin(), A.end(), B.begin(), B.end(), insert_iterator<set<string> >(C, C.begin())` 
  2. `set_intersection()` `set_difference()`用法类似
  3. lower_bound() upper_bound()都将键作为参数并返回一个迭代器，该迭代器指向集合中第一个`不小于`/`大于`键参数的成员
- multimap 也是可反转的经过排序的关联容器，且同一个键可能与多个值相关联。实际的值类型为`pair<class T, class V>` multimap的函数有：count(), lower_bound() upper_bound(), 成员函数equal_range()用键作为参数，返回一个pair对象(保存两个迭代器，它们表示的区间与该键匹配)
- 关联容器是基于树结构，无序关联容器基于哈希表。如unordered_set unordered_multiset unordered_map unordered_multimap
- 函数对象也叫函数符，指可以以函数方式与()结合使用的任意对象。包括函数名，函数指针以及重载了()运算符的类对象
- STL定义了函数符的概念。生成器(不需要参数的函数符)、一元函数、二元函数。返回bool值的一元函数是谓词，返回bool的二元函数是二元谓词
- 若有一个接受两个参数的模板函数，则可以用类将它转换单个参数的函数对象。如；
  ```c++
  template<class T>
  bool tooBig(const T& val, const T& limit)
  {
    return val > lim;
  }
  // 可以将其转换为单个参数的函数对象
  template<class T>
  class TooBig2
  {
    private:
      T cutoff;
    public:
      TooBig2(const T& t) : cutoff(t) {}
      bool operator()(const T& v) {return tooBig<T>(v, cutoff);}
  }
  ```
- **对于所有的算术运算符、关系运算符、逻辑运算符，STL都提供了等价的函数符** 见表P711
- binder1st 和 bind1st
- 可以用==来比较不同类型的容器，如deque和vector。可以这么做的原因是容器重载的==运算符使用迭代器来比较，所以若两者排列顺序相同，内容也相同，则是相等的。
- STL算法库有4组：
  1. 非修改式序列操作 如find() for_each
  2. 修改式序列操作 可以修改值，也可以修改排列顺序 如transform() random_shuffle() copy()
  3. 排序和相关操作 多个排序函数和其他函数，包括集合操作
  4. 通用数字运算 将区间的内容累积，计算两个容器的内部乘积等
- 有些算法就地完成工作，如sort()，被称作就地算法；有些算法将结果发送到另一个位置，是复制算符。有些算法由两个版本：就地和复制版本。STL约定复制版本的名称以_copy结尾，并接收一个额外的输出迭代器参数指定结果的存放位置。
- 对于复制算法，统一的约定：**返回一个迭代器，指向复制的最后一个值后面的一个位置**
- string类虽然不是STL的组成部分，但设计它时考虑到了STL，它包含begin() end() rbegin() rend(), 因此可以使用STL接口。next_permutation()算法将区间内容转换为下一种排列方式，对于字符串，排列按照字母递增顺序进行，若成功返回true，若区间已经处于最后的序列中，返回false。要得到区间内容的所有排列组合，应从最初的顺序开始，即先调用sort()
- 有时候可以使用STL方法或函数，通常方法是更好的选择，它更适合特定的容器，可以使用模板类的内存管理工具。如`list<int> la;` 若调用remove方法，`la.remove(4)；`则链表值为4的元素被删除且链表长度被自动调整；若调用remove函数，`remove(la.begin(), la.end(), 4);` 由于remove()函数不是成员，所以不能调整链表长度，它将未被删除的元素放在链表开始位置，并返回一个新的指向超尾值的迭代器，这样可以用erase来修改容器长度。
- `<complex>`为复数提供了类模板。
- vector, valarray, array的不同。vector是容器类和算法系统一部分，支持面向容器的操作。valarray是面向数值计算的，不是STL的一部分。array是为内置数组设计的，提供更好更安全的借口，让数组更紧凑，效率更高。
- array表示长度固定的数组，因此不支持push_back() insert()，但提供多个STL方法，包括begin(), end(), rbegin(), rend()
- valarray类重载了所有算术运算符，所以对于两个数组中每个值的和赋值给第三个数组，并将第三个数组扩大3倍 则有:
  ```c++
  valarray<double> vad1(10), vad2(10), vad3(10);
  vad3 = vad1+vad2;
  vad3 = 3 * vad3;
  // 对于普通的vector类型
  vector<double vc1(10), vc2(10), vc3(10);
  transform(vc1.begin(), vc1.end(), vc2.begin(), vc3.begin(), plus<double>());
  // valarray还可以完成
  vad3 = log(vad1);
  vad3 = vad1.apply(log); // 两种方法都是取对数
  // 方法apply()不修改调用对象，而是返回一个包含结果的新对象
  // 甚至可以：
  vad3 = 10.0 * ((vad1+vad2) / 2.0 + vad1*cos(vad2));
  ```
- valarray还提供了sum(), size(), max(), min(), 但valarray通用性更低，虽然它有一个resize()方法，但它不能像使用vector的push_back()那样自动调整大小。valarray没有begin() end()方法，因此不能将它们用作指定区间的参数。另外，vad是一个对象，不是指针，所以类似`sort(vad1, vad1+10);`不可行。为解决这个问题，c++11提供了以valarray对象为参数的模板函数begin(), end()，即可以使用`sort(begin(vad1), end(vad1));`
- valarray还有很多特性，如`valarray<double> numbers; valarray<bool> vbool = numbers > 9;`其中vbbol[i]为numbers[i]>9的true或false值
- slice类对象可用作数组索引。在这种情况下，它表示的是一组值。`slice(1,4,3)`表示`1,4,7,10` 第一个参数为起始索引，第二个为索引数，第三个为跨距，则有`valarray<double> varint; varint[slice(1,4,3)] = 10;`把这4个元素都设置为10
- initializer_list是c++11新增的。`vector<double> pay {1,2,3,4}`可行是因为容器类包含将`initializer_list<T>`作为参数的构造函数。上述声明与下面等价：`vector<double> pay({1,2,3,4})`
- c++11新增的通用初始化语法，**可使用表示法{}而不是()来调用类构造函数**，如：
  ```c++
  class A
  {
    int x,y,z;
    public:
    A(int xx=0, int yy=0, int zz=0) : x(xx), y(yy), z(zz) {}
  }
  A a {20,3}; // 此时相当于调用A(20,3,0)
  // 但若A提供了接受initializer_list作为参数的构造函数，则调用A({20,3})
  ```
- 若要使用initializer_list，必须包含头文件initializer_list。该模板类包含begin() end() size()
## 第17章 输入、输出和文件
- 包含`<iostream>`会自动创建8个流对象，4个对应窄字符流，4个对应宽字符流。都是凭借streambuf对象的帮助
  1. cin wcin
  2. cout wcout
  3. cerr wcerr 这个流没有被缓冲，信息将被直接发送给屏幕
  4. clog wclog 也对应着标准错误流
- 如果要获得字符数组的地址，可以强制转换成`void*` 用cout打印地址
- ostream类还提供put() write() 分别显示字符和字符串，与`<<`一样 也返回一个指向调用对象的引用。如：`cout.put('a'); cout.write(ch, 4);` write第一个参数为字符串地址，第二个参数为输出字符串长度，且不会在遇到空字符串时自动停止打印字符，只是打印指定数目的字符，即使超出边界。
- 刷新cout缓冲区的两种方式。`cout<<flush; flush(cout)`
- 浮点类型被显示为6位，末尾的0不显示，当指数大于等于6或小于等于-5时，将用科学计数法表示
- 使用cout格式化：
  1. dec, hex, oct 十进制、十六进制、八进制。 用法两种，如：`hex(cout); cout<<hex;`
  2. `cout.width()`返回字段宽度的当前设置；`cout.width(int i)`将字段宽度设置为i个空格，但只影响下一次输出，输出之后又恢复为默认值
  3. `cout.fill('*')`为修改填充字符。默认为空格，填充字符一直有效
  4. c++的默认精度为6位。`cout.precision(2)`设置cout的精度为2，新的精度设置一直有效。精度指的是有效数字
  5. `cout.setf(ios_base::showpoint)`打印末尾的0和小数点  `ios_base::boolalpha`输入输出的bool值显示为true或false; `ios_base::showbase`使用c++基数前缀(0,0x) `ios_base::uppercase`对于16进制输出使用大写字母 `ios_base::showpos`正数加上+
- 可以hex、oct、dec与cin一起使用。cin
- 


