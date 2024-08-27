在Vue里面一般使用这个来请求，他是一个HTTP客户端，相比`fetch`而已使用更少的代码发送一个请求。

首先下载：
```node
npm i axios
```

然后使用方法:
```js
import { ref,onMounted } from 'vue';
import axios from 'axios';

const jobs = ref([])
onMounted(async()=>{
    try{
        const response = await axios.get("http://localhost:5000/jobs")
        jobs.value = response.data
    }catch(error){
        console.error("Error fetching jobs")
    }
})
```
就是配合 `async` 和 `await` 一起使用


另一种使用方法就是点击触发函数，这里是定义函数：
```js
const deleteJob = async()=>{
  try{
    await axios.delete(`/api/jobs/${jobId}`);
    toast.success('Job Deleted Successfully');
    router.push('/jobs');
  }catch(error){
    console.error('Error deleting job',error);
    toast.error('Job Not Deleted');
  }
}
```











