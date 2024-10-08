# 自动补全
当出现提示补全时按回车却不能自动给补全，解决方案：
工具----选项----[文本编辑器](https://so.csdn.net/so/search?q=%E6%96%87%E6%9C%AC%E7%BC%96%E8%BE%91%E5%99%A8&spm=1001.2101.3001.7020)----C/C++----高级----主动提交成员列表，设置为**True**----点击确认
![](images/Pasted%20image%2020240228162049.png)


# 设置新标准
**std::string_view是C++17标准发布后新增的内容，而VS2022在项目属性页中默认的是C++14标准**。那么修改方法：
![](images/Pasted%20image%2020240319183634.png)
![](images/Pasted%20image%2020240319183703.png)
把上面这里改成c17
![](images/Pasted%20image%2020240319183747.png)
把这里改成c++20就行




visual默认支持的语言版本是c++14，所以17的一些特性用不了，那么要用的话就这样：
![](images/Pasted%20image%2020240329144605.png)
然后在这里将语言标准修改：
![](images/Pasted%20image%2020240329144635.png)
如果只修改源文件的不行，那么把解决方案的语言标准也修改了就行。




# 项目导出为exe
只需要将DEBUG设置为release，再运行，就会生成exe文件
![](images/Pasted%20image%2020240624210744.png)


# 运行时不出现终端console
把这里的console改成Window
![](images/Pasted%20image%2020240625164906.png)
然后把`int main()`改成
```cpp
int WinMain(HINSTANCE hInstance,HINSTANCE hPrevInstance,LPSTR lpCmdLine,int nShowCmd) 
```
然后重新生成就行了。

