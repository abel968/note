# wxWidgets学习笔记

- Windows 10 install wxWidgets:
  1. 官网上下载最新的源代码压缩包，并解压缩到`${InstallDirectory}`
  2. 使用vs2017打开`${InstallDirectory}/build/msw/wx_vc15.sln`
  3. 右键解决方案，选择“重定解决方案目标”，使用Windows SDK版本10.xxxx
  4. 菜单栏中选择生成->批生成，选择各个模块的Debug和Release版本 生成
  5. 最后生成的静态库存在`${InstallDirectory}/lib/vc_x64_lib/`，动态库存在`${InstallDirectory}/lib/vc_x64_dll/`，debug版本的库名最后有个d

- Mint install wxWidgets:(非最新版)
  1. sudo apt install libwxbase3.0-dev
  2. sudo apt install libwxgtk3.0-dev
  3. 在CMakeLists.txt中加入：
     ```cmake
     find_package(wxWidgets REQUIRED COMPONENTS net core base)
     include(${wxWidgets_USE_FILE})
     add_executable(myapp myapp.cpp)
     target_link_libraries(myapp ${wxWidgets_LIBRARIES})
     ```

## 开始使用
### App类
- 每个wxWidgets程序需要定义一个wxApp的子类，并且要创建且只能创建一个这个类的实例。这个类至少要定义一个OnInit函数，运行时将会调用这个函数。
- Oninit函数将会创建至少一个窗口实例，并进行初始化，若函数返回真wxWidgets将开始事件循环接收用户输入并处理。若返回假wxWidgets将释放内部分配的资源，结束程序运行。
- 宏wxT作用是兼容Unicode模式。如：`wxT("Minimal wxWidgets App")``
- 创建MyApp实例是在wxWidgets内部实现。需要告诉wxWidgets创建哪一个App实例。如：`IMPLEMENT_APP(MyApp)` 这个宏还会检查编译应用程序使用的库文件是否与当前使用库文件匹配。
- 创建App实例后，创建的结果会赋值给全局变量wxTheApp。但使用时需要wxApp到子类MyApp的强制类型转换。增加声明`DECLARE_APP(MyApp)`之后，可以通过调用wxGetApp()函数，返回该MyApp的引用。
### Frame窗口类
- 自定义的Frame窗口类继承wxFrame。一个Frame窗口是可以容纳别的窗口的顶层窗口，通常拥有一个标题栏和一个菜单栏。 
### 事件处理函数
- 事件表，是一组位于cpp文件里的宏。告诉wxWidgets来自用户的事件如何与类的成员函数对应。如：
  ```c++
  BEGIN_EVENT_TABLE(MyFrame, wxFrame)
    EVT_MEMU(wxID_ABOUT, MyFrame::OnAbout)
    EVT_MEMU(wxID_EXIT, MyFrame::OnQuit)
  END_EVENT_TABLE()
  ```
  该事件表将标识符wxID_ABOUT和wxID_EXIT的菜单事件与MyFrame的成员函数OnAbout和OnQuit关联。EVT_MENU宏是多个事件宏的一个，事件宏的作用是告诉wxWidgets哪种事件应该被关联到哪个成员函数。此处的两个标识符是wxWidgets预定义的宏，通常程序员应该通过枚举、常量、宏定义的方式定义自己的标识符。
## 事件处理
每个wxEvtHandle的派生类，如frame，按钮，菜单以及文档等，都会在其内部维护一个事件表。所有继承自wxWindow的窗口类，以及应用程序类都是wxEvtHandle的派生类。
### 静态事件
- 创建一个静态的事件表，需要以下几个步骤：
  1. 定义继承wxEvtHandle的类
  2. 在该类中为每一个你想要处理的事件定义一个处理函数,返回值都为void，只有一个事件对象作为参数，且不是虚函数。
  3. 在该类中使用宏DECLARE_EVENT_TABLE声明事件表
  4. 在.cpp文件中使用BEGIN_EVENT_TALBE和END_EVENT_TABLE实现一个事件表
  5. 在事件表的实现中增加事件宏，来实现事件到处理函数之间的映射
- 只有Command事件才会被递归到其父窗口的事件表。即当在事件源控件及其父类和祖先类的事件表中未找到对应的处理函数，就去其父窗口类及其父类和祖先类的事件表中找。
- 过滤某个事件。通过重载某个基类的事件表的方法来改变该基类的默认事件处理过程。如若要自己的文本框只接受字母的按键，而忽略其他按键。需要先定义一个类myTextCtrl继承wxTextCtrl，并在其事件表中使用EVT_KEY_DOWN事件映射宏。wxEvent::Skip函数提示事件处理过程中应该继续寻找其父类的事件表。如：
  ```C++
  void MyTextCtrl::OnChar(wxKeyEvent& event)
  {
      if (wxIsalpha(event.KeyCode()))
      {
          event.Skip();    //这些按键在可接受范围内，按照正常流程处理
      }
      else
      {
          // 没有调用Skip()函数，认为事件已经处理完成。
          wxBell();
      }
  }
  ```
- 挂载事件表  
  对于继承自wxWindow的类来说，可以先实现一个新类直接继承wxEvtHandler，然后定义该类的事件表，并使用wxWindow::PushEventHandler函数将该事件表压入窗口类的事件表栈中。最后被压入的那个事件表将会被最先匹配。还可以用wxWindow::PopEventHandler函数来弹出最顶层的事件表，若给PopEventHandler函数传递的是true参数，则被弹出的事件表会被删除。  
  有时候，需要手动调用窗口类事件表。应使用wxWindow::GetEventHandler方法，而不是调用该窗口类的成员函数，若你之前push过别的事件表，该函数将会返回处于最顶层的事件表。
### 动态事件
与动态事件表相关的函数有wxEvtHandler::Connect和wxEvtHandler::Disconnect。大多数时候不需要手动调用Disconnect函数，该函数会在窗口类被释放时自动被调用。  
Connect()函数的三个参数分别为菜单标识符、事件标识符、事件处理函数指针。Disconnect函数用法相同。如：
```C++
frame->Connect(wxID_EXIT, wxEVT_COMMAND_MENU_SELECTED, wxCommandEventHandler(MyFrame::OnQuit));
```
一般来说，事件处理函数的参数类型是wxXYZEvent，其处理函数指针应用wxXYZEventHandler宏进行强制类型转换。
### 窗口标识符
窗口标识符是在事件系统中用来唯一确定窗口的整数。在整个应用程序范围内窗口标识符不一定是唯一的，只要在某个固定上下文内是唯一即可。比如在一个frame窗口和它所有子窗口中是唯一的。若在窗口的构造函数中使用wxID_ANY作为标识符，意味着由wxWidgets自动生成该标识符。使用wxID_ANY标识符时，若你需要在同一个地方处理所有事件，则在Connect函数或者静态事件表中，使用wxID_ANY作为窗口标识符。wxID_ANY自动生成的标识符为负，用户定义的窗口标识符只能为正。  
下面是一些标准标识符。应该尽可能使用，因为某些系统会为特定标识符提供特定图片，或者默认的处理函数。
| 标识符 | 描述 |
|--------|--------|
|wxID_ANY||
|wxID_LOWEST| 最小的系统标识符(4999) |
|wxID_HIGHEST| 最大的系统标识符(5999) |
表格在`P59`中，为了防止自定义的标识符与系统标识符冲突，应使用小于wxID_LOWEST和大于wxID_HIGHEST的标识符。
### 自定义事件
- 自定义事件需要以下步骤：
  1. 从一个合适的事件类派生你的事件类，声明动态类型信息且实现一个Clone函数。若希望该事件在窗口继承关系之间传递，应使用wxCommandEvent派生类。
  2. 为该事件类的处理函数定义类型。
  3. 定义你的事件类支持的事件类型表。该表应该在你的头文件中，用BEGIN_DECLARE_EVENT_TYPES()和END_DECLARE_EVENT_TYPES()宏包含起来。其中的每个支持的事件的声明应使用DECLARE_EVENT_TABLE(name, interger)格式的宏。然后再你的.cpp文件中使用DEFINE_EVENT_TYPE(name)来实现该类。
  4. 为每个你的事件类支持的事件定义一个事件映射宏
- 例子见P66
### 小结
参考原书附录H, "wxWidgets怎么处理事件"。附录I "事件处理类和宏"。以及samples/event的例子。源码在include/wx/event.h
## 窗口的基础知识
### 窗口解析
- 窗口的大小通常是指整个的大小，包括修饰的边框和标题栏等；窗口的**客户区**大小指的是窗口内那些能被绘制或者它的子窗口能被放置的区域大小，不包括菜单栏、状态栏、工具栏等。
- 大多数窗口有**滚动条**。这些滚动条是通常是窗口自己增加而不是应用程序手动增加。为了优化性能，只有拥有wxHSCROLL和wxVSCROLL类型的窗口才会自动生成它们的滚动条。
- 一个窗口可以拥有一个**光标wxCaret**和一个**鼠标指针wxCursor**。
- 窗口分为像**wxFrame,wxDialog,wxPopup这样的**顶层窗口**和其它窗口。只有顶层窗口创建时可以使用NULL作为父窗口，也只有顶层窗口是**延时删除**（即所有的事件处理完后才会被删除）。除了wxPopup窗口以外，顶层窗口有一个标题栏和关闭按钮，只要应用程序允许，可以被拽着满屏幕跑或者改变大小。
- 当窗口需要**重绘**时，会收到两个事件，wxEVT_ERASE_BACKGROUND事件用于通知应用程序重绘背景，wxEVT_PAINT通知重绘前景。
- 每个窗口都有一个**前景色和背景色**。默认的背景插除函数会使用背景色来擦除背景。每个窗口也有一个字体设置。
- **改变大小**会收到一个wxEVT_SIZE事件。当某个窗口大小被改变时，其子窗口需要被重新放置和计算大小，推荐处理这种情况使用sizer类。
- 任何窗口都可以接收鼠标事件，**除非限制了鼠标区域或者被禁用**。对于键盘，只有当前处于活动的窗口才能接收，应用程序可以自己设置为活动状态，或者用户点击某个窗口设置为活动状态。**正变成活动状态**的窗口会收到wxEVT_SET_FOCUS事件，**正失去焦点**的窗口会收到wxEVT_KILL_FOCUS事件。
- 所有窗口都会收到**空闲事件wxEVT_IDLE**,当所有其他事件都被处理完之后。其中一个特殊的**空闲时间操作是进行用户界面更新**，所有的窗口定义一个函数来更新自己的状态，该函数会周期性的在系统空闲时被调用。
- 窗口的创建和删除。一般来说，窗口是用new方法创建的。除了frame和dialog窗口，别的窗口都需要在构造函数中传入一个非空的父窗口。
  ```C++
  // 一步创建法
  wxButton* button = new wxButton(parent,wxID_OK);
  // 两步创建法
  wxButton* button = new wxButton;
  button->Create(parent, wxIDOK);
  ```
  使用两步创建法：窗口在调用Create函数会收到wxEVT_CREATE事件，可以对该事件做进一步处理。
  使用两步创建的原因是：
    1. 有时想在晚些时候才完整的创建窗口。
    2. 希望在调用Create函数之前设置窗口某些属性值，尤其是这些属性之将被Create函数使用的时候
  **窗口的可见性：** 若创建的窗口的父窗口是可见的，那它也总是可见的，可以通过`Show(false)`使它不可见。而wxDialog和wxFrame这样的顶层窗口，在创建时通常是不可见的，这样可以避免在绘制那些子窗口和排列子控件的时候发生闪烁。需要通过Show或者ShowModel的调用让它可见。
  **窗口的删除：** 窗口调用Destroy函数（对于顶层窗口）或者delete函数（对于其子窗口）来释放的。在窗口刚刚要被释放之前调用wxEVT_DESTROY事件。子窗口是被自动释放的，所有delete函数是很少调用的。
- 窗口类型：设置窗口类型是设置窗口创建时的行为和外观的一种简介的方法。
### 窗口类概览
## 绘画和打印







