
<!--
author: wngn123
head: head.png
date: 2016-08-24
title: lua_use_default_type
tags: lua
category: Lua
status: publish
summary: 指定是否使用MIME类型（default_type指令指定的类型）作为Content-Type响应头信息。如果你不想使用这个默认的Content-Type响应头信息给你的Lua请求去处理，则将这个指令设置为关闭状态>（off）。
-->

lua_use_default_type
--------------------
**syntax:** *lua_use_default_type on | off*

**default:** *lua_use_default_type on*

**context:** *http, server, location, location if*

Specifies whether to use the MIME type specified by the [default_type](http://nginx.org/en/docs/http/ngx_http_core_module.html#default_type) directive for the default value of the `Content-Type` response header. If you do not want a default `Content-Type` response header for your Lua request handlers, then turn this directive off.

This directive is turned on by default.

This directive was first introduced in the `v0.9.1` release.

中文
--------------------
**语法 :** *lua_use_default_type on | off*

**默认值:** *lua_use_default_type on*

**上下文:** *http, server, location, location if*

指定是否使用MIME类型（default_type指令指定的类型）作为Content-Type响应头信息。如果你不想使用这个默认的Content-Type响应头信息给你的Lua请求去处理，则将这个指令设置为关闭状态（off）。

这个指令默认是开启状态（on）

这个指令第一次出现是在 v0.9.1 稳定版中。

  

```wngn123@163.com on 206-07-12```
