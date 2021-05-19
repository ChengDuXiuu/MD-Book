## 处理 Request URL

### 1、处理请求参数

1.  url请求参数名称与方法中参数名一致

    ```java
    http://localhost:8080/0919/test1?name=xxx&pwd=yyy
    @RequestMapping("/test1")
    public R test1(String name,String pwd) {
    
    }
    ```

2.  HttpServletRequest

    ```java
    http://localhost:8080/0919/test2?firstName=zhang&lastName=san
    @RequestMapping("/test2")
    public R test1(HttpServletRequest request) {
        String firstName = request.getParameter("firstName");
        String lastName = request.getParameter("lastName");
    
    }
    ```

3.  @PathVariable  : 适用于 请求中携带简单参数，例如ID查询

    >   从映射路径中获取参数，需要在映射路径中配合

    ```java
    http://localhost:8080/0919/test4/111/222
    @RequestMapping("/test2/{id}")
    public R test1(@PathVariable("id")String id) {
    }
    #适配
    export function ResourceInfoApi(id) {
      return request({
        url: request.adornUrl(`${BASE_URL}/info/${id}`),
        method: "get",
        params: request.adornParams()
      })
    }
    ```

4.  @RequestParam ： 适用于Map结构，类似于分页参数

    >   *   `常用来处理简单类型的绑定`，通过Request.getParameter() 获取的String可直接转换为简单类型的情况（ String--> 简单类型的转换操作由ConversionService配置的转换器来完成）；因为使用request.getParameter()方式获取参数，所以可以处理get 方式中queryString的值，也可以处理post方式中 body data的值；
    >   *   用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容，提交方式GET、POST；
    >   *    该注解有两个属性： value、required； value用来指定要传入值的id名称，required用来指示参数是否必须绑定；

    ```java
    http://localhost:8080/0919/test5?aaa=111&bbb=4444
    @RequestMapping("/test2")
    public R test1(@RequestParam("aaa")String aaa,@RequestParam("bbb")String bbb) {
    }
    
    #前台接口
    export function NetworkCardListApi(params) {
      return request({
        url: request.adornUrl(`${BASE_URL}/list`),
        method: "get",
        params: request.adornParams(params)
      })
    }
    #前台使用
        NetworkCardListApi({
            page: this.pageIndex,
            limit: this.pageSize,
            key: this.dataForm.key
        }).then((data => {
    
    	})    
    
    #后台
    public R list(@RequestParam Map<String, Object> params) {
        PageUtils page = networkCardService.queryPage(params);
    	String key = params.get("key").toString()	;
        return R.ok().put("page", page);
    }    
    ```

5.  @RequestBody ：适用于 存储POST或者更新PUT

    >   *   `该注解常用来处理Content-Type: 不是application/x-www-form-urlencoded编码的内容，例如application/json, application/xml等`；
    >   *   它是通过使用HandlerAdapter 配置的HttpMessageConverters来解析post data body，然后绑定到相应的bean上的。

    ```java
    export function NetworkCardSaveApi(data) { # data为实体类对象
      return request({
        url:request.adornUrl(`${BASE_URL}/save`),
        method:"post",
        data:request.adornData(data,false)
    })
    }
    
    public R save(@RequestBody List<NetworkCardVO> networkCardVOS) {
        List<NetworkCardPO> networkCardPOS = NetworkCardConvert.INSTANCE.vo2po(networkCardVOS);
        networkCardService.saveBatch(networkCardPOS);
        return R.ok();
    }
    ```
    
    

### 2、处理请求其他

1.  @RequestHeader

    >   @RequestHeader 注解，可以把Request请求header部分的值绑定到方法的参数上。

    ```bash
    Host                    localhost:8080
    Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
    Accept-Language         fr,en-gb;q=0.7,en;q=0.3
    Accept-Encoding         gzip,deflate
    Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
    Keep-Alive    
    ```

    ```java
    @RequestMapping("/displayHeaderInfo")
    public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding,
                                  @RequestHeader("Keep-Alive") long keepAlive)  {
     
      //...
     
    }
    ```

2.  @CookieValue

    >   @CookieValue 可以把Request header中关于cookie的值绑定到方法的参数上。

    ```bash
    JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
    ```

    ```java
    
    @RequestMapping("/displayHeaderInfo.do")
    public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie)  {
     
      //...
     
    }
    ```

3.  @SessionAttributes

    >   该注解用来绑定HttpSession中的attribute对象的值，便于在方法中的参数里使用。该注解有value、types两个属性，可以通过名字和类型指定要使用的attribute 对象；

    ```java
    @Controller
    @RequestMapping("/editPet.do")
    @SessionAttributes("pet")
    public class EditPetForm {
        // ...
    }
    ```

    

4.  @ModelAttribute

    >   该注解有两个用法，一个是用于方法上，一个是用于参数上；
    >
    >   用于方法上时： 通常用来在处理@RequestMapping之前，为请求绑定需要从后台查询的model；
    >
    >   用于参数上时： 用来通过名称对应，把相应名称的值绑定到注解的参数bean上；要绑定的值来源于：
    >
    >   A） @SessionAttributes 启用的attribute 对象上；
    >
    >   B） @ModelAttribute 用于方法上时指定的model对象；
    >
    >   C） 上述两种情况都没有时，new一个需要绑定的bean对象，然后把request中按名称对应的方式把值绑定到bean中。
    
用在方法上
    
```java
    // Add one attribute
    // The return value of the method is added to the model under the name "account"
    // You can customize the name via @ModelAttribute("myAccount")
     
    @ModelAttribute
    public Account addAccount(@RequestParam String number) {
        return accountManager.findAccount(number);
    }
    ```
    
用在参数上
    
```java
    
    @RequestMapping(value="/owners/{ownerId}/pets/{petId}/edit", method = RequestMethod.POST)
    public String processSubmit(@ModelAttribute Pet pet) {
       
    }
    ```
    


## 请求参数中域属性

>   解决pojo中嵌套pojo 参数映射。

```java
@Data
public class Student {
    private String name;
    private String age;
    private School school;
}
```

```json
{
  "name": "张三",
  "age": 18,
  "school.name": "清华青鸟",
  "school.address": "北京"
}
```

