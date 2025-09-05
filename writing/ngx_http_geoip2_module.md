| 字段         | 值                                                           |
| :----------- | :----------------------------------------------------------- |
| **模块名称** | `ngx_http_geoip2_module`                                     |
| **模块参数** | `--add-module=/www/server/nginx/src/ngx_http_geoip2_module --with-stream` |
| **前置脚本** | 见下方代码                                                   |

```
#!/bin/bash
# 安装编译依赖
if grep -Eqi "CentOS" /etc/redhat-release; then
    yum install -y git gcc make gcc-c++ pcre-devel zlib-devel openssl-devel wget
    # 安装 libmaxminddb
    yum install -y epel-release
    yum install -y libmaxminddb-devel
elif grep -Eqi "Ubuntu|Debian" /etc/issue; then
    apt update
    apt install -y git build-essential libpcre3-dev zlib1g-dev libssl-dev wget
    # 安装 libmaxminddb
    apt install -y libmaxminddb-dev
fi

cd
rm -rf /www/server/nginx/src/ngx_http_geoip2_module
git clone https://github.com/leev/ngx_http_geoip2_module.git /www/server/nginx/src/ngx_http_geoip2_module
rm -rf /www/server/nginx/src/ngx_http_geoip2_module/.git
```

#### 3. **验证安装**

1. Nginx 安装完成后，SSH 中执行：

   bash

   ```
   nginx -V 2>&1 | grep -o 'ngx_http_geoip2_module'
   ```

   

   若输出 `ngx_http_http_geoip2_module` 表示成功。