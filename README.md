
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
                .excludePathPatterns("/admin", "/admin/login/**");
    }
}
```

## 数字人民币

> 中国人民银行发行的数字形式的法定货币, 由**指定运营机构参与运营**并向公众兑换, 以广义账户体系为基础，支持银行账户松耦合功能，与纸钞硬币等价

