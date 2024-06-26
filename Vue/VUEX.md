Vuex是一个专为Vue定制的状态管理模块，其集中式地存储和管理应用的所有组件的状态，使这些状态数据可以按照我们预期的方式变化。

对于小型的、简单的Vue应用，我们使用Vue自身的状态管理功能就已经足够，但是对于复杂度高、组件繁多的Vue应用，组件间的交互会使得状态的管理变得困难，这时就需要Vuex的帮助了。

Vuex采用集中的方式管理所有组件的状态，相较于“集中式”而言，Vue本身对状态的管理是“独立式”的，即每个组件只负责维护自身的状态。


对于平级的多个组件，共享同一个状态是非常困难的，
不同的组件若要更改同一个状态，最直接的方式是将触发动作交给上层，对于多层嵌套的组件，则需要一层一层地向上传递事件，在最上层统一处理状态的更改，这会使代码的维护难度大大增加

在Vuex中，可以将需要组件间共享的状态抽取出来，以一个全局的单例模式进行管理。在这种模式下，视图无论在视图树中的哪个位置，都可以直接获取这些共享的状态，也可以直接触发修改动作来动态改变这些共享的状态。

安装
```node
npm install vuex@next --save
```
Vuex的基础使用是这样的：
![](images/Pasted%20image%2020240115144750.png)
核心是store，即仓库。简单理解，store本身就是一个容器，其内存储和管理的应用中需要多组件共享的状态。Vuex中的store非常强大，其中存储的状态是响应式的，若store中的状态数据发生变化，其会自动反映到对应的组件视图上。并且，store中的状态数据并不允许开发者直接进行修改，改变store中状态数据的唯一办法是提交mutation操作，通过这样严格的管理，可以更加方便地追踪每一个状态的变化过程

然后子组件中这样使用：
![](images/Pasted%20image%2020240115144845.png)
- 状态state就是应用中组件需要共享的数据

虽然使用Vuex管理状态非常方便，但是这并不意味着需要将组件所有使用到的数据都放在store中，这会使store仓库变得巨大且容易产生冲突。对于那些完全是组件内部使用的数据，还是应该将其定义为局部的状态。





















