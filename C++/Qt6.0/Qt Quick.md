- [[#禁止复制|禁止复制]]
- [[#信号/信号槽|信号/信号槽]]
- [[#Qt Quick引入自己写的类|Qt Quick引入自己写的类]]
		- [[#属性|属性]]
		- [[#MEMBER(！！！非常重要)|MEMBER(！！！非常重要)]]
		- [[#方法|方法]]
- [[#QML联合C++中的信号|QML联合C++中的信号]]
- [[#使用定时器|使用定时器]]
- [[#网格布局|网格布局]]
- [[#页面切换StackView|页面切换StackView]]
				- [[#使用已加载组件|使用已加载组件]]
				- [[#加载完成后push|加载完成后push]]
				- [[#自定义切换动画|自定义切换动画]]
- [[#引入Qt的新的包|引入Qt的新的包]]
- [[#QML中使用C++对象列表|QML中使用C++对象列表]]
- [[#锚点：上半部分的正中间|锚点：上半部分的正中间]]
- [[#播放音乐|播放音乐]]



在很多时候设置居中，margin之类属性的时候，如果没有效果，可以看看是否是没有设置这个元素的宽度或者是高度。


总体来说就是设置锚点，与父元素相关的位移和对应，

```cpp
Rectangle{
    id: bottomBar
    signal iconClicked(int iconIndex) // 定义信号，传递图标的索引

    anchors{
        left: parent.left
        right: parent.right
        bottom: parent.bottom
    }
    color:"black"
    height: parent.height/12
}
```
`anchors`里还有`leftMargin`，`verticalCenter`等等属性词，都是可以见名思义的。

# 父子组件通信
在子组件定义一个接收值的属性
```c++
// 定义一个接收值的属性 
property string passedText: "Default Text"
```
然后在父组件直接对这个属性值进行操作：
```c++
contactDetail.passedAvatar = modelData.avatar
```
contactDetail是子组件的id

# 布局方法
#### 锚点布局
这个不必多说，看图就行
![](images/Pasted%20image%2020240926161112.png)

#### 定位器Positioners
这是用于排列子项的组件，主要用于自动布局，主要有水平布局`Row`，垂直布局`Column`，
按网格排列的 Positioner，可以指定列数或行数的`Grid`，如下
```c++
Grid {
    columns: 3 // 每行三个子项
    spacing: 5 // 子项之间的间距
    Rectangle { width: 50; height: 50; color: "red" }
}
```
还有一种是在水平方向上排列子项，并在一行满时换行的`Flow`，如下
```
Flow {
    spacing: 10
    Rectangle { width: 50; height: 50; color: "red" }
}
```
当一行放不下更多的矩形时，它会自动换行。

#### 布局管理器Layout
布局管理器允许你更精细地控制布局的行为，例如设置元素的对齐方式、自动调整大小等。
`RowLayout` （将子元素水平排列）和 `ColumnLayout`（将子元素垂直排列）是最基本的布局方式。
![](images/Pasted%20image%2020240926183900.png)然后设置的`spacing`是调整元素之间的间距，设置的`anchors`是用于控制布局与父容器或其他元素的对齐方式，**禁止使用锚点**。
```ad-warning
如果指定`anchors.fill: parent`的话，会从最中间的那一行、那一列开始放置，要注意一下
```

有时候可能设置的宽度和其他的不一样，但是需要**居中**显示，但是这里不可以使用锚点，使用的是
```c++
Layout.alignment: Qt.AlignHCenter
```
如果要设置宽度，那么
```c++
Layout.preferredWidth: parent.width * 0.6
```
如果要设置Margin，那么
```c++
Layout.leftMargin: parent.width * 0.2
```

在使用`Text`和别的一起布局的时候，要注意后面的宽度有可能被限制到和`Text`的文字长度一起了，所以要防止这种情况，使用：
```c++
Text {
                    text: qsTr("Contact Info")
                    font.pixelSize: 18
                    font.bold: true
                    Layout.preferredWidth: parent.width
                    Layout.fillWidth: true  // 让 Text 填充可用宽度
                    horizontalAlignment: Text.AlignLeft  // 左对齐文本
                }
```
这里主要的就是`                    Layout.preferredWidth: parent.width`，设置这个是很重要的！！！




`GridLayout`提供了一种更为复杂的布局方式，允许开发者在二维网格中放置元素。通过设置行（row）和列（column）的索引，可以精确控制每个元素的位置。如下
```c++
GridLayout {
        columns: 2
        rows: 2
        Button { text: "A"; Layout.column: 0; Layout.row: 0 }
        Button { text: "B"; Layout.column: 1; Layout.row: 0 }
        Button { text: "C"; Layout.column: 0; Layout.row: 1; Layout.columnSpan: 2 } // 跨两列
        Button { text: "D"; Layout.column: 0; Layout.row: 2; visible: false } // 隐藏元素
    }
```






# 禁止复制
`QObject`是禁止复制的，也就是说
```c++
void Playlist::addSong(Song& song)
{
    m_songs.append(song);
}
```
如果`Song`是`QObject`，那么你在调用这个构造函数的时候`QList::append()` 会尝试复制对象，而 `Song` 类继承自 `QObject`，因此无法复制。所以会报错，那么使用这种`List`类型时，最好都是存储指针而不是对象。


# 信号/信号槽
要设置点击某处，另一处出现效果，可以设置信号和信号槽
```cpp
import QtQuick 2.15
import QtQuick.Controls 2.15 //为了在qt quick中引入信号

Rectangle{
    id: bottomBar
    signal iconClicked(int iconIndex) // 定义信号，传递图标的索引
    
	Image {
	MouseArea{
            onClicked: iconClicked(0)
        }
    }
}
```
然后在信号槽部分是使用 `on<信号名>` 的格式
```cpp
BottomBar{
		property int currentIndex: 0 // 当前显示的索引
        id: bottomBar
        // 连接信号到槽函数
        onIconClicked: {
            currentIndex = iconIndex
        }
    }
```



# Qt Quick引入自己写的类


### 属性
自己写的类如果是属性，是使用`Q_PROPERTY`定义，这样QML才能访问
```c++
    Q_PROPERTY(QString currentTrack READ currentTrack NOTIFY currentTrackChanged)
```
第一个是类型，第二个是属性名，第三个是可读的意思，而且是使用什么方法读，就是你必须定义好`currentTrack()`这个方法，返回对应的属性，`NOTIFY`是指信号，你需要在类中定义好名为`currentTrackChanged`的signal，如果没有`NOTIFY`的话，代码逻辑中这个属性被改变时就根本不会显示到Qml里面，所以必须要有对应的信号，然后代码逻辑触发这个信号后qml就会去更新。

### MEMBER(！！！非常重要)
在 `Q_PROPERTY` 宏中，`MEMBER` 是一种简化的方式，它可以直接替代 `read` 和 `write` 方法，自动生成 getter 和 setter 函数。这样做可以减少代码量，因为不再需要手动定义这些函数。
具体来说，`MEMBER` 直接绑定到一个类成员变量，并提供一个默认的 `getter` 和 `setter`。这适用于一些简单场景，例如直接暴露一个成员变量，而无需对它进行复杂的逻辑操作。
如下：
```c++
Q_PROPERTY(QList<QObject*> contacts MEMBER m_contacts NOTIFY contactsChanged)
```
这里`MEMBER m_contacts` 表示 `contacts` 属性直接绑定到类的成员变量 `m_contacts`，不再需要额外的`getter`或者`setter`了



### 方法
自己写的类包含了处理逻辑，那么需要在主代码中引入。
在`main.cpp`中，使用
```cpp
#include<Controllers/system.h> //这个是自己写的代码
#include<QQmlContext>

int main(int argc, char *argv[])
{
 // 。。。其他内容
System m_systemHandler;//自己写的类
QQmlContext * context(engine.rootContext());
context->setContextProperty("systemHandler",&m_systemHandler);

}
```
这样就可以在qml文件里面使用`systemHandler`这个属性。
使用时大概这样：
```cpp
source: (systemHandler.carLocked )? "qrc:/ui/assets/lock.png":"qrc:/ui/assets/unlock.png"
```
上面是调用自己类的属性，下面是调用自己类的方法`setCarLocked()`：
```cpp
MouseArea{
    anchors.fill: parent
    onClicked: systemHandler.setCarLocked(!systemHandler.carLocked)
}
```

> 但是要注意！！！ 如果要这样调用的话必须在定义这个方法的前面加上`Q_INVOKABLE`！！！如下
```cpp
class System : public QObject
{
public:
	Q_INVOKABLE void setCarLocked(bool newCarLocked);
}
```


# QML联合C++中的信号
之前讲了信号和信号槽都定义在`qml`文件里面的用法，下面讲一下如何在qml中使用C++文件里的信号
首先c++文件中定义
```cpp
class AudioController : public QObject
{
	signals:
	    void volumeLevelChanged();
}
```
然后引入到Qt quick中，
```cpp
engine.rootContext()->setContextProperty("audioController",&m_audioController);
```
`Connections` 元素允许我们将信号与槽函数连接，即在指定信号发出时执行特定的代码块。
`target` 指定了信号的发送者，`audioController` 就是上面的对象，它有一个 `volumeLevelChanged` 信号。
`function onVolumeLevelChanged()` 定义了一个槽函数，当 `volumeLevelChanged` 信号被触发时，这个函数会被调用。
```cpp
Connections{
        target: audioController
        function onVolumeLevelChanged(){
            visibleTimer.stop()
            volumeIcon.visible = false
            visibleTimer.start()
        }
}
```





# 使用定时器
```cpp
Timer{
        id: visibleTimer
        interval: 500
        repeat: false
        onTriggered: {
            volumeIcon.visible=true
    }
}
```
在使用时是这样的：
```cpp
visibleTimer.stop()
visibleTimer.start()
```

# 网格布局
要实现下面动图的八个格子的那种页面，主要使用`GridView` 
`GridView` 是 QML 中用于实现网格布局的一种视图组件。`GridView` 通过 `model` 属性与数据模型连接，使用 `delegate` 来定义每个网格单元的显示方式。
```cpp
ListModel{
        id: listModel
        ListElement{name:"One"; page:"StationPage.qml"}
        ListElement{name:"Two"; page:""}
        ListElement{name:"Three"; page:""}
        ListElement{name:"Four"; page:""}
        ListElement{name:"Five"; page:""}
        ListElement{name:"Six"; page:""}
        ListElement{name:"Seven"; page:""}
        ListElement{name:"Eight"; page:""}
    }
    GridView{
        id: gridView
        anchors.fill: parent
        model: listModel
        cellHeight: 240
        cellWidth: 240
        delegate: Rectangle{
            width: 200
            height: 200
            color: "grey"
            radius: 5
            Text {
                color: "white"
                text: name
                anchors.centerIn: parent
            }
            MouseArea{
                anchors.fill: parent
                //onClicked: mainLoader.source = page
                onClicked: stackview.push(page)
            }
        }
    }
```
`GridView` 是根据提供的 `cellWidth` 和 `cellHeight` 属性来排列内容，使得每个项目都以网格的形式展示。`delegate` 属性定义了如何呈现每个数据项。
这个类适合用于显示类似图片库、商品列表、图标面板等需要网格布局的数据集合


# 页面切换StackView
要实现下面这样的效果，有一种粗暴的切换方式`Loader`,但是没有切换动画，就不讲了，所以讲的是StackView
![](images/页面切换.gif)
实现这个，主要是`import QtQuick.Controls`里的`StackView`这个类。
然后切换前是不同的页面，是使用**独立的 QML 文件**或 QML 组件来代表不同的页面。
但是前面简单的放一下Loader也行
```cpp
StatusBar{
        id: statusBar
}
Loader{
        id: mainLoader
        anchors{
            left: parent.left
            right: parent.right
            top :statusBar.bottom
            bottom: parent.bottom
        }
        source:"StackViewPage.qml"
    }
```
这种就是主要用于切换也只切换部分页面。

首先需要设置 `StackView` 的初始页面，使用 `initialItem` 属性
```cpp
StackView{
	id: stackview
	anchors.fill: parent
	initialItem: "GridOne.qml"
}
```
这里的`GridOne.qml`就是上面的8个格子的页面

然后在`GridOne.qml`要设置切换的方法，如下
```cpp
Button{
        id: nextButton
        anchors{
            right: parent.right
            bottom: parent.bottom
            margins: 20
        }
        text:"Next"
        onClicked: stackview.push("GridTwo.qml")
}
```
在执行`stackview.push("GridTwo.qml")`之后，页面会切换到`GridTwo`的页面,然后栈里面存着`GridOne.qml`，在新页面要返回旧页面，也是进行栈的操作，但是是弹出：
```cpp
Button{
        id: previousButton
        anchors{
            right: nextButton.left
            rightMargin: 10
            verticalCenter: nextButton.verticalCenter
        }
        text:"Previous"
        onClicked: stackview.pop("GridTwo.qml")
    }
```
就是把自己这个页面弹出，返回栈中的上个页面

##### 使用已加载组件
这种情况不好的地方就是你每次`push`时，都会有一个新的实例，所以最好在一个页面中使用已加载的组件`push`和`pop`，但是为了防止未`push`也显示的情况，需要设置不可见，push时会自动可见：
```c++
//设置为不可见
RightScreen{
	id: rightScreen
	visible:false
}

CallScreen{
	id: callScreen
	visible:false
}
// 下面是在某个函数里面直接使用
if(iconIndex === 0) {
	rightStackView.pop()
	rightStackView.push(rightScreen)
}
if(iconIndex === 1) {
	rightStackView.pop()
	rightStackView.push(callScreen)
}
```


##### 加载完成后push
有时可能不需要`initialItem`，因为这个无法被`pop()`,所以我们可以选择在`stackview`加载完成之后立即`push`一个页面：
```c++
StackView{
        id: rightStackView
        anchors{
            top: parent.top
            bottom: bottomBar.top
            right: parent.right
        }
        width: parent.width*2/3
        //这个是一个纯白页面
        initialItem: rightBlankScreen
        Component.onCompleted: {
            rightStackView.push(rightScreen)
        }
}
```

##### 自定义切换动画
默认是左出右进，但是如果`stackview`不是占满左右两边可能有一些问题，所以这里自定义成淡入淡出：
```c++
StackView{
	// 自定义切换动画
	pushEnter: Transition {
		PropertyAnimation {
			property: "opacity"
			from: 0
			to:1
			duration: 200
		}
	}
	pushExit: Transition {
		PropertyAnimation {
			property: "opacity"
			from: 1
			to:0
			duration: 200
		}
	}
	popEnter: Transition {
		PropertyAnimation {
			property: "opacity"
			from: 0
			to:1
			duration: 200
		}
	}
	popExit: Transition {
		PropertyAnimation {
			property: "opacity"
			from: 1
			to:0
			duration: 200
		}
	}
}
```


# 引入Qt的新的包
我们可能需要新的包，比如播放多媒体的`QMediaPlayer`，那么首先在`Qt documentation`里面找到它，![](images/Pasted%20image%2020240913152509.png)看到这里的`CMAKE`了，把这两行添加到 `CmakeLists.txt`文件里面，![](images/Pasted%20image%2020240913152733.png)然后重新构建就不会出错了，![](images/Pasted%20image%2020240913152810.png)
如果报错，那么使用`Qt Maintenance Tool`先下载一下，![](images/Pasted%20image%2020240913153256.png)

# QML中使用C++对象列表
首先如果要传入，那么必须对象类型是`QObject`，然后需要把所有属性都设置为`Q_PROPERTY`，然后就使用QList定义对象列表，并放入Q_PROPERTY，如下
```c++
Q_PROPERTY(QList<Playlist*> playLists READ playLists NOTIFY playListsChanged)
```
这样就可以在QML中使用了。

在 QML 中，当你使用 `ListView` 等模型视图组件时，QML 会将 C++ 列表（如 `QList<Playlist*>`）作为数据源，并将每个元素转换为 QML 可以理解的对象。这些对象的属性通过 `Q_PROPERTY` 公开给 QML，因此你可以直接通过 `modelData.name` 这样的方式访问这些属性，而不需要像在 C++ 中那样使用 `->` 来访问指针成员。
```c++
ListView {
    model: playLists  // playLists 是一个 Q_PROPERTY 暴露的 QList<Playlist*>
    delegate: Item {
        Text {
            text: modelData.name  // 直接访问 Playlist 对象的 name 属性
        }
    }
}

```




# 锚点：上半部分的正中间
```c++
anchors{
	verticalCenter: parent.verticalCenter
	verticalCenterOffset: -parent.height / 4  // 将文本向上移动四分之一的父高度
}
verticalAlignment: Text.AlignVCenter
```



# 播放音乐
基本问题有两个，一个是`如何播放音乐`，另一个是`如何实现变化的图标`
第一点主要的问题就是qrc的路径寻找问题：
```c++
#include<QAudioOutput>
#include <QMediaPlayer>
QMediaPlayer* m_player = new QMediaPlayer;
QAudioOutput* audioOutput = new QAudioOutput;

audioOutput->setVolume(50);
m_player->setSource(QUrl("qrc:/ui/music/English/Not Alone.mp3"));
m_player->play();
```
路径就是按照 `qrc:/`开头就行了。
第二个问题就必须使用信号，这里又要提及一下，就是
```c++
Q_PROPERTY(bool isPlaying READ isPlaying NOTIFY isPlayingChanged)
```
这里的`NOTIFY`的`isPlayingChanged`，是在通知qml重新去执行`isPlaying`方法得到新的值，那么变化的图标解决方法就很简单了，就是在播放时发出这个信号就行：
```c++
void MusicPlayer::play()
{
    audioOutput->setVolume(50);
    m_player->play();
    // 发出自定义的 isPlayingChanged 信号
    emit isPlayingChanged();
}
```
对应的`isPlaying`是
```c++
bool MusicPlayer::isPlaying() const
{
    return m_player->playbackState() == QMediaPlayer::PlayingState;;
}
```
但是上面这种情况还是会导致一种情况，就是可能我在这边emit时，`m_player`还没有准备好音乐，就会导致并没有改变图标，所以一种更好的方式就是使用信号和信号槽，在`m_player`明确得到改变时，再会去更新isPlaying，如下：
```c++
//绑定信号和信号槽
connect(m_player, &QMediaPlayer::playingChanged, this, &MusicPlayer::updatePlayPauseIcon);
```
然后在信号槽里面发射信号，
```c++
void MusicPlayer::updatePlayPauseIcon()
{
    emit isPlayingChanged();
}
```
























