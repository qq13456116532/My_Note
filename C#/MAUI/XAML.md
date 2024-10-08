XAML 旨在简化在应用程序中创建 UI 的过程。采用 XAML 的这些元素**直接表示对象的实例化**。 使用 XAML 定义元素后，就可以在代码隐藏文件中对其进行访问，并可使用 C# 代码定义行为！！！！！！！！！！！！！！！！！！！



# 重要属性
`ContentPage`的`x:Class`属性指定与其对应的隐藏代码文件中的类，基本上是 `命名空间.类名`
元素的`x:Name`表示对象名，隐藏代码中可以直接使用，值用于在代码中创建字段，所以必须符合变量的命名约定

文件中的根元素
```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MauiApp1.MainPage">
```
第一个xmlns是页面的默认命名空间，将所有 .NET MAUI 类型和控件引入范围。 如果省略此命名空间，则无法使用控件，例如 `Button`、`Label``Entry` 或 `StackLayout`
第二个命名空间引用包含各种 .NET 内部类型的程序集，通过使用 x: 对这些类型添加前缀来引用此命名空间中的类型。

# 资源
这里资源的意思是给所有UI共享的对象。主要是为了在一个位置定义资源后，即可在需要它的任意位置引用它，可以来消除 XAML 中的硬编码值。
##### 创建资源
创建的方式是定义在页面`<ContentPage>`内部，
```xml
<ContentPage.Resources>
<ResourceDictionary>
    <Color x:Key="PageControlTextColor">Blue</Color>
    <x:Int32 x:Key="fontsize">32</x:Int32>
    <OnPlatform x:Key="textColor" x:TypeArguments="Color"> 
    <On Platform="iOS" Value="Silver" /> 
    <On Platform="Android" Value="Green" /> 
    <On Platform="WinUI" Value="Yellow" /> 
    <On Platform="MacCatalyst" Value="Pink" /> 
    </OnPlatform>
</ResourceDictionary>
</ContentPage.Resources>
```
上面放了三种定义方式。

##### 应用资源
有两种方式，一种是静态应用，一种是动态应用，
静态应用的话只会应用属性一次，当属性改变时，并不会随着改变，使用方式如下
```xml
<Label  FontSize="{StaticResource fontsize}" />
```
如果找不到这个`fontsize`会报错

😘有时可能在触发事件时会更新资源，
```c
this.Resources["fontsize"] = 20;
```
此时上面静态应用是做不到的，动态应用会侦听字典中对资源所做的更改。 如果字典中的资源值发生了更改，DynamicResource 会自动更新 UI，使用如下
```c
<Label  FontSize="{DynamicResource fontsize}" />
```
如果找不到这个`fontsize`，会让属性保持未设置状态，但不会报错。

##### 全局资源
主要是在`app.xaml`，你可以看到里面已经存在了引用了全局资源字典![](images/Pasted%20image%2020241008192046.png)那么添加自己的全局资源或者是全局样式，就是放到`<ResourceDictionary>`下面即可。


# 样式Style
上面资源虽然实现了动态/静态一次性修改所有样式，但是对所有控件设置的时候还是会发现太冗余了。于是使用Style来设置属性
```xml
<ContentPage.Resources>
<Style x:Key="MyButtonStyle" TargetType="Button">
    <Setter Property="BackgroundColor" Value="{StaticResource back}" />
    <Setter Property="BorderColor" Value="{StaticResource borderC}" />
    <Setter Property="BorderWidth" Value="{StaticResource border}" />
    <Setter Property="TextColor" Value="{StaticResource textC}" />
</Style>
</ContentPage.Resources>
```

那么在其他控件使用时，是这样：
```xml
<Button Text="OK" Style="{StaticResource MyButtonStyle}" />
```

如果`style`不设置`x:key`的话，就隐式地应用到所有Button上了， 
如果有按钮要个性化，那么对某些属性覆盖即可。




# 布局
它的布局类型主要有下面这些：
![](images/Pasted%20image%2020241008092355.png)
- `VerticalStackLayout` 和 `HorizontalStackLayout`就是从上到下或者从左到右
- `AbsoluteLayout`，支持设置控件的确切坐标。
- `FlexLayout` 是一种灵活的布局容器，它类似于 `StackLayout`，当内容在一行或一列中放不下时，能够自动换行。
- `Grid`，根据设置的列和行位置来布置其控件。



##### 优化布局
这里的控件也有`Margin`, `Padding` 。具体和HTML是一样的。


##### Grid
方位首先要搞清楚，![](images/Pasted%20image%2020241008164933.png)右下角是 `row 3 column 1` ，那就可以知道Row只确定高度，column只确定宽度

一个 `Grid` 布局通常包含以下几个部分：
- **行定义 (`RowDefinitions`)**：定义网格中的行，设置每行的高度。
- **列定义 (`ColumnDefinitions`)**：定义网格中的列，设置每列的宽度。
- **子控件**：每个子控件可以指定在某一行或列中显示，或者跨越多个行或列。

其中设置高度时，有三种取值，分别是`Auto`（根据内容确定），固定值和`*`，`*`是根据剩余空间来按比例分配

具体使用如下
```xml
<Grid RowDefinitions="*, *, *" ColumnDefinitions="*, *">
    <BoxView Grid.Row="1" Grid.Column="0" Color="Navy" />
</Grid>
```
因为设置了3行的高度，2列的宽度，所以总共只有6个`Grid`，然后里面选择了`Row 1,Coloumn 0`的那个网格占用，如下![](images/Pasted%20image%2020241008170946.png)

😘那么怎么让视图占据多行或多列的空间？
```xml
<Grid RowDefinitions="*, *, *" ColumnDefinitions="*, *">
    <BoxView Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2" Color="Navy" />
</Grid>
```
就变成了
![](images/Pasted%20image%2020241008171042.png)



# 标记扩展
在正常使用时，文字大小，如何定位之类的都是写定的，但是有时在程序运行时才能确定使用什么值，那么就需要标记扩展。
首先是编写一个继承接口的类
```c
public class GlobalFontSizeExtension : IMarkupExtension
{
    public object ProvideValue(IServiceProvider serviceProvider)
    {
        return MainPage.MyFontSize;//28
    }
}
```
然后以命名空间的方式引入到XAML，
```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:mycode="clr-namespace:Notes"
             x:Class="Notes.MainPage">
```
就是把上面那个接口所在的命名空间设置为`xmlns:mycode`的值即可
然后使用时就像下面这样：
```xml
<Label Text="Notes" FontSize="{mycode:GlobalFontSize}"/>
```
定义类的时候是 `XXXExtension`，使用时就不需要后面的`Extension`后缀了。



# 特定于平台的值
.NET MAUI XAML 提供 `OnPlatform` 标记扩展，允许你从 XAML 代码内部检测运行时平台。具体用法： 
```xml
<VerticalStackLayout>
<VerticalStackLayout.BackgroundColor>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Silver" />
            <On Platform="Android" Value="Green" />
            <On Platform="WinUI" Value="Yellow" />
        </OnPlatform></VerticalStackLayout.BackgroundColor>
        。。。
</VerticalStackLayout>
```
这种写法就很冗余，有更加简短的方式，如下
```xml
<VerticalStackLayout Padding="{OnPlatform iOS='30,60,30,30', Android='20,40,20,20', Default='30'}">
    <!--XAML for other controls goes here -->
</VerticalStackLayout>

```






































