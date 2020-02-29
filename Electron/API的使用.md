Electron可以使用自身的api以及node.js的api

### electron的api

1. DOM File对象
为了让用户能够通过HTML5的file API直接操作本地文件，DOM的File接口提供了对本地文件的抽象。Electron在File接口中增加了一个path属性，它是文件在系统中的真实路径。

2. DOM <webview> 标签
使用 webview 标签来把 'guest' 内容（例如 web pages ）嵌入到你的 Electron app 中. guest内容包含在 webview 容器中.一个嵌入你应用的page控制着guest内容如何布局摆放和表达含义.
与 iframe 不同, webview 和你的应用运行的是不同的进程. 它不拥有渲染进程的权限，并且应用和嵌入内容之间的交互全部都是异步的.因为这能保证应用的安全性不受嵌入内容的影响.

3. window.open 函数
当在界面中使用 window.open 来创建一个新的窗口时候，将会创建一个 BrowserWindow 的实例，并且将返回一个标识，这个界面通过标识来对这个新的窗口进行有限的控制.
这个标识对传统的web界面来说，通过它能对子窗口进行有限的功能性兼容控制.想要完全的控制这个窗口，可以直接创建一个 BrowserWindow .
新创建的 BrowserWindow 默认为继承父窗口的属性参数，想重写属性的话可以在 features 中设置他们.
window.open(url[, frameName][, features])
url String
frameName String (可选)
features String (可选)

#### 主进程中使用的模块

1. app 模块
app 模块是为了控制整个应用的生命周期设计的。

2. autoUpdater 模块
这个模块提供了一个到 Squirrel 自动更新框架的接口。

3. BrowserWindow 模块
BrowserWindow 类让你有创建一个浏览器窗口的权力

4. contentTracing 模块
content-tracing 模块是用来收集由底层的Chromium content 模块 产生的搜索数据. 这个模块不具备web接口，所有需要我们在chrome浏览器中添加 chrome://tracing/ 来加载生成文件从而查看结果.

5. dialog 模块
dialog 模块提供了api来展示原生的系统对话框，例如打开文件框，alert框，所以web应用可以给用户带来跟系统应用相同的体验.

6. global-shortcut 模块
global-shortcut 模块可以便捷的为您设置(注册/注销)各种自定义操作的快捷键

7. ipcMain 模块
ipcMain 模块是类 EventEmitter 的实例.当在主进程中使用它的时候，它控制着由渲染进程(web page)发送过来的异步或同步消息.从渲染进程发送过来的消息将触发事件.

8. menu 模块
menu 类可以用来创建原生菜单，它可用作应用菜单和 context 菜单.
这个模块是一个主进程的模块，并且可以通过 remote 模块给渲染进程调用.
每个菜单有一个或几个菜单项 menu items，并且每个菜单项可以有子菜单.

9. MenuItem 模块
菜单项模块允许你向应用或menu添加选项。

10. powerMonitor 模块
power-monitor模块是用来监听能源区改变的.只能在主进程中使用.在 app 模块的 ready 事件触发之后就不能使用这个模块了.

11. powerSaveBlocker 模块
powerSaveBlocker 模块是用来阻止应用系统进入睡眠模式的，因此这允许应用保持系统和屏幕继续工作.

12. protocol 模块
protocol 模块可以注册一个自定义协议，或者使用一个已经存在的协议.

13. session 模块
session 模块可以用来创建一个新的 Session 对象.
你也可以通过使用 webContents 的属性 session 来使用一个已有页面的 session ，webContents是BrowserWindow 的属性

14. webContents 模块
ebContents 是一个 事件发出者.
它负责渲染并控制网页，也是 BrowserWindow 对象的属性.

15. Tray 模块
用一个 Tray 来表示一个图标,这个图标处于正在运行的系统的通知区 ，通常被添加到一个 context menu 上

16. Locales
app.getLocale() 返回的区域设置值

#### 渲染进程中使用的模块

1. desktopCapturer 模块
desktopCapturer 模块可用来获取可用资源，这个资源可通过 getUserMedia 捕获得到.

2. ipcRenderer 模块
ipcRenderer 模块是一个 EventEmitter 类的实例. 它提供了有限的方法，你可以从渲染进程向主进程发送同步或异步消息. 也可以收到主进程的相应.

3. webFrame 模块
web-frame 模块允许你自定义如何渲染当前网页 .

4. remote 模块
remote 模块提供了一种在渲染进程（网页）和主进程之间进行进程间通讯（IPC）的简便途径。

#### 两个进程通用模块

1. clipboard 模块
clipboard 模块提供方法来供复制和粘贴操作

2. crashReporter 模块
crash-reporter 模块开启发送应用崩溃报告.

3. nativeImage 模块
在 Electron 中, 对所有创建 images 的 api 来说, 你可以使用文件路径或 nativeImage 实例. 如果使用 null ，将创建一个空的image 对象.

4. screen 模块
screen 模块检索屏幕的 size，显示，鼠标位置等的信息.在 app 模块的ready 事件触发之前不可使用这个模块.

5. shell 模块
shell 模块提供了集成其他桌面客户端的关联功能.

更多详细信息：https://www.w3cschool.cn/electronmanual/electronmanual-shell.html
