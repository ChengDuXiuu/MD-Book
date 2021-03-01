## 概括



`引言:`

​	HTTP请求是无状态的，即所有用户的所有的请求都一个样子，但是我们现在是动态网页，需要不同的用户显示不同的页面。则必须要求请求能够区分出用户信息已达到动态网页的效果。cookie和session应运而生。

`cookie`

*   存储在浏览器客户端。有效时间不设置默认为一次会话后失效并将信息存放在内存中。否则将信息存放在硬盘中。
*   存储数据一般不超过4k
*   浏览器每次请求都会携带cookie
*   存放的数据必须加密且只能是字符串



`session`

*   存储在服务器端。
*   没有容量限制，但是客户访问量暴增，对服务器压力有挑战
*   session id 用来建立用户和服务器之间的联系；每个用户对应创建不同的session id来标识，并将该session id存放在【HTML隐藏标签、cookie、url后】中。
*   有效时间为一次会话
*   存放的数据可以是Java支持的任意类型，且不用加密
*   一般是将session id 存放在cookie中因此强依赖cookie



`cookie和Session带来的问题`

*   一般项目中使用cookie和session结合使用，一些敏感信息存放在session，登录验证令牌session id存放在 cookie实现长期登录的目的
*   session真正痛点在于 **需要服务端存储session id 来和浏览器中的session id形成关联用作验证。**
    *   一方面会造成 服务器的压力
    *   最难受的是 当服务器进行横向扩展时 ，存放在服务器内存中的session id没办法扩展到其他服务器，即在集群和网关中session卡了脖子【当然也有解决办法，就是session id 在所有的集群中都要保持一致 或者是 专门用一个服务器来存储sessino id 】



`Token`

>   因为 session 的扩展问题，新的技术应用而生，但是新的技术必须解决 **不能在服务器中保存数据** 这样才能解决session的核心问题。

实现原理 ：

1.  用户第一次登陆时 服务端获取到用户ID + 密匙  然后使用加密算法对数据做签名  并返回到浏览器，保存到cookie
2.  之后的登陆都会携带该cookie信息到请求头发送至服务端



*   不用服务端保存用户身份信息

*   全程只需要在浏览器端保存用户身份信息。时间可以任意设置

*   **因为服务端不存储用户身份信息因此支持扩展**

*   跨域也支持。cors和jsonp

*   跨程序调用，即单点登录(第三方登录)也可以

    

## Session 、Cookie 实现登录验证

### 利用Session实现登录验证

1.  自定义HandlerInterceptor

    ```java
    public class LoginInterceptor implements HandlerInterceptor {
     
        @Override
        public void afterCompletion(HttpServletRequest request,
                                    HttpServletResponse response, Object obj, Exception err)
                throws Exception {
        }
     
        @Override
        public void postHandle(HttpServletRequest request, HttpServletResponse response,
                               Object obj, ModelAndView mav) throws Exception {
     
        }
     
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                                 Object obj) throws Exception {
            //获取session里的登录状态值
            String str = (String) request.getSession().getAttribute("isLogin");
            //如果登录状态不为空则返回true，返回true则会执行相应controller的方法
            if(str!=null){
                return true;
            }
            //如果登录状态为空则重定向到登录页面，并返回false，不执行原来controller的方法
            response.sendRedirect("/loginPage");
            return false;
        }
    }
    ```



2.  编写Controller，实现登录登出

    ```java
    @Controller
    @RequestMapping("")
    public class BackendController {
     
        
        @RequestMapping(value = "/login", method = {RequestMethod.POST})
        public String login(HttpServletRequest request,RedirectAttributes model, String name, String password){
            //验证账号密码，如果符合则改变session里的状态，并重定向到主页
            if ("xxx".equals(name)&&"123456".equals(password)){
                request.getSession().setAttribute("isLogin","yes");
                return "redirect:IndexPage";
            }else {
                //密码错误则重定向回登录页，并返回错误，因为是重定向所要要用到RedirectAttributes
                model.addFlashAttribute("error","密码错误");
                return "redirect:loginPage";
            }
        }
        //登出，移除登录状态并重定向的登录页
        @RequestMapping(value = "/loginOut", method = {RequestMethod.GET})
        public String loginOut(HttpServletRequest request) {
            request.getSession().removeAttribute("isLogin");
            return "redirect:loginPage";
        } 
    }
    ```

    

### 利用Cookie实现登录验证

>   如果想登录状态退出浏览器后仍保留一段时间的可以将Session改为Cookie。

1.  自定义HandlerInterceptor

    ```java
    public class LoginInterceptor implements HandlerInterceptor {
     
        @Override
        public void afterCompletion(HttpServletRequest request,
                                    HttpServletResponse response, Object obj, Exception err)
                throws Exception {
        }
     
        @Override
        public void postHandle(HttpServletRequest request, HttpServletResponse response,
                               Object obj, ModelAndView mav) throws Exception {
     
        }
     
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                                 Object obj) throws Exception {
    //        获取request的cookie
            Cookie[] cookies = request.getCookies();
            if (null==cookies) {
                System.out.println("没有cookie==============");
            } else {
    //            遍历cookie如果找到登录状态则返回true执行原来controller的方法
                for(Cookie cookie : cookies){
                    if(cookie.getName().equals("isLogin")){
                        return true;
                    }
                }
            }
    //        没有找到登录状态则重定向到登录页，返回false，不执行原来controller的方法
            response.sendRedirect("/loginPage");
            return false;
        }
    }
    ```

    

2.  编写Controller层代码实现登录登出

    ```java
    @Controller
    @RequestMapping("")
    public class BackendController {
     
        @RequestMapping(value = "/loginPage", method = {RequestMethod.GET})
        public String loginPage(HttpServletRequest request, String account, String password) {
            return "login";
        }
     
        @RequestMapping(value = "/login", method = {RequestMethod.POST})
        public String login(HttpServletRequest request, HttpServletResponse response, RedirectAttributes model, String name, String password) {
            if ("xxx".equals(name) && "123456".equals(password)) {
                Cookie cookie = new Cookie("isLogin", "yes");
                cookie.setMaxAge(30 * 60);// 设置为30min
                cookie.setPath("/");
                response.addCookie(cookie);
                return "redirect:IndexPage";
            } else {
                model.addFlashAttribute("error", "密码错误");
                return "redirect:loginPage";
            }
        }
     
        @RequestMapping(value = "/logOut", method = {RequestMethod.GET})
        public String loginOut(HttpServletRequest request, HttpServletResponse response) {
            Cookie[] cookies = request.getCookies();
            for (Cookie cookie : cookies) {
                if (cookie.getName().equals("isLogin")) {
                    cookie.setValue(null);
                    cookie.setMaxAge(0);// 立即销毁cookie
                    cookie.setPath("/");
                    response.addCookie(cookie);
                    break;
                }
            }
            return "redirect:loginPage";
        }
    }
    ```

    

>   另外，无论基于Session还是Cookie的登录验证都需要对HandlerInteceptor进行配置，增加对URL的拦截过滤机制。
>
>   版权声明：本文为CSDN博主「chao09_01」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
>   原文链接：https://blog.csdn.net/chao821/article/details/106892742/