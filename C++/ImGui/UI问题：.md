
### 输入框的标签默认在后面，且不能设置为NULL：
使用`##`就可以默认不显示后面的字符
```cpp
static char buf[32] = "你好";
//static char buf[32] = u8"NIHONGO"; // <- this is how you would write it with C++11, using real kanjis
ImGui::InputText("##1", buf, IM_ARRAYSIZE(buf));
```


### 同一行，文本和输入框不在同一高度：
![](images/Pasted%20image%2020240627204550.png)

```cpp
{ //使用局部作用域，防止对齐样式一直用
ImGui::AlignTextToFramePadding(); // 调整文本对齐到框架填充
ImGui::Text("UserName: ");

ImGui::SameLine();

static char buf[32] = "你好";
ImGui::InputText("##1", buf, IM_ARRAYSIZE(buf));
}
```















