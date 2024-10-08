首先是下载Electron-forge的代码模板：
```node
npm init electron-app@latest my-new-app -- --template=vite
```
然后看到里面的`forge.config.js`，![](images/Pasted%20image%2020240920153716.png)这里是三个进程的配置： 
- **主进程 (`vite.main.config.mjs`)**：处理 Electron 的主进程。
- **预加载 (`vite.preload.config.mjs`)**：负责在主进程和渲染进程之间安全地暴露 API。
- **渲染进程 (`vite.renderer.config.mjs`)**：用于渲染进程的配置文件，应该在这里处理 `.vue` 文件。

只有第三个进程是处理vue文件的，所以我们在`vite.renderer.config.mjs`配置文件中添加`vue`插件，以便可以处理`.vue`文件


安装一下`vue`：
```node
npm install vue
npm install @vitejs/plugin-vue --save-dev
```
然后在对应的配置文件里面加入这两句：
```node
import vue from '@vitejs/plugin-vue';  // 引入 Vue 插件
//在return的插件中加入vue()
plugins: [vue(),pluginExposeRenderer(name)],
```


然后就是正常的编写Vue流程了，编写`App.vue`，然后在`index.html`里面需要有：
```html
<div id="app"></div>
```
在渲染进程对应的JavaScript文件，即`renderer.js`里面挂载我们的`App.vue`就完成了，
```js
import { createApp } from 'vue';
import App from './App.vue';

createApp(App).mount('#app');
```





















