# VsCode 快捷键
| Press | Function |
| :---: | :---: |
| Ctrl+shift+p | |
| Ctrl+p | 打开某个文件 |
| Ctrl+shift+n/w | 打开/关闭新窗口 |
| Ctrl+, | user setting |
| Ctrl+shift+[/] | 折叠/打开当前代码块 |
| Ctrl+k Ctrl+[/] | 折叠/打开次区域代码块 |
| Ctrl+k Ctrl+0/j | 折叠/打开所有代码块 |
| Ctrl+k Ctrl+C/U | 添加/取消注释 |
| Ctrl+/ | 注释当前行 |
| Alt+shift+a | 块注释 |
| Ctrl+o/t | 显示当前/所有索引 函数名也算 |
| Ctrl+g | 去某行 |
| Ctrl+shift+m | 显示problems面板 |
| F8/shift+F8 | 后/前一个warning或error |
| alt + 左/右箭头 | 向左/右切换标签页 |
| Ctrl + f/h | |
| F3/shift+F3 | 找到后/前一个 |
| alt|enter | 在ctrl+f后使用，可以在所有搜索到的后面添加光标 |
| alt+鼠标左键 | 可以出现多个光标 |
| shift+alt+鼠标左键 |  |
| ctrl + space | 触发提示 |
| alt + F12 | 不跳转，只是显示定义 |
| ctrl+k, F12 | 显示定义在侧边 |
| shift+alt+f | 格式化整个文件 |
| Ctrl + \ | 拆分当前窗口 |
| Ctrl + 1/2/3 | 聚焦第1/2/3个窗口 |
| Ctrl+n | 新建文件 |
| Ctrl+o | 打开文件 |
| Ctrl+k s | 保存所有文件 |
| Ctrl+k p | 复制当前文件的路径 |
| Ctrl+k r | 在资源管理器中打开当前目录 |
| Ctrl+shift+e | 是否聚焦在资源管理器 |
| Ctrl+shift+f | 在资源管理器中使用，在所有文件中搜索 |
| Ctrl+shift+h | 在资源管理器中使用，在所有文件中替换 |
| Ctrl+shift+d | 打开debug界面 |
| Ctrl+shift+x | 打开插件界面 |
| Ctrl+shift+u | 显示output面板 |
| Ctrl+shift+v | 显示markdown预览 |
| Ctrl+k, z | 代码界面全屏 |
| F9 | 设置断点 |
| F5 | start/continue |
| shift+F5 | stop |
| F11/shift+F11 | Step into/out |
| F10 | Step over |
| Ctrl+k, Ctrl+i | show hover |
| Ctrl+whitespace | 触发自动补全 |


# VsCode c++ 配置
1. 安装c++ CMake CMakeTool NativeDebug插件
2. windows下安装mingw-w64编译器 [mingw-w64下载地址](https://sourceforge.net/projects/mingw-w64/postdownload) ，linux下g++  
   windows下还需要配置环境变量path为C:\MinGW\mingw64\bin  打开vscode，F1，输入CMake:scan kit,选择正确的kit,修改User setting添加："cmake.generator": "MinGW Makefiles"
3. 快捷键ctrl+shift+p 输入C/Cpp: Edit configurations 打开c_cpp_properties.json文件配置。compilePath为编译器路径，includePath为头文件路径