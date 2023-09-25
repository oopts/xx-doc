## 单点登录

**核心问题**

- 前端token无法在多个系统间共享
- 后端session无法在多个系统间共享

**解决token无法在浏览器共享**

- 同父域名下, 把token存放在父域名的token中
- 不同域名下
    1. 由子系统验证未登录后重定向至 登录地址,并附带参数redirect-url
    2. 登录页发现已经登录过, 从cookie中获取到token, 并重定向 redirect-url, 并附带参数 token

**解决session无法在服务器间共享**

- 可以访问到同一Redis, 直接使用Redis
- 使用JWT加密令牌, 通过密钥获取用户信息
- 可以访问认证中心, 发送请求从认证中心获取到登录的用户信息

## Oauth2

## QueryDSL

## docsify

安装

```shell
npm i docsify-cli -g
```

初始化

```shell
docsify init .
```

预览

```shell
docsify serve -p 80
```

[详细教程, 请点击](https://blog.csdn.net/liyou123456789/article/details/124504727)

