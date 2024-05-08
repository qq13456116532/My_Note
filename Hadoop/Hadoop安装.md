### 安装伪分布模式

非root账户： kaiwang       159137

```bash
sudo useradd -m hadoop -s /bin/bash
sudo passwd hadoop 
输入 hadoop 
sudo adduser hadoop sudo 
sudo su hadoop
```
安装SSH ,这一步要特别注意代理网络没问题
```bash
sudo apt-get install vim
sudo apt-get install openssh-server
ssh localhost
exit
cd ~/.ssh/ # 若没有该目录，再执行一次ssh localhost
ssh-keygen -t rsa # 会有提示，都按回车就可以
cat ./id_rsa.pub >> ./authorized_keys  # 加入授权

```
**此时再用 `ssh localhost` 命令，无需输入密码就可以直接登录了。**
下载Java
```bash
cd /usr
sudo mkdir java
cd java
sudo wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
sudo tar -zxvf jdk-8u141-linux-x64.tar.gz
sudo vim /etc/profile
```
在配置文件最后写入
```bash
export JAVA_HOME=/usr/java/jdk1.8.0_141
CLASSPATH=$JAVA_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH
```

这里可能每次启动终端都需要重新加载一下，不然环境变量相当于没有修改
```bash
source /etc/profile
```
或者在
```bash
sudo vim ~/.bashrc
```
里面把上面那个添加上



下载Hadoop
```bash
sudo wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
sudo tar -zxvf hadoop-3.3.6.tar.gz -C /usr/local # 解压到/usr/local中
cd /usr/local/
sudo mv ./hadoop-3.3.6/ ./hadoop # 将文件夹名改为hadoop
sudo chown -R hadoop ./hadoop # 修改文件权限
cd /usr/local/hadoop
bin/hadoop version
```
搭建伪分布式
```bash
sudo vim etc/hadoop/core-site.xml
```
将原先的`<configuration></configuration>`改为下面的内容：
```xml
<configuration> 
  <property> 
    <name>hadoop.tmp.dir</name> 
    <value>file:/usr/local/hadoop/tmp</value> 
    <description>Abase for other temporary directories.</description>       </property> 
  <property> 
    <name>fs.defaultFS</name> 
    <value>hdfs://localhost:9000</value> 
  </property> 
</configuration>
```

```bash
vim etc/hadoop/hdfs-site.xml
```
将原先的`<configuration></configuration>`改为下面的内容：
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>  
    <value>file:/usr/local/hadoop/tmp/dfs/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/tmp/dfs/data</value>
  </property>
</configuration>
```
如果下面的报错Java_Home找不到，那么先去
```bash
vim /usr/local/hadoop/etc/hadoop/hadoop-env.sh
```
修改这一行：
```bash
export JAVA_HOME=/usr/java/jdk1.8.0_141
```

然后修改权限
```bash
sudo chown -R hadoop:hadoop /usr/local/hadoop/logs
sudo chmod -R 755 /usr/local/hadoop/logs
```


配置完成后，执行 NameNode 的格式化:
```bash
cd /usr/local/hadoop
bin/hdfs namenode -format
# 然后输入 Y
```
接着开启 NameNode 和 DataNode 守护进程：
```bash
cd /usr/local/hadoop
sbin/start-all.sh #start-all.sh是个可执行文件，中间没有空格
```


















