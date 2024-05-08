# 空间
**Box（连续空间）**： 
```python
# 创建一个 2x2 的连续空间
observation_space = gym.spaces.Box(low=0, high=255, shape=(2, 2), dtype=np.uint8)
observation = observation_space.sample()
```
![](images/Pasted%20image%2020240425205145.png)

**Discrete（离散空间）**：
```python
# 创建一个包含3个离散动作的空间  
action_space = gym.spaces.Discrete(3)   
action = action_space.sample()
```
![](images/Pasted%20image%2020240425205237.png)

**MultiDiscrete（多离散空间）**：
```python
# 创建一个多离散空间，每个维度的范围分别是 [0, 1, 2]
multi_discrete_space = gym.spaces.MultiDiscrete([3, 3, 3])  
# 生成一个随机的多离散观察结果  
observation = multi_discrete_space.sample()
```
![](images/Pasted%20image%2020240425205340.png)


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
![[images/Pasted image 20230627001046.png]]

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




