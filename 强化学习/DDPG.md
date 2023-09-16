![](images/Pasted%20image%2020230915181441.png)
如果当前状态是 s，那么价值网络的打分就 是：![](images/Pasted%20image%2020230915181511.png)
我们将它的期望作为目标函数，为了使其最大化
![](images/Pasted%20image%2020230915181611.png)

可以用梯度上升来增大 J(θ)。每次用随机变量 S 的一个观测值（记作 sj）来计算梯 度
![](images/Pasted%20image%2020230915181632.png)
可以用链式法则求出梯度$g_j$
![](images/Pasted%20image%2020230915181804.png)
根据这个来更新$θ$ 
对Critic的更新就使用TD


