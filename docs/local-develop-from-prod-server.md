## 一、互联网访问本地开发环境

平时大家做开发，会调试外部的开发接口，比如：微信小程序，支付接口，消息推送等；一般的流程是代码本地调试，然后发布、部署到服务器，使用服务器的数据库和服务来确认我们的开发是否跟设计、数据是匹配的。这个流程在一定程度上是比较耗时的，下面推荐一种更简便的方式：通过互联网访问本地开发环境，把本地开发环境发布到互联网上。下面使用nodejs做为本地的开发环境

1. 安装nodejs框架和依赖

```
    //安装express框架, cors解决跨域问题
    -> npm install express body-parser cors --save
```
```
    //监听文件变化，重启服务
    -> npm install nodemon --save-dev
```
```
    //添加script脚本在package.json
    "test": "echo \"Error: no test specified\" && exit 1"
```
```
    -> npm start
```

2. 域名解析和配置nginx

```
    //解析域名A记录到 sandbox.micaven.com
```
```
    //nginx配置文件
    //补充知识 nginx配置 upstream
    upstream tunnel {
    server 127.0.0.1:7689;
    }

    server {
        listen 80;
        server_name sandbox.micaven.com;

        location / {
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_redirect off;

            proxy_pass http://tunnel;
        }
    }
```

```
    //加载nginx配置，生效
    systemctl reload nginx
```

3. 本地ssh通道

```
    ssh -vnNt -R 7689:localhost:8080 root@42.x.x.x
```

```
    //访问：sandbox.micaven.com
    // 请求本地的接口地址：http://localhost:8080
```

4. 申请阿里云CA ssl证书, 配置nginx ssl

```
    //根据nginx ssl推荐配置，把ssl证书重命名，放在/etc/nginx/ssl
    // 配置nginx的ssl选项，在server区块中添加
```

```
    //重新加载配置
    systemctl reload nginx
```

```
    //访问域名：https://sandbox.micaven.com
    //更改本地文件，确认接口响应信息
```

补充知识： nodejs，express框架搭建应用；nginx配置；ssl证书申请和配置
