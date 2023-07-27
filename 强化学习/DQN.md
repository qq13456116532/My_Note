![](images/Pasted%20image%2020230725215940.png)

# 解决平衡车
![[Pasted image 20230711195135.png]]
深度Q学习（Deep Q-Learning，DQN）的强化学习算法来解决 "CartPole-v1" 这个 Gym 环境下的控制问题。

```python
#定义环境
class MyWrapper(gym.Wrapper):

    def __init__(self):
        env = gym.make('CartPole-v1', render_mode='rgb_array')
        super().__init__(env)
        self.env = env
        self.step_n = 0

    def reset(self):
        state, _ = self.env.reset()
        self.step_n = 0
        return state

    def step(self, action):
        state, reward, terminated, truncated, info = self.env.step(action)
        done = terminated or truncated
        self.step_n += 1
        if self.step_n >= 200:
            done = True
        return state, reward, done, info
env = MyWrapper()
```
`MyWrapper` 类，封装了基本的 Gym 环境，并添加了一些额外的功能。例如，在每一步中记录游戏步骤数量，并设定了最大步骤限制，防止出现无限循环之类的问题

```python
import torch

#计算动作的模型,也是真正要用的模型
model = torch.nn.Sequential(
    torch.nn.Linear(4, 128),
    torch.nn.ReLU(),
    torch.nn.Linear(128, 2),
)
#经验网络,用于评估一个状态的分数
next_model = torch.nn.Sequential(
    torch.nn.Linear(4, 128),
    torch.nn.ReLU(),
    torch.nn.Linear(128, 2),
)

#把model的参数复制给next_model
next_model.load_state_dict(model.state_dict())
```
`model` 是用来计算Q值的主网络，即`value`，`next_model` 是用来计算Q目标的辅助网络，即`target`。这两个网络的结构相同，且 `next_model` 的参数定期从 `model` 中更新。这种做法可以防止目标值过快更新，从而增加训练的稳定性。

```python
import random

#得到一个动作
def get_action(state):
    if random.random() < 0.01:
        return random.choice([0, 1])

    #走神经网络,得到一个动作
    state = torch.FloatTensor(state).reshape(1, 4)

    return model(state).argmax().item()
```
model(state)是一个大小为 2 的向量，因为模型对两个可能的动作（`action=0` 和 `action=1`）进行了预，`.argmax()`: 这个函数返回的是具有最大值的索引，也就是模型认为最佳的动作，`.item()`: 这个函数将一个只包含一个元素的张量转化为 Python 标准的数据类型，这是为了能够在 Python 中直接使用模型的预测结果。

```python

#样本池
datas = []
#向样本池中添加N条数据,删除M条最古老的数据
def update_data():
    old_count = len(datas)

    #玩到新增了N个数据为止
    while len(datas) - old_count < 200:
        #初始化游戏
        state = env.reset()

        #玩到游戏结束为止
        over = False
        while not over:
            #根据当前状态得到一个动作
            action = get_action(state)

            #执行动作,得到反馈
            next_state, reward, over, _ = env.step(action)

            #记录数据样本
            datas.append((state, action, reward, next_state, over))

            #更新游戏状态,开始下一个动作
            state = next_state

    update_count = len(datas) - old_count
    drop_count = max(len(datas) - 10000, 0)

    #数据上限,超出时从最古老的开始删除
    while len(datas) > 10000:
        datas.pop(0)

    return update_count, drop_count

```
更新神经网络肯定是需要使用最新的模型进行游戏的样本。
```python
#获取一批数据样本
def get_sample():
    #从样本池中采样
    samples = random.sample(datas, 64)
    #[b, 4]
    state = torch.FloatTensor([i[0] for i in samples]).reshape(-1, 4)
    #[b, 1]
    action = torch.LongTensor([i[1] for i in samples]).reshape(-1, 1)
    #[b, 1]
    reward = torch.FloatTensor([i[2] for i in samples]).reshape(-1, 1)
    #[b, 4]
    next_state = torch.FloatTensor([i[3] for i in samples]).reshape(-1, 4)
    #[b, 1]
    over = torch.LongTensor([i[4] for i in samples]).reshape(-1, 1)
    return state, action, reward, next_state, over
```
采样数据样本进行神经网络的更新。

```python
def get_value(state, action):
    #使用状态计算出动作的logits
    #[b, 4] -> [b, 2]
    value = model(state)

    value = value.gather(dim=1, index=action)
    return value
```
这是获取这一批state的价值value。
dim=1（即第二个维度）上进行操作，index=action 表示我们需要提取的索引就是实际执行的动作。所以，这一步的结果 value 的形状是 [b, 1]，表示每个实例执行的动作的预测价值。

```python
def get_target(reward, next_state, over):
    #上面已经把模型认为的状态下执行动作的分数给评估出来了
    #下面使用next_state和reward计算真实的分数
    #针对一个状态,它到底应该多少分,可以使用以往模型积累的经验评估
    #这也是没办法的办法,因为显然没有精确解,这里使用延迟更新的next_model评估

    #使用next_state计算下一个状态的分数
    #[b, 4] -> [b, 2]
    with torch.no_grad():
        target = next_model(next_state)

    #取所有动作中分数最大的
    #[b, 2] -> [b, 1]
    target = target.max(dim=1)[0]
    target = target.reshape(-1, 1)

    #下一个状态的分数乘以一个系数,相当于权重
    target *= 0.98

    #如果next_state已经游戏结束,则next_state的分数是0
    #因为如果下一步已经游戏结束,显然不需要再继续玩下去,也就不需要考虑next_state了.
    #[b, 1] * [b, 1] -> [b, 1]
    target *= (1 - over)

    #加上reward就是最终的分数
    #[b, 1] + [b, 1] -> [b, 1]
    target += reward

    return target
```
`get_target()`函数根据Q-learning的更新公式计算了目标Q值。这个目标Q值将用于后续的训练过程，通过优化预测Q值与目标Q值之间的差距，不断地更新模型参数，使模型的预测结果越来越接近实际情况。
1. `target.max(dim=1)`: `max()`函数返回一个给定张量中的最大值。参数`dim=1`表示沿着第二个维度（即行）取最大值，也就是说，在每一行中找到最大值。在PyTorch中，维度从0开始计数，所以第二个维度实际上是张量的行。这个函数会返回两个结果，一个是最大值，另一个是最大值对应的索引。
    
2. `[0]`: `max()`函数返回两个结果，最大值和对应的索引，而这里我们只关心最大值，不关心最大值的位置，所以我们通过`[0]`来获取最大值。


```python
def train():
    model.train()
    optimizer = torch.optim.Adam(model.parameters(), lr=2e-3)
    loss_fn = torch.nn.MSELoss()

    #训练N次
    for epoch in range(500):
        #更新N条数据
        update_count, drop_count = update_data()

        #每次更新过数据后,学习N次
        for i in range(200):
            #采样一批数据
            state, action, reward, next_state, over = get_sample()

            #计算一批样本的value和target
            value = get_value(state, action)
            target = get_target(reward, next_state, over)

            #更新参数
            loss = loss_fn(value, target)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()

            #把model的参数复制给next_model
            if (i + 1) % 10 == 0:
                next_model.load_state_dict(model.state_dict())
train()
torch.save(model, 'model_dqn.pth')
```
然后可以使用上面这个训练好的模型来玩游戏

```python
import random  
  
import gym  
import torch  
  
def get_action(state):  
if random.random() < 0.01:  
return random.choice([0, 1])  
  
#走神经网络,得到一个动作  
state = torch.FloatTensor(state).reshape(1, 4)  
  
return model(state).argmax().item()  
  
  
model = torch.load('model_dqn.pth')  
  
env = gym.make('CartPole-v1', render_mode='human')  
#初始化游戏  
state = env.reset()  
state = state[0]  
print("state before reshaping:", state)  
#记录反馈值的和,这个值越大越好  
reward_sum = 0  
#玩到游戏结束为止  
over = False  
while not over:  
# 根据当前状态得到一个动作  
action = get_action(state)  
  
# 执行动作,得到反馈  
state, reward, over, *_ = env.step(action)  
reward_sum += reward  
print("reward_sum:", reward_sum)
```


# 解决倒立摆
倒立摆问题和平衡车问题的区别是，这里的state是3维向量，action是 `Box(-2.0, 2.0, (1,), float32)`，即-2到2 的连续值，平衡车是0或1

```python
import gym
#定义环境
class MyWrapper(gym.Wrapper):
    def __init__(self):
        env = gym.make('Pendulum-v1', render_mode='rgb_array')
        super().__init__(env)
        self.env = env
        self.step_n = 0

    def reset(self):
        state, _ = self.env.reset()
        self.step_n = 0
        return state

    def step(self, action):
        state, reward, terminated, truncated, info = self.env.step(action)
        done = terminated or truncated
        self.step_n += 1
        if self.step_n >= 200:
            done = True
        return state, reward, done, info


env = MyWrapper()

env.reset()
import torch
#计算动作的模型,也是真正要用的模型
model = torch.nn.Sequential(
    torch.nn.Linear(3, 128),
    torch.nn.ReLU(),
    torch.nn.Linear(128, 11),
)

#经验网络,用于评估一个状态的分数
next_model = torch.nn.Sequential(
    torch.nn.Linear(3, 128),
    torch.nn.ReLU(),
    torch.nn.Linear(128, 11),
)

#把model的参数复制给next_model
next_model.load_state_dict(model.state_dict())
```
这里是设置了value和target两个模型，target模型，`next_model` 的参数定期从 `model` 中更新。这种做法可以防止目标值过快更新，从而增加训练的稳定性。

```python
import random
def get_action(state):
    #走神经网络,得到一个动作
    state = torch.FloatTensor(state).reshape(1, 3)
    # 网络输出的是一个长度为11的向量，action得到其中最大的那个数值
    action = model(state).argmax().item()

    if random.random() < 0.01:
        action = random.choice(range(11))

    #离散动作连续化，action是0-1
    action_continuous = action
    action_continuous /= 10
    action_continuous *= 4
    action_continuous -= 2

    return action, action_continuous


#样本池
datas = []


#向样本池中添加N条数据,删除M条最古老的数据
def update_data():
    old_count = len(datas)

    #玩到新增了N个数据为止
    while len(datas) - old_count < 200:
        #初始化游戏
        state = env.reset()

        #玩到游戏结束为止
        over = False
        while not over:
            #根据当前状态得到一个动作
            action, action_continuous = get_action(state)

            #执行动作,得到反馈
            next_state, reward, over, _ = env.step([action_continuous])

            #记录数据样本
            datas.append((state, action, reward, next_state, over))

            #更新游戏状态,开始下一个动作
            state = next_state

    update_count = len(datas) - old_count
    drop_count = max(len(datas) - 5000, 0)

    #数据上限,超出时从最古老的开始删除
    while len(datas) > 5000:
        datas.pop(0)

    return update_count, drop_count


#获取一批数据样本
def get_sample():
    #从样本池中采样
    samples = random.sample(datas, 64)

    #[b, 3]
    state = torch.FloatTensor([i[0] for i in samples]).reshape(-1, 3)
    #[b, 1]
    action = torch.LongTensor([i[1] for i in samples]).reshape(-1, 1)
    #[b, 1]
    reward = torch.FloatTensor([i[2] for i in samples]).reshape(-1, 1)
    #[b, 3]
    next_state = torch.FloatTensor([i[3] for i in samples]).reshape(-1, 3)
    #[b, 1]
    over = torch.LongTensor([i[4] for i in samples]).reshape(-1, 1)

    return state, action, reward, next_state, over

def get_value(state, action):
    #使用状态计算出动作的logits
    #[b, 3] -> [b, 11]
    value = model(state)

    #根据实际使用的action取出每一个值
    #这个值就是模型评估的在该状态下,执行动作的分数
    #在执行动作前,显然并不知道会得到的反馈和next_state
    #所以这里不能也不需要考虑next_state和reward
    #[b, 11] -> [b, 1]
    value = value.gather(dim=1, index=action)

    return value
```
这是获取这一批state的价值value。
dim=1（即第二个维度）上进行操作，index=action 表示我们需要提取的索引就是实际执行的动作。所以，这一步的结果 value 的形状是 [b, 1]，表示每个实例执行的动作的预测价值

```python
def get_target(reward, next_state, over):
    #上面已经把模型认为的状态下执行动作的分数给评估出来了
    #下面使用next_state和reward计算真实的分数
    #针对一个状态,它到底应该多少分,可以使用以往模型积累的经验评估
    #这也是没办法的办法,因为显然没有精确解,这里使用延迟更新的next_model评估

    #使用next_state计算下一个状态的分数
    #[b, 3] -> [b, 11]
    with torch.no_grad():
        target = next_model(next_state)

    #取所有动作中分数最大的
    #[b, 11] -> [b, 1]
    target = target.max(dim=1)[0]
    target = target.reshape(-1, 1)

    #下一个状态的分数乘以一个系数,相当于权重
    target *= 0.98

    #如果next_state已经游戏结束,则next_state的分数是0
    #因为如果下一步已经游戏结束,显然不需要再继续玩下去,也就不需要考虑next_state了.
    #[b, 1] * [b, 1] -> [b, 1]
    target *= (1 - over)

    #加上reward就是最终的分数
    #[b, 1] + [b, 1] -> [b, 1]
    target += reward

    return target

```
`get_target()`函数根据Q-learning的更新公式计算了目标Q值。这个目标Q值将用于后续的训练过程，通过优化预测Q值与目标Q值之间的差距，不断地更新模型参数，使模型的预测结果越来越接近实际情况。
1. `target.max(dim=1)`:   `max()`函数返回一个给定张量中的最大值。参数`dim=1`表示沿着第二个维度（即行）取最大值，也就是说，在每一行中找到最大值。在PyTorch中，维度从0开始计数，所以第二个维度实际上是张量的行。这个函数会返回两个结果，一个是最大值，另一个是最大值对应的索引。
    
2. `[0]`:    `max()`函数返回两个结果，最大值和对应的索引，而这里我们只关心最大值，不关心最大值的位置，所以我们通过`[0]`来获取最大值。

```python
def train():
    model.train()
    optimizer = torch.optim.Adam(model.parameters(), lr=1e-2)
    loss_fn = torch.nn.MSELoss()

    #训练N次
    for epoch in range(200):
        #更新N条数据
        update_count, drop_count = update_data()

        #每次更新过数据后,学习N次
        for i in range(200):
            #采样一批数据
            state, action, reward, next_state, over = get_sample()

            #计算一批样本的value和target
            value = get_value(state, action)
            target = get_target(reward, next_state, over)

            #更新参数
            loss = loss_fn(value, target)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()

            #把model的参数复制给next_model
            if (i + 1) % 50 == 0:
                next_model.load_state_dict(model.state_dict())
train()  
torch.save(model, 'model_daolibai.pth')
```
上面是进行训练，然后是测试：

```python
import random

import  gym
import  torch

model = torch.load('model_daolibai.pth')

env = gym.make('Pendulum-v1', render_mode='human')
def get_action(state):
    # 走神经网络,得到一个动作
    state = torch.FloatTensor(state).reshape(1, 3)
    # 网络输出的是一个长度为11的向量，action得到其中最大的那个数值
    action = model(state).argmax().item()

    if random.random() < 0.01:
        action = random.choice(range(11))

    # 离散动作连续化，action是0-1
    action_continuous = action
    action_continuous /= 10
    action_continuous *= 4
    action_continuous -= 2

    return  action_continuous


state = env.reset()
state = state[0]
reward_sum = 0

# print(state)
over = False
step=0
while not over:
    # 根据当前状态得到一个动作
    action = get_action(state)
    print("step:", step)
    step += 1
    # 执行动作,得到反馈
    state, reward, over, *_ = env.step([action])
    reward_sum += reward
print("reward_sum:", reward_sum)

```


## DDQN
Double DQN 是深度强化学习中的一个算法，它是对基础的 DQN（Deep Q-Network）算法的改进。Double DQN 的提出主要是为了解决原始的 DQN 算法中的一个问题，即过估计（overestimation）Q 值的问题。
在原始的 DQN 算法中，我们使用相同的网络既来选择动作又来估计这个动作的 Q 值，这样就会倾向于过估计某些动作的 Q 值。如果对某些动作的 Q 值过估计，那么我们的策略就可能会过于乐观，导致在实际环境中无法达到预期的效果。
在原来的 `get_target`中，是直接取分数最大的：

```python
with torch.no_grad():  
	target = next_model(next_state)
target = target.max(dim=1)[0]
```
现在变成如下，我们使用当前的 Q-network 来选择动作的索引，然后使用另一个目标 Q-network 来估计这个动作的 Q 值。

```python
def get_target(reward, next_state, over):
    with torch.no_grad():
        target = next_model(next_state)

    """以下是主要的Double DQN和DQN的区别"""
    # 取所有动作中分数最大的
    # [b, 11] -> [b]
    # target = target.max(dim=1)[0]

    # 使用model计算下一个状态的分数
    # [b, 3] -> [b, 11]
    with torch.no_grad():
        model_target = model(next_state)

    # 取分数最高的下标
    # [b, 11] -> [b, 1]
    model_target = model_target.max(dim=1)[1]
    model_target = model_target.reshape(-1, 1)

    # 以这个下标取next_value当中的值
    # [b, 11] -> [b]
    target = target.gather(dim=1, index=model_target)
    """以上是主要的Double DQN和DQN的区别"""

    #下一个状态的分数乘以一个系数,相当于权重
    target *= 0.98

    #如果next_state已经游戏结束,则next_state的分数是0
    #因为如果下一步已经游戏结束,显然不需要再继续玩下去,也就不需要考虑next_state了.
    #[b, 1] * [b, 1] -> [b, 1]
    target *= (1 - over)

    #加上reward就是最终的分数
    #[b, 1] + [b, 1] -> [b, 1]
    target += reward

    return target
```
`model_target.max(dim=1)[1]`在张量的第1个维度（即每一行）中找到最大值，`max()`函数返回两个结果：最大值和对应的索引，我们用`[1]`取的是索引。
`model_target.reshape(-1, 1)`：这行代码是将`model_target`张量改变形状，让其每行只有一个元素（即一个动作的索引）
`target = target.gather(dim=1, index=model_target)`：这行代码的作用是从`target`张量中取出与`model_target`中索引对应的元素。这里`dim=1`表示在第1个维度（即每一行）中按照索引取元素，`index=model_target`就是指定索引。取出来的元素就是我们需要的，具有最大Q值的动作对应的`target`值。
















