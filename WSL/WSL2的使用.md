WSL是指 Windows Subsystem for Linux
WSL 2 仅在 Windows 11 或 Windows 10 版本 1903、内部版本 18362 或更高版本中可用

要查看自己的Windows版本，在cmd中输入 `
```c
winver
```
即可查看。

![[Pasted image 20230702211655.png]]


# WSL更新
如果当前是WSL1，那么首先需要更新到WSL2
先去`https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi` 下载。
然后安装，安装界面点击next就直接下载了。
![[Pasted image 20230702212447.png]]


然后以管理员身份运行CMD:
![[Pasted image 20230702212053.png]]

然后运行
```powershell
wsl --set-default-version 2
```

![[Pasted image 20230702212333.png]]

查看WSL发行版 

```powershell
wsl --list --online
```

![[Pasted image 20230702212533.png]]

# 安装Ubuntu

这里要使用WSL做强化学习，所以选Unbuntu。

```powershell
wsl --install -d Ubuntu
```
这里下载可能需要魔法？
![[Pasted image 20230702213652.png]]
可以看到我们这里需要输入用户名，接下来应该也要输入密码，那么这里设置成  `root123-root123`

![[Pasted image 20230702213835.png]]
这样就安装成功了。
我们使用  `exit`可以退出ubuntu的会话。

然后查看一下刚刚安装的WSL:
```powershell
wsl --list -v
```
![[Pasted image 20230702215436.png]]

此时在应用中可以发现![[Pasted image 20230702215540.png]]
点击这个也可以启动Ubuntu的WSL，如下![[Pasted image 20230702215856.png]]


# WSL的GUI可视化

```shell
sudo apt update
```
然后安装X11应用，它是 Linux 窗口管理系统，这是随它一起提供的各种应用和工具的集合

```shell
sudo apt install x11-apps -y
```
要启动就可以使用
- `xcalc`, `xclock`, `xeyes`
这几个命令

上面是专用的可视化，桌面的可视化如下：

```shell
sudo apt-get update
```


```shell
sudo apt-get install xrdp
```
![[Pasted image 20230703094905.png]]
这样就是安装成功
然后安装桌面环境（运行在类Unix操作系统上，提供轻量级桌面环境），这里有3.4G

```shell
sudo apt-get install kde-full
```

配置xrdp默认启动环境
```shell
sudo sed -i.bak '/fi/a #xrdp multiple users configuration \n startkde \n' /etc/xrdp/startwm.sh
```

开启端口及重启xrdp服务
```shell
# allow just RDP through the local firewall
sudo ufw allow 3389/tcp
# restart xrdp
sudo /etc/init.d/xrdp restart
```


然后在Windows上搜索`remote`，然后打开远程工具。
![[Pasted image 20230703095154.png]]

然后在ubantu上输入`ifconfig`获取ech0的ip地址
![[Pasted image 20230703095223.png]]
在远程工具里，填入ip:3389回车，即可打开桌面了。
![[Pasted image 20230703120306.png]]
输入账号密码，这里是 root123-root123
![[Pasted image 20230703120711.png]]
再次输入密码root123
![[Pasted image 20230703121140.png]]
就可以使用WSL2的Ubuntu的桌面了。
要使用终端，可以到左下角进行搜索。




# 进入其中一个Linux 发行版
首先使用
```shell
wsl -l
```
查看一下所有的Linux 发行版
![[Pasted image 20230703123236.png]]
这里假设要进入 Ubuntu，使用
```shell
wsl -d Ubuntu
```
![[Pasted image 20230703123527.png]]
成功进入。





