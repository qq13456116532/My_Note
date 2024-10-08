通过以下命令设置 npm 使用该端口的代理：
```node
npm config set proxy http://127.0.0.1:7890 
npm config set https-proxy http://127.0.0.1:7890
```


这样就会将 HTTP 和 HTTPS 的请求都通过 `127.0.0.1:7890` 这个本地地址进行代理。




