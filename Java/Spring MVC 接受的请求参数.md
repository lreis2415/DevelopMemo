# Spring MVC 接受的请求参数
[TOC]
## 1. 概述
 Spring MVC 允许以多种方式将客户端的数据传送到控制器的处理方法中：
- 查询参数（Query Parameter）
- 表单参数（Form Parameter）
- 路径变量（Path Variable）
## 2. 详解
### 2.1 处理查询参数
> 查询参数都是String类型的，但当绑定到方法参数时会转换为相应的类型

在方法中使用 `@RequestParam`注解，同时可通过`defaultValue`属性设置当参数不存在时的默认值，如
```
public List<Spittle> spittles( @RequestParam(value="max",defaultValue=MAX_LONG_AS_STRING) long max,@RequestParam(value="count",defaultValue="20") int count )
// 不设置默认值时
public List<Spittle> spittles( @RequestParam("max") long max,@RequestParam("count") int count )
```
### 2.2 处理路径参数接受输入
1. 在`@RequestMapping`中添加占位符（用{}括起来）表示变量部分，如 `@RequestMapping(value="/{spittleId}")`,这样就能够处理针对“/spittles/123454”的请求。
2. 在方法参数上添加`@PathVariable("spittleId")` 注解，如`public String spittle(@PathVariable("spittleId") long spittleId, Model model)`
3. 若方法参数名与占位符名称**相同**（都是spittleId），则可去掉`@PathVariable`的 value 属性：`public String spittle(@PathVariable long spittleId, Model model)`
> 此时若修改参数名，需要同步修改占位符名称

### 2.3 处理表单
使用HTML的`<form>`标签。
> 如果form所在视图是通过return 视图名的形式渲染的，那么，form中可以没有action属性。此时，其将提交到与展现是相同的URL路径上。如，访问“/register”得到带form的`“/registerForm”`视图，则提交form时会提交到“/register”

处理表单提交时，相应的方法参数可以使用**包含与请求参数同名属性的对象**（即对象属性与form中的input的**name**同名。若Spitter对象有属性username，则某个表单域中的name需要为username）
若需要对参数进行校验时，可使用Spring对Java Validation API的支持。即对参数使用`@Valid`注解，并紧跟Errors参数，以便对错误进行处理。
具体的校验规则在参数对象中设置，如
```java
public class Spitter{
    @NotNull   //所有的注解位于 javax.validation.constraints 包中
    @Size(min=5,max=16)
    private String username;//非空，5-16个字符
}
```
则：
```java
public String processRegistration(@Valid Spitter spitter, Errors errs)
{
   if(errs.hasErrors){
       return "registerForm";//如果校验失败，重新返回表单，避免重复提交
   }
}
```
---
## 3. 补充内容 
> 此部分非《Spring 实战》内容
### 3.1 Ajax/JSON 输入
>http://blog.csdn.net/oTengYue/article/details/51598277
请求的`Content-Type`为`application/json`，请求数据在request的body中
- 不能使用`String xxx`形式
- 不能使用`@RequestParam`

`@RequestBody`注释进行参数传递

```java
@RequestMapping(value = "buAuth/save1")
@ResponseBody
public String save1(@RequestBody BuAuth buAuth){
    return "SUCCESS";
}
```

　　采用`@RequestBody`标注的参数，SpringMVC 框架底层能够自动完成JSON字符串转对应的Bean并注入到方法参数中，主要是通过使用`HandlerAdapter` 配置的`HttpMessageConverters`来解析post data body，然后绑定到相应的bean上的。此时Ajax发送的data值必须为Json字符串，如果Controller中需要映射到自定义Bean对象上上，则必须设置Ajax的contentType为`application/json`（或`application/xml`）。这种方式完整举例如下：

```javascript
$.ajax({
    type: "POST",
    url: "$!{_index}/buAuth/save1",
    data:JSON.stringify(dataObj) ,//传递参数必须是Json字符串
    contentType: "application/json; charset=utf-8",//必须声明contentType为application/json,否则后台使用@RequestBody标注的话无法解析参数
    dataType: "json",
    success: function (response, info) {}
});
```

```java
@RequestMapping(value = "buAuth/save1")
@ResponseBody
public String save1(@RequestBody BuAuth buAuth){
    return "SUCCESS";
}
```

注：（1）此时前端直接用`$.post()直接请求会有问题，ContentType默认是application/x-www-form-urlencoded`。需要使用`$.ajaxSetup()`标示下ContentType为`application/json`（或`application/xml`）。
```
$.ajaxSetup({ContentType:" application/json"});

$.post("$!{_index}/buAuth/save",{buAuth:JSON.stringify(dataObj),menuIds:menu_ids},function(result){});
```

（2）可以使用`@ResponseBody`传递数组，如下举例（做为整理直接引用其他博客例子）

```javascript

var saveDataAry=[];
var data1={"userName":"test","address":"gz"};
var data2={"userName":"ququ","address":"gr"};
saveDataAry.push(data1);
saveDataAry.push(data2);
$.ajax({
    type:"POST",
    url:"user/saveUser",
    dataType:"json",
    contentType:"application/json",
    data:JSON.stringify(saveData),
    success:function(data){ }
});
```


```java
@RequestMapping(value = "saveUser", method = {RequestMethod.POST }}) 
@ResponseBody  
public void saveUser(@RequestBody List<User> users) { 
    userService.batchSave(users); 
}
```
（3）Controller中的同一个方法只能使用`@ResponseBody`标记一个参数。也即是说无法直接通过该方法同时传递多个对象，不过可以间接通过设置一个中间pojo对象（设置不同的属性）来达到传递多个对象的效果。举例如下：
```javascript
var buAuthPage = {
    buAuth :   data,
    menuInfo : {code:"100"}
};

$.ajax({
    type: "POST",
    url: "$!{_index}/buAuth/save5",
    data: JSON.stringify(buAuthPage),
    contentType: "application/json; charset=utf-8",
    dataType: "json",
    success: function(data){
    }
});
```

```java
public class BuAuthPage {
    BuAuth buAuth;
    public BuAuth getBuAuth() {
        return buAuth;
    }
    public void setBuAuth(BuAuth buAuth) {
        this.buAuth = buAuth;
    }
    public MenuInfo getMenuInfo() {
        return menuInfo;
    }
    public void setMenuInfo(MenuInfo menuInfo) {
        this.menuInfo = menuInfo;
    }
}

@RequestMapping(value = "buAuth/save5")
@ResponseBody
public String save5(@RequestBody BuAuthPage buAuthPage){
    return "SUCCESS";
}
```
（4）Axios默认的请求数据类型就是`application/json`



### 3.2 multipart参数


### 3.3 接收 header 数据
>http://www.logicbig.com/tutorials/spring-framework/spring-web-mvc/spring-mvc-request-header/
有时候，需要接收并处理请求头中的数据，此时使用`@RequestHeader`注解（Controller方法参数）

几种形式：
- `@RequestHeader("User-Agent") String userAgent`、 `@RequestHeader("If-Modified-Since") Date date`
- `@RequestHeader(value="User-Agent", defaultValue="foo") String userAgent`
- `@RequestHeader HttpHeaders headers`
- `@RequestHeader Map<String, String> header`

>也可以使用`HttpServletRequest`，  `request.getHeader("code")`
