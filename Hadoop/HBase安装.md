创建文件

```python
mkdir -p /data/hbase1 cd /data/hbase1 sudo wget https://dlcdn.apache.org/hbase/3.0.0-alpha-4/hbase-3.0.0-alpha-4-bin.tar.gz
```
然后解压
```bash
mkdir /apps 
sudo tar -xzvf /data/hbase1/hbase-3.0.0-alpha-4-bin.tar.gz -C /apps 
cd /apps 
mv /apps/hbase-3.0.0-alpha-4/ /apps/hbase
```
添加HBase的环境变量。首先使用vim打开用户环境变量文件。
```bash
sudo vim ~/.bashrc
```
在环境变量文件末尾位置，追加HBase的bin目录路径相关配置，并保存退出。即下列内容
```bash
#hbase 
export HBASE_HOME=/apps/hbase 
export PATH=$HBASE_HOME/bin:$PATH
```
执行source命令，使环境变量生效。
```bash
source ~/.bashrc
```
此时就可以调用HBase的bin目录下的脚本了。先来查看一下HBase的版本信息。
```bash
hbase version
```
然后修改配置文件
```bash
cd /apps/hbase/conf 
sudo vim hbase-env.sh
```
追加配置内容到hbase-env.sh中，并保存退出。
```bash
export JAVA_HOME=/usr/java/jdk1.8.0_141 
export HBASE_MANAGES_ZK=true
export HBASE_CLASSPATH=/apps/hbase/conf
```
JAVA_HOME为java程序所在位置；
HBASE_MANAGES_ZK表示是否使用HBase自带的zookeeper环境；
HBASE_CLASSPATH指向hbase配置文件的路径。


进入配置文件
```bash
sudo vim hbase-site.xml
```

在里面添加
```xml
 <property>
    <name>hbase.master</name>
    <value>localhost</value>
  </property>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://localhost:9000/hbase</value>
  </property>
<property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>localhost</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/data/tmp/zookeeper-hbase</value>
  </property>
```
hbase.master：HBase主节点地址。
hbase.rootdir：HBase文件所存储的位置。
hbase.cluster.distributed：HBase是否为分布式模式。
hbase.zookeeper.quorum：这里是配置ZooKeeper的服务器的地方。
hbase.zookeeper.property.dataDir：这里表示HBase在ZooKeeper上存储数据的位置。

```bash
sudo mkdir -p /data/tmp/zookeeper-hbase
```
.使用vim编辑/apps/hbase/conf/regionservers文件，此文件存储了HBase集群节点的ip地址，目前只有一台节点，所以只需要填写localhost即可。
```bash
vim /apps/hbase/conf/regionservers
进入应该只有localhost，所以不用改
```



















