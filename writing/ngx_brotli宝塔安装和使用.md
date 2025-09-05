1. **模块名称**
   `ngx_brotli`
2. **模块参数**
   `--add-module=/www/server/nginx/src/ngx_brotli`
3. **前置脚本**（可选但推荐）
   在安装前自动克隆仓库并编译依赖库：

```
cd
rm -rf /www/server/nginx/src/ngx_brotli
# 克隆 ngx_brotli 及子模块
git clone --recurse-submodules -j8 https://github.com/google/ngx_brotli.git /www/server/nginx/src/ngx_brotli
cd /www/server/nginx/src/ngx_brotli/deps/brotli
mkdir out && cd out
cmake -DCMAKE_BUILD_TYPE=Release \
      -DBUILD_SHARED_LIBS=OFF \
      -DCMAKE_C_FLAGS="-O3" \
      -DCMAKE_CXX_FLAGS="-O3" ..
make -j$(nproc)
```

