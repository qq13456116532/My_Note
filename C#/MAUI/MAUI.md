.NET MAUI 是一种多平台框架，用于使用 C# 和 XAML 创建本机桌面和移动应用，可设计能够在 Windows、Android、iOS、iPadOS 和 macOS 上运行的移动应用。


它把UI的实现变成了一个标签，类似`html`，
```html
<Button Text="Click me" SemanticProperties.Hint="Counts the number of times you click"
        Clicked="OnCounterClicked"
        HorizontalOptions="Center" />
```
这个就是定义了按钮的标签（“Click me”），并指定当用户选择该按钮时，应运行名为 `OnCounterClicked` 的方法。


# 开发流程
创建一个自己的`cs`代码文件，里面编写自己需要的逻辑，
然后把`MainPage.xaml`里`ContentPage`里面的内容删了，改成自己想要的页面。
然后在`MainPage.xaml.cs`里面编写各种事件的触发逻辑，如按钮
```c
<Button x:Name = "CallButton"
                Text = "Call"
                IsEnabled = "False"
                Clicked = "OnCall"/>
```
就需要编写`OnCall(object sender, EventArgs e)`函数。
可以直接把
```c
x:Name = "CallButton"
```
这里的`CallButton`当对象名使用。

然后之前自己编写的`.cs`代码文件就可以在这里使用，例如之前创建了静态`A`类，那么这里就可以使用`A.method()`了。

然后可以准备**分类关注点**，就是把控件行为和UI分离，即上面的`OnCall`可以在C#代码里添加，如下
```C
public MainPage() { 
	InitializeComponent(); 
	Counter.Clicked += OnCall; 
} 
private void OnCall(object sender, EventArgs e)
{ 
	... 
 }
```

然后准备创建多页页面。创建`Pages`文件夹，写多个页面，每个页面都要和`MainPage.xaml`和`MainPage.xaml.cs`相似的代码，主要改一下类名，改一下页面样子。

然后选择一种导航方式，可以是浮出控件（类似Anki），可以是TAB（类似京东）。




# 弹窗
一般是在事件处理时才弹窗，所以在代码中编写
```c
async void OnCall(object sender, System.EventArgs e)
    {
        if (await this.DisplayAlert(
            "Dial a Number",
            "Would you like to call " + translatedNumber + "?",
            "Yes",
            "No"))
        {
            // do when yes 
        }
    }
```



# 事件响应
编写的事件响应方法有以下要求：
- 方法必须为 `void`。
- 它必须采用两个参数；一个是 `object` 引用，指示触发事件的对象（称为发送方），另一个是 `EventArgs` 参数，包含发送方传递给事件处理程序的任何自变量。
- 方法最好是 `private`。
- 如果需要运行异步操作，事件处理程序可以是 `async`。



# 检测网络连接
使用 `Connectivity` 类。 该类公开了一个名为 `NetworkAccess` 的属性和一个名为 `ConnectivityChanged` 的事件。 可以使用这些成员来检测网络中的变化。
```cs
if(Connectivity.Current.NetworkAccess == NetworkAccess.None) 
{
    ...
}
```
上面是检测是否有连接，下面是监听网络变化事件的触发
```cs
Connectivity.Current.ConnectivityChanged += Connectivity_ConnectivityChanged;
...
void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs  e)
{
    bool stillConnected = e.IsConnected;
}
```


















# 安装
首先安装MAUI：
```c
dotnet workload install maui
```

然后在Vsocde里面安装插件![](images/Pasted%20image%2020241007171516.png)
然后还要先下载安卓SDK，网站是[VsCode to develop MAUI ](https://learn.microsoft.com/en-us/dotnet/maui/get-started/installation?view=net-maui-8.0&tabs=visual-studio-code#android)
首先安装微软的JDK，这个里面应该是自带了安卓的SDK，网站是[OpenJDK](https://learn.microsoft.com/en-us/java/openjdk/download#openjdk-17)，选择![](images/Pasted%20image%2020241007191735.png)下载解压后，加到环境变量即可。



然后创建一个MAUI的模板项目：
Vscode里面`CTRL+SHIFT+P`，搜索即可，
![](images/Pasted%20image%2020241007171637.png)
这个就是基础模板：![](images/Pasted%20image%2020241007165611.png)
其中 
- `App.xaml` 定义应用将在 XAML 布局中使用的应用程序资源，即设置**内置控件**的颜色和默认样式
- `App.xaml.cs`是 App.xaml 文件的代码隐藏文件。 此文件定义 App 类。 此类表示**运行时的应用程序**。可以设置应用程序生命周期事件处理程序。
- `AppShell.xaml`是 .NET MAUI 应用程序的主要结构。组织应用的界面和导航逻辑，使得管理应用程序的不同页面和导航更加直观和简化。
- `MainPage.xaml`包含用户界面定义，就是你想要展示的页面在这里修改。
- `MainPage.xaml.cs`是页面的代码隐藏文件。 在此文件中，为页面上的控件触发的各种事件处理程序和其他操作定义逻辑。
- `.csproj` 文件里的`ItemGroup`可在第一个窗口出现之前，为应用加载时出现的初始屏幕指定图像和颜色。 还可以为应用使用的字体、图像和资产设置默认位置。



然后在项目目录下使用.NET CLI 来安装或更新 Android SDK 和 Java SDK，并且接受所需的开发许可证，使用如下命令
```c
dotnet build -t:InstallAndroidDependencies -f:net8.0-android -p:AndroidSdkDirectory="C:\Users\10293\AppData\Local\Android\Sdk" -p:JavaSdkDirectory="D:\Java_17_ms\jdk-17.0.12+7" -p:AcceptAndroidSDKLicenses=True
```
Android SDK 就安装在`%LOCALAPPDATA%\Android\Sdk`
Java SDK 就安装在 `D:\Java_17_ms\jdk-17.0.12+7`，这个是之前安装的微软openJdk的位置。

然后打开`.cs`文件，右下角会看到![](images/Pasted%20image%2020241007212715.png)
点击其中的`Debug Target`，然后选择一个设备：
![](images/Pasted%20image%2020241007213001.png)
就可以成功启动。

在右上角选择![](images/Pasted%20image%2020241007213308.png)即可，启动结果如下![](images/Pasted%20image%2020241007213348.png)


前面的`.NET SDK` 和 `Android SDK` 主要是为了让你的应用能够编译并运行在 Android 平台上，但它们并不负责启动 Android 模拟器。模拟器本身需要通过额外的软件来启动和管理。
所以我这里采用MUMU模拟器来启动项目，启动一个后![](images/Pasted%20image%2020241007215456.png)点击`ADB`查看占用的端口，
![](images/Pasted%20image%2020241007215603.png)

然后找到之前下载的`adb.exe`，一般在`C:\Users\10293\AppData\Local\Android\Sdk\platform-tools`，打开终端运行
```C
adb.exe connect 127.0.0.1:16384
```

然后因为MuMu不支持快速部署，所以需要先去`.csproj`，在`PropertyGroup`里面添加
```xml
<EmbedAssembliesIntoApk>true</EmbedAssembliesIntoApk>
```
来禁用快速部署。

然后开始Debug，浏览器里就会打开APP了。
![](images/Pasted%20image%2020241008090020.png)










