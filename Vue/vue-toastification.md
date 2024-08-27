

安装：
```node
npm i vue-toastification@next
```
然后在 `main.js`里面引入并使用
```node
import Toast from 'vue-toastification'
import 'vue-toastification/dist/index.css'

import App from './App.vue'
const app = createApp(App)

app.use(Toast)
```


具体使用方法，这个是只能在JavaScript里面使用的，首先导入

```node
import { useToast } from 'vue-toastification';
```
然后创建对象
```node
const toast = useToast();
```
在某些情况使用不同的方法就行了：
```node
toast.success('Job Added Successfully');


toast.error('Job Was Not Added');
```











