
### 启动用法
这是docker桌面版的用法

打开后，![](images/Pasted%20image%2020230918201727.png)
右上角先登陆一下。




此时，docker就在本地的Windows机器上运行起来了。
可以打开CMD终端，![](images/Pasted%20image%2020230918202327.png)
可以发现，运行成功。


我这里先把DockerFile下载到本地了。然后CMD到那个文件所在的文件夹。
![](images/Pasted%20image%2020230918204435.png)

然后使用命令
```shell
docker build . -t "gym-dssat:debian" -f Dockerfile_Debian_Bullseye
```
来创建镜像image

然后使用
```shell
docker run gym-dssat:debian
```
来启动容器


### 修改配置
如果需要修改Docker-Engine的配置，比如要修改CPU占用率无上限，可以这样：

```python
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "resources": {
    "cpu": {
      "realtimeRuntime": 0
    }
  }
}

```
`realtimeRuntime` 被设置为 0，这意味着 CPU 的使用没有上限。但是注意，这样做可能会导致 CPU 资源过度使用





























