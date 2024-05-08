LSTM全称 Long Short-Term Memory

Recurrent network的应用主要如下两部分：
1. 文本相关。主要应用于自然语言处理（NLP）,已经被ChatGPT爆了  
2. 时序相关。就是时序预测问题（timeseries），诸如预测天气、温度、包括个人认为根本不可行的但是很多人依旧在做的预测股票价格问题
然后强化学习不是完全可见的环境需要使用到历史观测也需要用到。
特点就是**有先后顺序的概念**

与其相反的全连接网络Fully-Connected Network和卷积神经网络，就是缺乏记忆，一条影评里的每一个字经过word embedding后，被当成了一个独立的个体输入到网络中；网络不清楚之前的，或者之后的文字是什么。这样的网络，我们称为**feedforward network**

但是在真实情况中，我们理解一段文字的信息的时候，每个文字并不是独立的，我们的脑海里也有它的上下文。比如当你看到这段文字的时候，你还记得这篇文章开头表达过一些关于LSTM的信息；所以，我们在脑海里维护一些信息，这些信息随着我们的阅读不断的更新，帮助我们来理解我们所看到的每一个字，每一句话。这就是RNN的做法：**维护一些中间状态信息**。


# 简单RNN
RNN就是 **Recurrent Neural Network**
实现了我们来维护中间信息，记录之前看到信息这样最简单的一个概念的模型
![](images/Pasted%20image%2020231020152208.png)
或者是代码：
```python
state_t = 0 #时刻t的状态
for input_t in input_sequence: # 在timesteps上loop
    output_t = f(input_t, state_t) # input_t state_t得到时刻t输出
    state_t = output_t # 用当前输出去更新内部状态
```
每一个时刻t，RNN会考虑当前时刻t 的状态_state_，以及当前时刻t 的输入(维度是`(input_features,)`),然后总和得到在时刻t的输出。并且为当前时刻t的输出去更新状态_state_。

展开来就是这样：
![](images/Pasted%20image%2020231020152812.png)

但是它的缺点是不能处理long sequence/timeseries问题。原因是**梯度消失**，网络几乎不可训练。所以也只是理论上可以记忆任意长的序列




# LSTM
LSTM就是用来解决RNN中梯度消失问题的，从而可以处理long-term sequences。


我们在SimpleRNN基础上，增加一条传送带（adding a carry track）用来传递信息。传送带上每个时刻的状态我们记为：`c t` c是carry的意思。
![](images/Pasted%20image%2020231020153601.png)
当前时刻的输出就应该收到三个信息的影响：当前时刻的输入、当前时刻的状态、传送带上带来的很久以前的信息
```python
state_t = 0 #时刻t的状态
for input_t in input_sequence: # 在timesteps上loop
    output_t = f(input_t, state_t, C_t) # input_t state_t得到时刻t输出
    state_t = output_t # 用当前输出去更新内部状态
    C_t = g(C_t)
```



那么现在的问题就是两个状态怎么更新：state_t, C_t
RNN内部的状态state_t还是跟之前一样：用上一个时刻的输出来更新

# C_t的更新
根据`input_t, state_t`以及三套**不同的**`W U b`，来计算出三个值：
```text
i_t = activation(dot(state_t, Ui) + dot(input_t, Wi)+ bi)
f_t = activation(dot(state_t, Uf) + dot(input_t, Wf) + bf)
k_t = activation(dot(state_t, Uk) + dot(input_t, Wk) + bk)
```
然后组合这三个值来更新`C_t`： `c_t+1 = i_t * k_t + c_t * f_t`

 1. `c_t * f_t` 是为了让模型忘记一些不相关的信息，在carry dataflow的时候。即时是很久之前的信息，模型也有不用他的选择权利，所以模型要有忘记不相关信息的能力
  2. `i_t * k_t` 为模型提供关于当前时刻的信息，给carry track增加一些新的信息。

一个忘记不相关信息，一个增加新的信息，然后再carry track向下传递这个c_t

### 优缺点
1. 优点。解决了SimpleRNN梯度消失的问题，可以处理long-term sequence
2. 缺点。计算复杂度高，想想谷歌翻译也只是7-8层LSTM就知道了；自己跑代码也有明显的感觉，比较慢。

















