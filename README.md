## 单点登录

**核心问题**

- 前端token无法在多个系统间共享
- 后端session无法在多个系统间共享

**解决token无法在浏览器共享**

- 同父域名下, 把token存放在父域名的token中
- 不同域名下
    1. 由子系统验证未登录后重定向至 登录地址,并附带参数 redirect-url
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


## ThreadLocal 保存登录用户

**ThreadLocal 保存 删除 获取**
```java
public class UserContext {

    private UserContext() {

    }

    private static final ThreadLocal<AdminLoginUser> adminUserThreadLocal = new ThreadLocal<AdminLoginUser>();

    public static Integer getAdminUserId() {
        AdminLoginUser adminLoginUser = adminUserThreadLocal.get();
        return adminLoginUser == null ? null : adminLoginUser.getUserId();
    }

    public static AdminLoginUser getAdminUser() {
        return adminUserThreadLocal.get();
    }

    public static void setAdminUser(AdminLoginUser adminUser) {
        adminUserThreadLocal.set(adminUser);
    }

    public static void removeAdminUser() {
        adminUserThreadLocal.remove();
    }


}
```

**拦截器**

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    // 获取用户信息, 存入 ThreadLocal
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Object loginUser = session.getAttribute("loginUser");
        if (loginUser instanceof LoginUser) {
            UserContext.setAdminUser((LoginUser) loginUser);
        } else {
            response.sendRedirect("/login");
            return false;
        }
        return true;
    }

    // 请求完成, ThreadLocal 删除
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserContext.removeAdminUser();
    }

}
```

**配置拦截器**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
  @Autowired
  private LoginInterceptor loginInterceptor;
  
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(loginInterceptor)
            .addPathPatterns("/admin/**")
            .excludePathPatterns("/admin","/admin/login/**");
  }
}
```