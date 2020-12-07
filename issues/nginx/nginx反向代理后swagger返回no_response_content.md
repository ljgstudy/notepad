### nginx反向代理后swagger返回no response content

---

#### 一、修改nginx配置

```yaml
server {
        listen   80;
        server_name   www.yduserapi.dev.ali.52koala.cn yduserapi.dev.ali.52koala.cn;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass  http://yduserapi.dev.52koala.cn;
        }
    }
```



#### 二、待选的解决方案

有两种解决方案：

1) 第一种是修改nginx的配置，在转发的时候把请求头带过去

```
location / { 
    //添加header避免swagger-ui出现no response from server的错误 
    proxy_set_header Host $host; 
    proxy_set_header X-Real-IP $remote_addr; 
    //这里通常是你要转发的地址 
    proxy_pass http://ip地址:端口号;
}
```

2) 修改springboot的application.yaml配置，指定swagger的host

```
springfox:
documentation:
swagger:
v2:
host: ip地址:端口号
```