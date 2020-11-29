## 7.Shiro整合springboot之thymeleaf权限控制

### 1.引入扩展依赖

```xml
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 2.页面中引入命名空间

-  xmlns:shiro="http://www.pollix.at/thymeleaf/shiro"

```html
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
......
```

### 3.常见权限控制标签使用

```html
<!-- 验证当前用户是否为“访客”，即未认证（包含未记住）的用户。 -->
<p shiro:guest="">Please <a href="login.html">login</a></p>


<!-- 认证通过或已记住的用户。 -->
<p shiro:user="">
    Welcome back John! Not John? Click <a href="login.html">here</a> to login.
</p>

<!-- 已认证通过的用户。不包含已记住的用户，这是与user标签的区别所在。 -->
<p shiro:authenticated="">
    Hello, <span shiro:principal=""></span>, how are you today?
</p>
<a shiro:authenticated="" href="updateAccount.html">Update your contact information</a>

<!-- 输出当前用户信息，通常为登录帐号信息。 -->
<p>Hello, <shiro:principal/>, how are you today?</p>


<!-- 未认证通过用户，与authenticated标签相对应。与guest标签的区别是，该标签包含已记住用户。 -->
<p shiro:notAuthenticated="">
    Please <a href="login.html">login</a> in order to update your credit card information.
</p>

<!-- 验证当前用户是否属于该角色。 -->
<a shiro:hasRole="admin" href="admin.html">Administer the system</a><!-- 拥有该角色 -->

<!-- 与hasRole标签逻辑相反，当用户不属于该角色时验证通过。 -->
<p shiro:lacksRole="developer"><!-- 没有该角色 -->
    Sorry, you are not allowed to developer the system.
</p>

<!-- 验证当前用户是否属于以下所有角色。 -->
<p shiro:hasAllRoles="developer, 2"><!-- 角色与判断 -->
    You are a developer and a admin.
</p>

<!-- 验证当前用户是否属于以下任意一个角色。  -->
<p shiro:hasAnyRoles="admin, vip, developer,1"><!-- 角色或判断 -->
    You are a admin, vip, or developer.
</p>

<!--验证当前用户是否拥有指定权限。  -->
<a shiro:hasPermission="userInfo:add" href="createUser.html">添加用户</a><!-- 拥有权限 -->

<!-- 与hasPermission标签逻辑相反，当前用户没有制定权限时，验证通过。 -->
<p shiro:lacksPermission="userInfo:del"><!-- 没有权限 -->
    Sorry, you are not allowed to delete user accounts.
</p>

<!-- 验证当前用户是否拥有以下所有角色。 -->
<p shiro:hasAllPermissions="userInfo:view, userInfo:add"><!-- 权限与判断 -->
    You can see or add users.
</p>

<!-- 验证当前用户是否拥有以下任意一个权限。  -->
<p shiro:hasAnyPermissions="userInfo:view, userInfo:del"><!-- 权限或判断 -->
    You can see or delete users.
</p>
<a shiro:hasPermission="pp" href="createUser.html">Create a new User</a>

```

### 4.加入shiro的方言配置

- 页面标签不起作用一定要记住加入方言处理

```java
@Bean(name = "shiroDialect")
public ShiroDialect shiroDialect(){
  return new ShiroDialect();
}
```

![image-20200601210335151](第五章-S.assets/image-20200601210335151.png)



