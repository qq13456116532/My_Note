官网是[Install Tailwind CSS with Vue 3 and Vite - Tailwind CSS](https://v2.tailwindcss.com/docs/guides/vue-3-vite)


要使用先使用npm下载，
```shell
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```
![](images/Pasted%20image%2020240823201021.png)

下载后创建tailwind的配置文件
```shell
npx tailwindcss init -p
```
此时就会生成两个配置文件，![](images/Pasted%20image%2020240823201059.png)
打开其中的`tailwind.config.js`
![](images/Pasted%20image%2020240823201220.png)
这里的`content`是指为哪些前端文件使用tailwind的CSS，所以一般写成
```js
content: ['./index.html','./src/**/*.{vue,js,ts,jsx,tsx}'],
```

然后基本上改成这样：
```js
export default {
  content: ['./index.html','./src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {
      fontFamily:{
        sans:['Poppins','sans-serif'],
      },
      gridTemplateColumns:{
        '70/30':'70% 28%',
      },
    },
  },
  variants: {
    extend: {},
  },
  plugins: [],
}
```

然后在
![](images/Pasted%20image%2020240823203847.png)
里面修改成
```js
@import './base.css';
@tailwind base;
@tailwind components;
@tailwind utilities;
```
这样就可以使用Tailwind的CSS了，例如可以这样使用它的类：
```vue
<h1 class="text-3xl">
    Vue Jobs
</h1>
```






























