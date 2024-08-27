`json-server` 是一个非常流行的工具，用于快速搭建一个全功能的 REST API。可以使用简单的 JSON 文件作为数据源。比如你放一个json数组在里面，那么就会**自动创建**获取整个数组、获取某个元素的**API**。而不需要你手动创建spring boot之类的服务器才能使用了。不论是 获取的，还是创建的，删除的，都有！！！

首先下载
```node
npm install -g json-server
```

然后一般是在`package.json`里面使用脚本启动
```js
"scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "server": "json-server --watch src/jobs.json --port 8000"
  }
```
就是在里面的`scripts`添加 `server`这个属性来启动`json-server`，这里是启动在了端口8000，但是`jobs.json`里必须有json类型数据。

然后运行
```node
npm run server
```
来启动这个json服务器，
![](images/Pasted%20image%2020240826111055.png)
直接访问这个URL就是下面这样：
![](images/Pasted%20image%2020240826111626.png)


然后再使用
```node
npm run dev
```
来启动Vite，也就是我们的Vue项目。









