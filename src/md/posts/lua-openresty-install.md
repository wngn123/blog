<!--
author: wngn123
head: head.png
date: 2016-08-24
title: OpenResty 安装
tags: lua
category: Lua
status: publish
summary: OpenResty 安装
-->

## 相关shell脚本


```shell
    mkdir /home/work/openresty
    cd /home/work/openresty
    git clone https://github.com/wngn123/wngn-luastudy.git
    cd wngn-luastudy
    cp -rf /home/work/openresty/wngn-luastudy/src/openresty/lua /home/work/openresty/
    cp -rf /home/work/openresty/wngn-luastudy/src/openresty/nginx /home/work/openresty/
    sudo cp -rf /home/work/openresty/nginx/nginx.conf /home/local/openresty/nginx/conf/
    sh /home/work/openresty/nginx/nginx_start.sh
    
```
