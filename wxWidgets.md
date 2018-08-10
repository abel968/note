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
- 关于头文件
  ```c++
  #include <wx/wxprec.h>

  #ifndef WX_PRECOMP
    #include <wx/wx.h>
  #endif
  ```
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
### 基础窗口类
- wxWindow 
  wxWindow类一定要有一个非空父窗口，否则运行时会有断言错误
  ```c++
  wxWindow(wxWindow* parent,
  wxWindowID id,
  const wxPoint& pos = wxDefaultPosition,
  const wxSize& size = wxDefaultSize,
  long style = 0;
  const wxString& name = wxT("panel"));
  ```
  **基本窗口类型 P61**  
  **wxWindows相关事件 P63**
- wxWindow实现的成员函数可以方便的被其子类掉用
  1. CaptureMouse / ReleaseMouse / GetCapture / HasCapture  
     前两个可以捕获/释放鼠标。在绘图时，可以让鼠标不移出去。GetCapture获取当前正在使用的捕获设备。HasCapture检测鼠标是否被该窗口捕获。
  2. Centre, CentreOnParent, CentreOnScreen  
    调整自己位置位于父窗口或屏幕中心
  3. ClearBackground 使用当前背景色清除窗口
  4. ClientToScreen / ScreenToClient 将坐标在屏幕坐标系和客户区坐标系相互转换。
  5. Close函数产生一个wxCloseEvent事件
  6. ConvertDialogToPixels 和 ConvertPixelsToDialog函数可以对数值进行对话框单位和像素单位的转为。
  7. Destroy安全的释放窗。
  8. Enable(bool) 允许或禁止窗口及其子窗口处理输入事件。Disable函数和Enable函数功能一样，即两者使用false作为参数的效果是完全一样的。
  9. FindFocus函数是一个静态函数，可以找到当前拥有键盘焦点的窗口。
  10. FindWindow函数通过标识符或者名字在窗口关系树中寻找某个窗口，返回值可能为它的本身或子窗口。若确定要找的窗口类型，可以**安全的使用wxDynamicCast进行类型强制转换**，转换的结果是一个指向该类型的指针或NULL。如：`MyWindow* window = wxDynamicCast(FindWindow(ID_MYWINDOW), MyWindow);`
  11. Fit函数会自动改变窗口大小来容纳其子窗口，被应用在基于sizer布局时。FitInsid函数类似，区别在于它使用的是虚大小(应用在那些包含滚动条的窗口)
  12. Freeze / Thaw 告诉wxWidgets，在这两个函数之间进行的刷新界面的操作是允许被优化的。如要在文本框控件逐行加入多行文本，可以用该方法优化显示效果，避免闪烁。
  13. GetAcceleratorTable / SetAcceleratorTable获取和设置某窗口的加速键表。
  14. GetBackgroundColour / SetBackgroundColour 访问窗口的背景颜色属性。该属性被wxEVT_ERASE_BACKGOUND事件用来绘制背景。修改后应调用Refresh或ClearBackground函数来刷新北京。**SetOwnBackgroundColour**作用与SetBackgroundColour基本相同，但前者不会更改当前窗口的子窗口背景属性。
  15. GetBestSize函数以像素为单位返回窗口最适合的大小。
  16. GetCaret / SetCaret函数访问或者设置窗口光标
  17. GetClientSize /SetClientSize访问客户去大小
  18. GetCursor / SetCursor访问鼠标
  19. GetDefaultItem 返回指向该窗口默认子按钮的指针或者NULL。默认子按钮指的是按回车键时默认激活的按钮。可以通过wxButton::SetDefault函数进行设置。
  20. GetDropTarget / SetDropTarget函数访问wxDropTarget对象，该对象用来处理和控制窗口的拖放操作。
  21. GetEventHandler / SetEventHandler 访问窗口的第一事件表。默认是窗口自己，但可以指定不同的事件表，或通过PushEventHandler和PopEventHandler函数设置事件表链。
  22. GetExtraStyle / SetExtraStyle 访问窗口的扩展类型风格。
  23. GetFont / SetFont 访问与窗口相关的字体。**SetOwnFont**功能与SetFont相似，前者设置的字体不会被子窗口继承。
  24. GetForegroundColour / SetForegroundColour访问窗口的前景颜色属性。**SetOwnForegroundColour**不会改变子窗口的属性。
  25. GetHelpText / SetHelpText 访问窗口相关的上下文帮助
  26. GetId / SetId 访问窗口标识符
  27. GetLabel / SetLabel 访问标签
  28. GetName / SetName 访问窗口名称
  29. GetParent
  30. GetPosition返回相对于父窗口的窗口左上标
  31. GetRect 返回wxRect 包含窗口大小和位置
  32. GetSize / SetSize
  33. GetSizer / SetSizer 访问该窗口绑定的最顶级的窗口布局对象
  34. GetTextExtent 返回当前字体下某个字符串的像素长度
  35. GetToolTip/SetToolTip  访问tooltip
  36. GetUpdateRegion 返回窗口自上次Paint事件以来需要刷新的区域
  37. GetValidator / SetBalidator
  38. GetVirtualSize 窗口的虚大小
  39. GetWindowStyle / SetWindowStyle操作窗口类型比特位
  40. InitDialog发送一个wxEVT_INIT_DIALOG事件来开始对话框数据的传送
  41. IsEnabled检测当前窗口的使能状态
  42. IsExposed检测一个点或一个矩形区域是否位于需要刷新的范围
  43. IsShown用来指示窗口是否可见
  44. IsTopLever指示是否顶层窗口。(**仅用于wxFrame或wxDialog**)
  45. Layout函数用来在窗口已指定一个布局对象的情况下更新窗口布局
  46. Lower/Raise函数将窗口移到窗口树的最底/顶层
  47. MakeModal禁用其他所有顶层窗口，以便用户只能和当前顶层窗口交互
  48. Move用来移动窗口
  49. MoveAfterInTabOrder/MoveBeforeInTabOrder更改窗口的TAB顺序到作为参数的窗口的后/前面
  50. PushEventHandler压入一个事件表到当前的事件表栈，PopEventHandler弹出且返回事件表栈最顶层的事件表，RemoveEventHandler查找事件表中的一个事件表并将其出栈
  51. PopupMenu在某位置弹出菜单
  52. Refresh / RefreshRect函数导致窗口收到一个重画事件
  53. SetFocus函数让本窗口收到键盘焦点
  54. SetScrollbar函数用来设置窗口内建的滚动条属性
  55. SetSizeHints函数用来定义窗口的最小最大尺寸
  56. Show函数显示/隐藏窗口  Hide相当于Show(false)
  57. transferDataFromWindow / transferDataToWindow
  58. Update立刻重画窗口已过期区域
  59. UpdateWindowUI发送wxUpdateUIEvents事件到窗口
  60. Validate使用当前的验证对象验证窗口数据


## 绘画和打印







