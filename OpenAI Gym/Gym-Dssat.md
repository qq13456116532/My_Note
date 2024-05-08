



# Gym-Dssat
### Linux构建
这边可以使用WSL来安装。


安装完成后是这样：
![](images/Pasted%20image%2020231219160014.png)
然后就可以使用下面pycharm连接Linux的方式连接到WSL里的环境。

直接去 [With packages (recommended) — gym-dssat documentation (inria.fr)](https://rgautron.gitlabpages.inria.fr/gym-dssat-docs/Installation/packages.html)
然后运行里面的命令即可创建一个有该环境的Linux

##### pycharm连接Linux
这里注意不能开启代理，否则wsl将连不上

可以参考[利用pycharm调试ssh远程程序并实时同步文件的操作方法](https://www.jb51.net/article/268644.htm)

首先Linux打开SSH，然后修改root密码，让外部能用root登陆，都在`Ubuntu/基础使用` 里面有写，然后pycharm里面配置Ubuntu的地址，后面就配置一下Python Interceptor，注意必须是虚拟环境的python，即 `bin/python3`
![](images/Pasted%20image%2020230925205735.png)
像上面这样，选择 Existing而不是New

##### 设置环境变量
因为在Ubuntu中使用时需要执行activate脚本，然后查看里面时发现需要设置环境变量，所以需要设置过才能执行代码。

1. 打开 `Run/Debug Configurations` 对话框。
![](images/Pasted%20image%2020230928170041.png)
    - 你可以通过点击顶部工具栏中的运行配置下拉列表旁边的 `Edit Configurations...` 来找到它。
2. 在左侧，选择你的运行/调试配置。
![](images/Pasted%20image%2020230928170129.png)
3. 在右侧，找到 `Environment variables` 字段。
4. 点击环境变量字段旁边的按钮（看起来像一个表格）。
5. 在弹出的对话框中，添加你需要的环境变量。
    - 对于 `GYM_DSSAT_PDI_PATH`，你可能需要设置值为 `/opt/gym_dssat_pdi/lib/python3.10/site-packages/gym_dssat_pdi`。
    - 对于 `PATH`，你可能需要添加 `/opt/dssat_pdi` 到现有的 `PATH` 值。
    - ![](images/Pasted%20image%2020230928170259.png)
6. 点击 `OK`。

或者在使用python console时
```python
os.environ["GYM_DSSAT_PDI_PATH"] = "/opt/gym_dssat_pdi/lib/python3.10/site-packages/gym_dssat_pdi"
os.environ["PATH"] = "/opt/dssat_pdi"
os.environ["VIRTUAL_ENV"] = "/opt/gym_dssat_pdi"
```


但是上面两种好像都不太有用，所以准备在pycharm里面启动虚拟环境：
![](images/Pasted%20image%2020231004103541.png)



再填写以下字段：
- **Name**：输入一个名称，例如"Activate Virtual Environment"。
- **Program**：输入`source`（或完整路径，如果`source`不在`$PATH`中）
- **Arguments**：输入`/opt/gym_dssat_pdi/bin/activate`（即您的虚拟环境激活脚本的路径）。
- **Working directory**：可以设置为您项目的根目录或保持为空。
如下
![](images/Pasted%20image%2020231004104041.png)


好像都不行，就只能在Pycharm里面使用自动同步代码，然后ssh的terminal去Ubuntu里面激活环境执行代码了。
![](images/Pasted%20image%2020231004185051.png)


最终发现设置这样的环境变量就可以了：
```python
GYM_DSSAT_PDI_PATH=/opt/gym_dssat_pdi/lib/python3.10/site-packages/gym_dssat_pdi;PATH=/opt/dssat_pdi:/opt/gym_dssat_pdi/bin;VIRTUAL_ENV=/opt/gym_dssat_pdi
```
![](images/Pasted%20image%2020231005093200.png)

##### 设置自动同步文件
![](images/Pasted%20image%2020230928170541.png)
然后这里设置为`Always`即可
![](images/Pasted%20image%2020230928170607.png)
但是会看到下面出现警告
![](images/Pasted%20image%2020230928170909.png)去把mapping映射修改一下：
![](images/Pasted%20image%2020230928171407.png)
然后就出现这样的东西：
![](images/Pasted%20image%2020230928171501.png)
upload一下就可以了


### docker构建

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
在Linux的VSC中则是这样：
![](images/Pasted%20image%2020230920145534.png)
然后如果需要下载某些环境，那么在这里面使用 pip install






### 环境基础


这个环境的状态的stage有如下几种
施肥的istage
![](images/Pasted%20image%2020230922145619.png)

灌溉的istage：
![](images/Pasted%20image%2020230922145601.png)
但是也有istage=7的情况，我猜测是什么都没达到


他的观察observation是：
```python
{
    'cumsumfert': 0.0,  # 累积氮肥施用量（kg/ha）
    'dap': 0,           # 种植后的天数（day）
    'dtt': 0.0,         # 当天的生长度日（°C/day）
    'ep': 0.0,          # 实际植物蒸腾速率（L/m2 /day）
    'grnwt': 0.0,       # 粮食重量干物质（kg/ha）
    'istage': 7,        # DSSAT作物生长阶段（文档未提供istage 7的具体含义）
    'nstres': 0.0,      # 植物氮应力指数（无单位）
    'rtdep': 0.0,       # 根深（cm）
    'srad': 8.65,       # 当天的太阳辐射（MJ/m2 /day）
    'sw': array([...]), # 各土壤层的体积土壤水含量（cm3 [water] / cm3 [soil]）
    'swfac': 0.0,       # 植物水应力指数（无单位）
    'tmax': 18.56,      # 当天的最高温度（°C）
    'topwt': 0.0,       # 地上部分的生物量（kg/ha）
    'totir': 0.0,       # 总灌溉水量（L/m2）
    'vstage': 0.0,      # 植物营养生长阶段（叶片数）
    'wtdep': 0.0,       # 地下水表深度（cm）
    'xlai': 0.0         # 植物种群叶面积指数（m2 叶/m2 土壤）
}

```


一个episode就是一个生长季。一般160天
A simulation starts prior to planting and ends at crop harvest
最终状态是 crop maturity，作物成熟，也有failure
agent每天都要决定 施肥或灌溉
开始日期和开始种植日期差一个月，
各种变量意义在[Files · stable · GAUTRON Romain / gym_dssat_pdi_project · GitLab (inria.fr)](https://gitlab.inria.fr/rgautron/gym_dssat_pdi/-/tree/stable)

它的奖励塑造旨在促进代理人学习，并引导政策实现理想的权衡，如最大限度地提高粮食产量和最大限度地减少诱发污染

默认的施肥返回函数：
![](images/Pasted%20image%2020230921215934.png)
$trnu$是第t和t+1间从环境中得到的，anfer是第t天施肥的数量。
前一天有施肥的情况，也会有一定的惩罚。
灌溉问题就是每天提供一个数量的水
范围是这样![](images/Pasted%20image%2020230922084624.png)

观察空间：
- istage DSSAT maize growing stage
- **vstage**: 营养生长阶段（叶子数量）  
    Translates to: Vegetative growth stage (number of leaves)
- **topwt**: 地上部分种群生物量 (kg/ha)  
    Translates to: Above the ground population biomass (kg/ha)
- **grnwt**: 粮食干重 (kg/ha)  
    Translates to: Grain weight dry matter (kg/ha)
- **swfac**: 植物水分胁迫指数 (无单位)  
    Translates to: Index of plant water stress (unitless)
- **nstres**: 植物氮胁迫指数 (无单位)  
    Translates to: Index of plant nitrogen stress (unitless)
- **xlai**: 植物种群叶面积指数 (m² 叶/m² 土壤)  
    Translates to: Plant population leaf area index (m² leaf/m² soil)
- **dtt**: 当日积温 (°C/天)  
    Translates to: Growing degree days for the current day (°C/day)
- **dap**: 种植后天数 (天)  
    Translates to: Days after planting (day)
- **cumsumfert**: 累计氮肥施用量 (kg/ha)  
    Translates to: Cumulative nitrogen fertilizer applications (kg/ha)
- **rain**: 当日降水量 (L/m²/天)  
    Translates to: Rainfall for the current day (L/m²/day)
- **ep**: 实际植物蒸腾速率 (L/m²/天)  
    Translates to: Actual plant transpiration rate (L/m²/day)
![](images/Pasted%20image%2020230922085135.png)


在这个模型或模拟中，播种日期是固定的，大约在模拟开始后一个月。模拟的收益（或回报）有两个组成部分，一个针对施肥子问题，另一个针对灌溉子问题。这是一个多目标问题，也就是说，决策主体需要同时考虑施肥和灌溉两个方面以最大化总体收益。
最后，该问题的默认观测空间（用于决策的信息集）是施肥问题和灌溉问题各自观测空间的合集。换句话说，决策主体可以从施肥和灌溉两个方面获取的所有信息中进行决策。


我们将代理（agent）的目标函数定义为**未折扣的收益之和**
因为PPO既可以解决连续，也可以解决离散问题，所以论文中使用PPO来做个示例
他们做了三个策略：
- 无策略，不施肥
- 按天数施肥
- PPO得到的策略，未调整超参数，使用随机天气

这是变量，对于 - ，虽然没有直接提供，但是很容易获得
![](images/Pasted%20image%2020230922093921.png)
对于一个施肥策略 $π$, 使用 ${grnwt}^π$作为干物质粮食产量，  ${grnwt}^0$是不施肥的产量,${cumsumfert}^π$作为 $π$的施肥总量，那么就有等式
![](images/Pasted%20image%2020230922093439.png)
这里ANE（农艺氮使用效率）表示与没有施肥的政策（即空政策）相比，每单位氮肥带来的粮食产量响应。最大化ANE与经济和环境方面有关，这将导致肥料使用的高效，从而降低污染风险。–a key metric of sustainable fertilization

这是三种策略的比较，![](images/Pasted%20image%2020230922094003.png)
PPO最终具有最高的平均累积回报，PPO的累积回报比专家政策的变化要小
我们已经证明，通过使用现成的Stable-Baselines3 PPO实现，我们能够学习到一个相关的施肥政策，该政策在目标函数方面略微优于专家施肥政策，通过适当的调整，PPO的性能可能会有所提升。

但是也有些需要做的事情：
- 一个代理（agent）所学习的施肥政策仍然需要可解释性。例如，发现哪些是决定施肥应用的最重要的观察变量，它们的值如何影响施肥，这样才能在实际农业环境中更好使用。
- 每次田间观察都是有成本的，像氮吸收量就需要破坏性的植物采样和实验室分析，所以观察的测量成本和观察频率都要为了实际应用最小化
- 减少需要的样本来解决这个问题，研究者也应该设计一个有改进效率的RL算法

前面的示例讲的是施肥，灌溉的示例如下：
灌溉用例遵循与施肥用例相同的方法（三种策略）。它只在观察空间（如下表）和回报函数上与施肥用例有所不同，
![](images/Pasted%20image%2020230922103322.png)

$topwt(t,t+1)$是指时间t和t+1之间地上生物量的变化，用$amir(t)$表示第t天的灌溉水量, 灌溉的返回函数是：
![](images/Pasted%20image%2020230922103651.png)
然后也是使用三种策略，零策略，专家策略和通过PPO学习的策略。
- 零策略： 从不进行灌溉的策略，相当于依赖于雨水灌溉的农作物，将灌溉政策的效果作为相对于这一空策略的性能增益来衡量，以便将灌溉的效果与降水的效果分离
- 专家政策： 这一政策包括十六个确定性的灌水，仅取决于种植后的天数![](images/Pasted%20image%2020230922104143.png)这里的DAP是Day After Plant，这只是一个简化，实际中的专家肯定要多方面考虑。
- 通过PPO学习的策略

对于一个**灌溉策略** $π$, 使用 ${grnwt}^π$作为干物质粮食产量，  ${grnwt}^0$是不施肥的产量，${totir}^π$是总的灌溉水量，更多的可以看如下表
![](images/Pasted%20image%2020230922104758.png)

然后水的利用效率使用WUE，![](images/Pasted%20image%2020230922104918.png)

三种策略的结果差不多是这样
![](images/Pasted%20image%2020230922110333.png)
PPO在维持与专家政策相当的平均谷物产量的同时，展示了极大的效率优势
这些结果进一步强调了使用强化学习算法在农业管理中的潜力，尤其是在需要平衡产量、资源效率和环境可持续性等多个目标时。PPO不仅能减少水和肥料的使用，还能在一定程度上减少环境污染，这对于实现更可持续的农业系统来说是非常重要的。



### 代码

##### 初始化 
要使用dssat的环境，需要开启虚拟环境：
```shell
source /opt/gym_dssat_pdi/bin/activate
```
![](images/Pasted%20image%2020230922144354.png)

然后使用作者提供的三种代码时，需要先下载包：
```python
pip install stable_baselines3
```
它使用的sb3的官网在这： [稳定基线3 文档 - 可靠的强化学习实现 — 稳定基线3 2.2.0a6 文档 (stable-baselines3.readthedocs.io)](https://stable-baselines3.readthedocs.io/en/master/)
这里发现下载sb3需要的内存空间非常大，所以建议一开始创建虚拟机就使用50G磁盘

代码位置是：[GAUTRON Romain / gym_dssat_pdi_baselines · GitLab (inria.fr)](https://gitlab.inria.fr/rgautron/gym_dssat_pdi_baselines/-/tree/main/)


##### 基础代码：

```python
import gym
env_args = {
     'mode': 'fertilization', # irrigation，fertilization，all三选一
     'seed': 123,
     'random_weather': True,
}
env = gym.make('gym_dssat_pdi:GymDssatPdi-v0', **env_args)
env.reset()

action = {'anfer':0} # nitrogen to fertilize in kg/ha，是fertilization、all选择
state,reward,done,info = env.step(action)
sum = 0
while True:
     state,reward,done,info = env.step(action)
     print(reward)
     if(reward!=None):
          sum = sum+reward
     if done:
          print("ends,the return is :"+str(sum))
          # print(state) # the last state must be None
          break
env.render(type='reward', cumsum=True)  #会生成一个pdf
```
这是论文中写的Null policy，即全程都不施肥，自己长
这里要注意的是在done为True的那一刻，state和reward返回的都是None。

##### action
动作有两种种类，动作是以字典的形式提供给环境的
动作空间：![](images/Pasted%20image%2020230922084624.png)

`anfer` 是施肥量（单位：kg/ha），`amir` 是灌溉量（单位：L/ha）
```python
action_dict = {
    'anfer': 5,  # if mode == fertilization or mode == all ; nitrogen to fertilize in kg/ha
    'amir': 10,  # if mode == irrigation or mode == all ; water to irrigate in L/ha
}
```

##### 终止环境

使用
```python
try:
  env = gym.make(...)
  ...
  env.step(action_dict=...)
  ...
finally:
  env.close()

```
来终止，不再使用该环境。

如果还需要使用，那么使用下面这个：
一旦作物被收割（即 `env.done == True`），需要使用 `env.reset()` 重置环境

但是如果你没有使用env.close()，可能造成**危险**的情况！！！可能导致僵尸进程，这些进程可能会干扰新实例并引发错误。如果出现这种情况，你可以手动终止进程，使用`pkill -9 dscsm048`
所以建议使用finally强制close()

##### 设置种子seed
看到前面的`env_args`有seed这个参数，一般来说是为了可再现性，不需要这点也可以不设置。
```python
env.set_seed(seed)
```


##### Histories属性
是一个字典，用于在每个情节（episode）期间填充，并在 `env.reset()` 时清空。它对于分析代理的性能非常有用
使用 `env.history`获得：
```python
{'observation': [...], 'action': [...], 'reward': [...]}
```


##### 渲染Render
这个环境既为state提供了可视化，也为reward提供了可视化。
**状态（State）可视化**：
```python
env.render(type='ts',  # time series mode
        feature_name_1='nstres',  # mandatory first raw state variable, here the nitrogen stress factor (unitless)
        feature_name_2='grnwt')  # optional second raw state variable, here the grain weight (kg/ha)
```
这里ts是指按实际顺序，必须提供第一个原始状态变量，像这里就是氮应力因子，第二个可以不提供，这里也提供了是粮食重量，
结果如下![](images/Pasted%20image%2020230922161901.png)

**奖励（Reward）可视化**：
- 使用 `env.render(type='reward')` 可以快速查看奖励。
- 添加 `cumsum=True` 参数，
```python
env.render(type='reward', cumsum=True)  # if you want cumulated rewards
```
可以可视化累积奖励。
结果分别如下：
![](images/Pasted%20image%2020230922162049.png)
![](images/Pasted%20image%2020230922162058.png)









# SB3问题
在使用stable-baseline3时，发现会报错，
问题是原来需要使用gym的BOX类型，现在要改成 gymnasium的类型
![](images/Pasted%20image%2020231005190709.png)
就是把sb3_wrapper里的`GymDssatWrapper`这个类的动作空间和状态空间修改一下就可以了。
还要在reset()里面添加一个 info，![](images/Pasted%20image%2020231005192228.png)
还要添加一个属性
![](images/Pasted%20image%2020231005194506.png)
在step()方法的返回值中增加一个
![](images/Pasted%20image%2020231005195304.png)

然后这里因为修改了 `sb3_wrapper.py`的内容，所以在写的 `ecaluate()`函数中也需要修改一下。





