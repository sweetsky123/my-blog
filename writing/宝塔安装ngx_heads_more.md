- **模块名称**: `ngx_headers_more`
- **模块参数**: `--add-module=/www/server/nginx/src/ngx_headers_more`
- **前置脚本**:

```
cd
rm -rf /www/server/nginx/src/ngx_headers_more
git clone https://github.com/openresty/headers-more-nginx-module.git /www/server/nginx/src/ngx_headers_more
rm -rf /www/server/nginx/src/ngx_headers_more/.git
```

