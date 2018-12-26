
4. 确定对象初始化
   - 对内置型对象进行手工初始化, 因为c++不保证初始化它们.
   - 为避免跨编译单元初始化次序的问题, 用local static对象替换non-local static对象, 即单例模式
   - 单例模式. 函数内使用static构造对象, 然后返回该对象的引用. 
   - 但是, 该函数内含static对象, 这样在多线程系统中带有不确定性, 一种解决办法是在程序的单线程启动阶段手工调用所有这样的函数.

5. 了解c++默默编写并调用哪些函数
   - 若写下`class Empty {};` 相当与写下了:  
    ```c++
      class Empty
      {
          public:
          Empty() {...}  // 若声明了一个构造函数, 则该default构造函数不再创建
          Empty(const Empty& rhs) {...}
          ~Empty() {...}
          Empty& operator=(const Empty& rhs) {...}
      }
    ```
   - 当class内有一个成员为reference 或者 const时, 编译器拒绝生成copy assignment函数, 因为编译器不知道该怎么做. 若一个基类的copy assignment函数被声明为private, 其派生类也不生成copy assignment函数, 因为派生类无权调用基类的该函数.
   - Uncopyable基类, 其内部将两个copy声明为私有的, 则该类的派生类不再自动声明两个copy函数. (因为编译器试着生成的copy函数会尝试调用其base class的对应函数, 但该调用被编译器拒绝, 所以不会自动生成)
6. 若不想使用编译器自动生成的函数, 就应该明确拒绝  
   - 若不想使用copy构造函数和copy assignment操作符, 应使它们成为private. 但为了防止member函数和friend函数调用, 应该只是声明它们, 而不定义. 这样在调用时会产生一个连接错误.
   - 可以声明一个Uncopyable类, 其两个copy函数都是private的, 然后继承该类的派生类都不会自动生成两个copy函数.
7. 多态基类声明virtual析构函数
   - 如果class不含virtual函数, 通常表示它并不意图被用作一个base class, 此时令其析构函数为virtual是一个馊主意
   - 所有STL容器都是不带virtual析构函数的class, 所以不应该继承他们.
   - 即使有的类被作为base class, 但若其目的不是为了多态, 也不该使用virtual析构函数. 如条例5中的Uncopyable
8. 别让异常逃离析构函数
   - 即别在析构函数中抛出异常. 如果一个被析构函数调用的函数可能抛出异常, 析构函数应该捕捉任何异常, 然后吞下它们(不传播)或结束程序(std::abort())
9. 绝不在构造和析构过程中调用virtual函数 
   - 派生类调用构造或析构函数时, 其自动调用的基类构造或析构函数内部调用的virtual函数为基类的对应函数, 因此在构造和析构期间不要调用virtual函数,其调用的普通函数中也应该不含virtual函数.
10. 令operator= 返回一个reference to *this
    - 主要用于赋值连锁形式, 如`x=y=z=5` 这是因为赋值采用右结合定律
    - 所有赋值相关的运算都应该这样, 如+= , -=
11. 在operator= 中处理"自我赋值"
    - 有三种技术(主要针对类内的指针成员)
      1. 判断this和当前赋值对象的地址是否相等
      2. 使用一个old指针记录当前指针成员指向的地址, 指针成员指向new出的新地址, delete old指针
      3. copy and swap. 即先用copy构造函数创建一个新对象, 然后将新对象与this数据交换, 函数结束时新对象会自动析构.
12. 复制对象时勿忘其每一个成分
    - 派生类的copying函数不会自动调用基类的coying函数. 所以对于copy构造函数,应使用初始化列表的方式调用基类copy构造函数; copy赋值函数, 使用`Base::operator=(rhs)`调用

13. 以对象管理资源
    - 获得工厂模式的资源后立刻放进管理对象, 如std::auto_ptr `std::auto_ptr<Investment> pInv(createInvestment()); `
    - 需要注意的是别让多个auto_ptr指向同一对象, 否则对象会被删除一次以上.同时, auto_ptr有一个预防该情况的实现, 即其copying函数会将源操作数的指针指向nullptr, 目的操作数指针指向之前源操作数所指内容.
    - auto_ptr shared_ptr都在析构函数内做delete而不是delete[]  所以不要在动态分配的array上使用它们
14. 在资源管理类中小心coping行为
    - 并不是所有的资源都是heap-based的, 所以此时智能指针可能不适合做资源管理者. 此时应该自己定义一个类, 如锁, 可以定义一个类(构造函数是lock(mutex), 析构函数是unlock(mutex))
    - 对于一个RAII对象被复制时, 可以(前两种为主要方法):
      1. 禁止复制
      2. 对底层资源使用引用计数法  (shared_ptr可以允许指定删除器, 如若指向一个mutex指针, 可以使shared_ptr的析构函数不是使用delete, 而是调用该指针的unlock)
      3. 复制底部资源
      4. 转移底部资源的拥有权, 类似auto_ptr
15. 在资源管理类中提供对原始资源的访问
    - 对原始资源访可以使用显示转换(自定义成员函数get())或者隐式转换(`operator FontHandle()`). 显示转换比较安全, 隐式转换对用户更方便
16. 成对使用new 和 delete时要采用相同形式
    - 尽量不要对数组形式做typedef动作. 否则可能new了该数组之后, delete忘记加[]
17. 以独立语句将newed对象置入智能指针
    - 用独立语句将new对象存储于智能指针内. 否则, 如:
      ```c++
        processWidget(std::shared_ptr<Widget>(new Widget), priority());
        // 因为在c++函数的参数计算是不确定的, 即可以先new Widget, 然后调用priority() 若priority()出异常, 则上一步new的Widget将没有被delete, 则内存泄漏
        // 所以应该使用
        std::shared_ptr<Widget> pw(new Widget);
        processWidget(pw, priority());
      ```

18. 让接口容易被正确使用, 不易被误用
    - 如将工厂模式函数的返回值变成智能指针, 防止用户忘记使用智能指针
    - 
    