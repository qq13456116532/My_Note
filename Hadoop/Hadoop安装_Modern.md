```bash
sudo passwd root  # 输入root
su -
```

先进入root用户，
```bash
sudo -s
sudo vim ~/.bashrc
```
在末尾添加上
```bash
source /etc/profile
```

然后exit


下载hadoop和Java
```bash
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz
sudo wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
```

解压缩
```bash
sudo tar -zxvf jdk-8u141-linux-x64.tar.gz
sudo tar -zxvf hadoop-3.2.2.tar.gz 
```


```bash
sudo mv jdk1.8.0_141/  /usr/java
sudo mv hadoop-3.2.2/ /usr/hadoop
```
在主文件夹下查看隐藏文件![](images/Pasted%20image%2020231021191611.png)

打开这个 `.profile`文件，添加环境变量
```bash
export JAVA_HOME=/usr/java
# JDK8 添加 JRE_HOME 和 CLASSPATH 配置
export JRE_HOME=$JAVA_HOME/jre
CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin:$PATH
```


然后在终端输入
```bash
source ~/.profile
```


然后配置Hadoop：
找到这个文件![](images/Pasted%20image%2020231021191956.png)
在末尾添加
```bash
export JAVA_HOME=/usr/java
```



然后找到 `core-site.xml`文件
```xml
<configuration>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/usr/hadoop/tmp</value>
  </property>

  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

然后找到`hdfs-site.xml`文件，改成这样
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>3</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/hadoop/tmp/dfs/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/hadoop/tmp/dfs/data</value>
  </property>
  <property>
    <name>dfs.http.address</name>
    <value>0.0.0.0:50070</value>
  </property>
</configuration>
```

然后打开终端，
```bash
cd /usr/hadoop/bin
./hdfs namenode -format
```
进行格式化

然后启动
```bash
cd /usr/hadoop/sbin
./start-all.sh
```

![](images/Pasted%20image%2020231021192746.png)
启动失败是因为没有配置ssh。

安装SSH
```bash
sudo apt-get install openssh-server
ssh localhost # 输入yes
exit
cd ~/.ssh/ # 若没有该目录，再执行一次ssh localhost
ssh-keygen -t rsa # 会有提示，都按回车就可以
cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
```

确保SSH服务配置正确，允许root用户登录。你可以在`/etc/ssh/sshd_config`文件中检查`PermitRootLogin`选项，确保它被设置为`yes`或者`without-password`。
```bash
sudo vim /etc/ssh/sshd_config
PermitRootLogin yes
```


启动
```bash
cd /usr/hadoop/sbin
./start-all.sh
```
![](images/Pasted%20image%2020231021193316.png)
启动成功


安装HBase
```bash
wget https://archive.apache.org/dist/hbase/2.2.2/hbase-2.2.2-bin.tar.gz
```
解压缩
```bash
sudo tar -zxf hbase-2.2.2-bin.tar.gz 
sudo mv hbase-2.2.2/ /usr/hbase
```

这里实际证明不需要
```bash
sudo chown -R hadoop ./hbase # maybe?

```

然后上面通过相同的方法添加环境变量：
```bash
export PATH=$PATH:/usr/hbase/bin
```
然后检验安装：
```bash
hbase version
```
然后去配置HBase
```bash
找到 /usr/hbase/conf/hbase-env.sh
# 添加
export JAVA_HOME=/usr/java
export HBASE_CLASSPATH=/usr/hbase/conf
export HBASE_MANAGES_ZK=true
```
若想删除启动时的无关警告输出，可以在这里删除掉最后一行的注释符：
```bash
export HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP="true"
```
然后去找`conf/hbase-site.xml`
```xml
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://localhost:9000/hbase</value>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.unsafe.stream.capability.enforce</name>
        <value>false</value>
    </property>
</configuration>
```
启动HBase，首先需要启动Hadoop
```bash
cd /usr/hadoop/sbin
./start-all.sh
```

启动时如果报错访问被拒绝，那么使用root用户

```bash
su root 
ssh-keygen
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
ssh root@localhost
```

```bash
cd /usr/hadoop/bin
./hdfs dfs -chmod 777 /
```

再次启动
```bash
sudo chown -R kaiwang:kaiwang /usr/hbase/logs
cd /usr/hbase
./bin/start-hbase.sh
```



然后可以进入HBASE命令行
```bash
./bin/hbase shell
status 'simple' # 检查HBase集群的状态
```


创建表
```bash
create 'users', 'personal', 'contact'

```













