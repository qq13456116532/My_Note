
## 安装包 
这里是安装到了conda中
`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple gym`
和
`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pygame`
``
然后会在pycharm中进行运行，因为 jupter notebook中无法使用reder()

然后现在使用的是gym0.26,python是3.10。
gym0.26修改了很多，具体可以看 [Release notes for v0.26.0 by pseudo-rnd-thoughts · Pull Request #3056 · openai/gym · GitHub](https://github.com/openai/gym/pull/3056)


## 入门
现在运行一个入门程序：

```python
import gym  
import time  
  
# 生成环境,包含渲染画面
env = gym.make('CartPole-v1', render_mode='human')  
# 环境初始化  
state = env.reset()  
# 循环交互  
while True:  
# 从动作空间随机获取一个动作  
action = env.action_space.sample()  
# agent与环境进行一步交互  
state, reward, terminated, truncated, info = env.step(action)  
print('state = {0}; reward = {1}'.format(state, reward))  
# 判断当前episode 是否完成  
if terminated:  
print('terminated')  
break  
time.sleep(0.1)  
# 环境结束  
# env.close()

```
运行上面这段代码，会产生如下界面：
![[Pasted image 20230627001046.png]]

第一个程序就运行完成了。


# box2d 下载错误
如果出现这个错误

```python
error: Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools": https://visualstudio.microsoft.com/visual-cpp-build-tools/
```

**安装Microsoft C++ 生成工具 - Visual(视觉) Studio**：[https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/](https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/)

![](images/Pasted%20image%2020230821152151.png)
然后勾选使用c++的桌面开发
![](images/Pasted%20image%2020230821152205.png)
等待下载安装成功，然后就可以了。



# Gym-Dssat
这个其实不是OpenAi官方的环境。
使用方法：

1. 先去[GAUTRON Romain / gym_dssat_pdi_project · GitLab (inria.fr)](https://gitlab.inria.fr/rgautron/gym_dssat_pdi/-/tree/stable/)将代码下载下来。

2. 然后打开`\docker_recipes`这个文件夹，CMD转到该文件夹下，使用

```shell
docker build . -t "gym-dssat:debian" -f Dockerfile_Debian_Bullseye
```

​    语句来创建镜像

3. 然后使用

```shell
docker run --name gym_dssat -d -it gym-dssat:debian
```

​    来启动容器。
![](images/Pasted%20image%2020230918210711.png)
这样就是启动成功了，在Docker-Desktop里面也可以看到：
![](images/Pasted%20image%2020230918210739.png)



然后进入容器安装这个环境需要的包：

```python
pip install gym numpy matplotlib jinja2 pyzmq
```

然后发现其实都安装好了。

然后需要把安装环境的代码放到这个容器里去。
![](images/Pasted%20image%2020230918214109.png)
在docker-desktop查看文件，可以看到里面已经有需要的文件了![](images/Pasted%20image%2020230918214146.png)


所以直接执行
```python
source /opt/gym_dssat_pdi/bin/activate
```
![](images/Pasted%20image%2020230918214308.png)
此时就进入了虚拟环境内部。













