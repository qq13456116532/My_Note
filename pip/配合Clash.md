![[Pasted image 20230703214540.png]]
可以看到Clash的端口是7890，所以我们在 pip intsall中指定这个端口

```python
pip --proxy //127.0.0.1:7890 install pyinstaller
```
或者

```python
pip --proxy //127.0.0.1:7890 install  -r requirements.txt
```


这样次次都使用 --proxy指定太麻烦了，我们可以考虑设置环境变量，这样就不需要每次都手动输入代理地址了。以下是如何在不同的操作系统中设置环境变量的方法：
  
如果你经常需要使用代理，可以考虑设置环境变量，这样就不需要每次都手动输入代理地址了。以下是如何在不同的操作系统中设置环境变量的方法：

1. **Windows**：在命令提示符（cmd）中，你可以使用以下命令设置环境变量：
    
```c
setx http_proxy http://127.0.0.1:7890
setx https_proxy http://127.0.0.1:7890
```
    这将会设置 `http_proxy` 和 `https_proxy` 环境变量。之后，你可以直接使用 `pip install`，pip 将会自动使用这些环境变量中的代理。
    
2. **Linux/Mac**：在 bash shell 中，你可以将以下行添加到你的 `~/.bashrc` 或 `~/.bash_profile` 文件中：

```shell
    export http_proxy=http://127.0.0.1:7890 
    export https_proxy=http://127.0.0.1:7890
```
    然后，运行 `source ~/.bashrc` 或 `source ~/.bash_profile` 来使改动生效。之后，你可以直接使用 `pip install`，pip 将会自动使用这些环境变量中的代理。







