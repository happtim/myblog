安装nginx

在conf/nginx.config http 后面添加
```
include ../conf.d/*.conf; 
```

配置文件
```

server {
    listen 8000;

    location / {
        root D:/InnoLight/2024-1-30/web/wwwroot/;
		
		try_files $uri $uri/ /index.html =404;
    }
}

```