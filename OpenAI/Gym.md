目前发现python3.9，gym0.26是可以配合使用的。



如果不知道需要的是哪个游戏，那么可以使用： 

```python
import gym  
  
for item in gym.envs.registry.keys():  
	print(item)
```








先来一段代码：

```python
import gym  
  
env = gym.make('CartPole-v1',render_mode='human')  
# env = gym.make('CartPole-v1',render_mode="rgb_array")  
  
env.reset()  
env.render()
env.step(env.action_space.sample())
```

`reset()`为重新初始化函数， 在强化学习算法中，智能体需要⼀次次地尝试并累积经验，然后从经 验中学到好的动作。每⼀次尝试我们称之为⼀条轨迹或⼀个episode，每次 尝试都要到达终⽌状态。⼀次尝试结束后，智能体需要从头开始，这就需 要智能体具有重新初始化的功能。函数reset（）就是⽤来做这个的。
在源码中：
![](images/Pasted%20image%2020230726225729.png)利⽤均匀随机分布初始化环境的状态

`render()`基于环境初始化时设置的`render_mode`属性来决定应该如何进行渲染。不同的环境支持的渲染模式可能会有所不同，以下是一些常见的`render_mode`选项：
- `None`（默认）：不计算渲染帧。
- `human`：渲染函数返回`None`，环境会持续地在当前的显示设备或终端中进行渲染，通常用于人类用户观看。
- `rgb_array`：返回一个表示环境当前状态的帧。帧是一个形状为`(x, y, 3)`的numpy.ndarray，表示一个x乘y像素的图像的RGB值。
- `rgb_array_list`：返回一个列表，包含自上次重置以来环境状态的所有帧。每个帧的格式同`rgb_array`。
- `ansi`：返回一个字符串或者`StringIO.StringIO`对象，包含每个时间步的终端风格的文本表示。这个文本可以包含换行符和ANSI转义序列（例如用于表示颜色）。


step（）函数在仿真器中扮演物理引擎的⾓⾊。其输⼊是动作a，输出是：下 ⼀步状态、⽴即回报、是否终⽌、调试项。它描述了智能体与环境交互的 所有信息，是环境⽂件中最重要的函数。在本函数中，⼀般利⽤智能体的 运动学模型和动⼒学模型计算下⼀步的状态和⽴即回报，并判断是否达到 终⽌状态。
最新的基本都是返回这五个值：
![](images/Pasted%20image%2020230726233704.png)



# Breakout 打砖块
基础代码:

```python
    env = gym.make('Breakout-ramNoFrameskip-v4',render_mode='human')
    o = env.reset()
    while True:
        env.render()
        a = env.action_space.sample()
        observation,reward,terminated,truncated,info=env.step(a)
        if(terminated):
            break
    env.close()
```
如果人类观看使用`human`模式，训练则使用`rgb_array`
游戏 id 中的 NoFrameskip 意味着没有跳帧和动作重复，而 v4 意思是当前 为第 4 个版本
产生的效果如下：
![](images/Pasted%20image%2020230815222721.png)
































