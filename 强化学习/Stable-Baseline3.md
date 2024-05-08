首先是下载
```python
pip install stable-baselines3[extra]
```

# 基础的训练, 保存 and 加载
导入包
```python
import gymnasium as gym
import numpy as np
from stable_baselines3 import DQN
```
我们选择MlpPolicy是因为月球着陆器的输入是一个特征向量，而不是图像。
```python
model = DQN(
    "MlpPolicy",
    "LunarLander-v2",
    verbose=1,
    exploration_final_eps=0.1,
    target_update_interval=250,
)
```
导入评估的方法
```python
from stable_baselines3.common.evaluation import evaluate_policy
```
先评估一下未训练的方法：
```python
# Separate env for evaluation
eval_env = gym.make("LunarLander-v2")
# Random Agent, before training
mean_reward, std_reward = evaluate_policy(
    model,
    eval_env,
    n_eval_episodes=10,
    deterministic=True,
)
print(f"mean_reward={mean_reward:.2f} +/- {std_reward}")
```
训练的代码：
```python
# Train the agent
model.learn(total_timesteps=int(1e5))
# Save the agent
model.save("dqn_lunar")
del model  # delete trained model to demonstrate loading
```
加载已训练的模型：
```python
model = DQN.load("dqn_lunar")
```
并且评估：
```python
# Evaluate the trained agent
mean_reward, std_reward = evaluate_policy(model, eval_env, n_eval_episodes=10, deterministic=True)
print(f"mean_reward={mean_reward:.2f} +/- {std_reward}")
```


# 逐渐降低的学习率


```python
from typing import Callable

from stable_baselines3 import PPO


def linear_schedule(initial_value: float) -> Callable[[float], float]:
    """
    Linear learning rate schedule.

    :param initial_value: Initial learning rate.
    :return: schedule that computes
      current learning rate depending on remaining progress
    """
    def func(progress_remaining: float) -> float:
        """
        Progress will decrease from 1 (beginning) to 0.

        :param progress_remaining:
        :return: current learning rate
        """
        return progress_remaining * initial_value

    return func

# Initial learning rate of 0.001
model = PPO("MlpPolicy", "CartPole-v1", learning_rate=linear_schedule(0.001), verbose=1)
model.learn(total_timesteps=20_000)
# By default, `reset_num_timesteps` is True, in which case the learning rate schedule resets.
# progress_remaining = 1.0 - (num_timesteps / total_timesteps)
model.learn(total_timesteps=10_000, reset_num_timesteps=True)
```




