目前发现python3.9，gym0.26是可以配合使用的。

下面这是gym主要的方法。

```python
env = gym.make('CartPole-v1', render_mode='human')
state = env.reset()  
print('这个游戏的状态用4个数字表示,我也不知道这4个数字分别是什么意思,反正这4个数字就能描述游戏全部的状态')  
print('state=', state)  
#state= [ 0.03490619 0.04873464 0.04908862 -0.00375859]  
  
print('这个游戏一共有2个动作,不是0就是1')  
print('env.action_space=', env.action_space)  
#env.action_space= Discrete(2)  
  
print('随机一个动作')  
action = env.action_space.sample()  
print('action=', action)  
#action= 1  
  
print('执行一个动作,得到下一个状态,奖励,是否结束')  
state, reward, over, _ = env.step(action)  
  
print('state=', state)  
#state= [ 0.02018229 -0.16441101 0.01547085 0.2661691 ]  
  
print('reward=', reward)  
#reward= 1.0  
  
print('over=', over)  
#over= False

```




# 空间类型
`Discrete(n)`
是一种表示离散空间的方式，意味着有 `n` 个不同的可能值，分别是 `[0, 1, 2, ..., n-1]`。也就是说，如果你看到 `Discrete(2)`，这意味着这个空间或者动作有两种可能的值：0 或 1
`Box`: 
这种类型的空间用于表示一个 n 维的盒子，也就是说，每个动作或观察都是一个 n 维的向量，每个维度都有最小值和最大值。例如，如果观察空间是 `Box(0, 1, shape=(3, 4))`，那么每个观察都是一个形状为 3x4 的数组，所有的值都在 0 和 1 之间。
`Tuple`: 
这是一种组合空间，它将其他几种空间结合在一起。例如，如果动作空间是 `Tuple((Discrete(2), Box(0, 1, shape=(1,))))`，那么每个动作都是一个元组，其中包含一个整数（0 或 1）和一个浮点数（在 0 和 1 之间）。








