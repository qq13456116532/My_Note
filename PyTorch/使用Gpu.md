对模型使用 cuda()方法，对损失函数使用cuda()方法并赋值
Dataloader取出的数据也要使用cuda()方法进行赋值

首先要判断 torch.cuda.is_available()


也可以设置device，然后对上面这些使用to()函数

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

# 使用步骤：
- 使用上面的代码创建出cuda
- 在创建神经网络时将网络移动到GPU上
```python
self.dqn_net = DQN(self.state_dim, self.action_dim).to(self.device)
```
- 然后在使用神经网络时，将输入数据也先放入GPU：
```python
state = torch.FloatTensor(state).to(self.device)  
Q_values = self.dqn_net(state)
```
- 使用完后，然后GPU上只能存储Tensor，如果需要变成numpy类型，需要先放入CPU
```python
state = torch.FloatTensor(state).to(self.device)  
Q = self.dqn_net(state).cpu().detach().numpy()
```

