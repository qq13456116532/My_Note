在安装时注意要选择最小安装
![](images/Pasted%20image%2020230926145605.png)

系统创建完之后，
一、安装openssh
使用如下命令安装openssh
```bash
sudo apt install openssh-server
```

二、修改配置文件
安装完成后修改配置文件/etc/ssh/sshd_config，命令如下
```bash
apt install vim 
sudo vim /etc/ssh/sshd_config
```
将
```bash
 #PermitRootLogin prohibit-password
```
改成
```bash
PermitRootLogin yes
```

三、重启服务
使用如下命令程序ssh服务
```bash
sudo systemctl restart ssh
```

四、测试
使用如下命令测试是否能成功登录
```bash
ssh root@localhost
```
此时，ssh就开启了，再使用XSHELL测试一下
![](images/Pasted%20image%2020230926154453.png)











