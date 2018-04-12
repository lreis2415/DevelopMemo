# Spring 集成 Swagger2

## Maven 

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

## 配置类 SwaggerConfig 

```java
@EnableWebMvc //启用Mvc，非springboot框架需要引入注解@EnableWebMvc
@EnableSwagger2 // 使swagger2生效
@Configuration // 配置注解，Spring 自动加载此配置信息
@ComponentScan(basePackages = {"com.custom.web"})  //需要扫描的包路径
public class SwaggerConfig {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select() // 选择那些路径和api会生成document
            	//扫描指定包中的swagger注解
                .apis(RequestHandlerSelectors.basePackage("com.z*.b*.c*.controller"))
                //扫描所有有注解的api，用这种方式更灵活
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any()) // 对所有api进行监控
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("接口列表 v1.1.0") // 任意，请稍微规范点
                .description("接口测试") // 任意，请稍微规范点
                .termsOfServiceUrl("http://url/swagger-ui.html") // 将“url”换成自己的ip:port
                .contact("laowu") // 无所谓（这里是作者的别称） 已废弃，使用Contact类
                .version("1.1.0")
                .build();
    }
}
```

##  配置文件

### web.xml

Springmvc前端控制器扫描路径增加“/v2/api-docs”，用于扫描Swagger的 **/v2/api-docs，否则 /v2/api-docs无法生效。**

```xml
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/v2/api-docs</url-pattern>
</servlet-mapping>
```

### mvc.xml

```xml
<!-- 使用 Swagger Restful API文档时，添加此注解 -->
<mvc:default-servlet-handler />
<mvc:annotation-driven/>
或者
<mvc:resources location="classpath:/META-INF/resources/" mapping="swagger-ui.html"/>
<mvc:resources location="classpath:/META-INF/resources/webjars/" mapping="/webjars/**"/>

<!-- ?有人的配置如下 -->
<mvc:exclude-mapping path="/swagger*/**"/>  
<mvc:exclude-mapping path="/v2/**"/>  
<mvc:exclude-mapping path="/webjars/**"/>  
```



## Controller 上面

```java
// 控制器类，可选
@Api(tags="个人业务") 
//方法上：
@ApiOperation(value = "教程", httpMethod = "POST", notes = "教程")
//放在入参中：
@ApiParam(required = true, name = "test", value = "教程入参") 

/**
 * 根据用户名获取用户对象
 * @param name
 * @return
 */
@RequestMapping(value="/name/{name}", method = RequestMethod.GET)
@ResponseBody
@ApiOperation(value = "根据用户名获取用户对象", httpMethod = "GET", response = ApiResult.class, notes = "根据用户名获取用户对象")
public ApiResult getUserByName(@ApiParam(required = true, name = "name", value = "用户名") @PathVariable String name) throws Exception{
    UcUser ucUser = ucUserManager.getUserByName(name);

    if(ucUser != null) {
        ApiResult<UcUser> result = new ApiResult<UcUser>();
        result.setCode(ResultCode.SUCCESS.getCode());
        result.setData(ucUser);
        return result;
    } else {
        throw new BusinessException("根据{name=" + name + "}获取不到UcUser对象");
    }
}


@ApiOperation("信息软删除")
@ApiResponses({ @ApiResponse(code = CommonStatus.OK, message = "操作成功"),
            @ApiResponse(code = CommonStatus.EXCEPTION, message = "服务器内部异常"),
            @ApiResponse(code = CommonStatus.FORBIDDEN, message = "权限不足") })
@ApiImplicitParams({ @ApiImplicitParam(paramType = "query", dataType = "Long", name = "id", value = "信息id", required = true) })
@RequestMapping(value = "/remove.json", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
public RestfulProtocol remove(Long id){}
```

  上述代码是Controller中的一个方法，`@ApiOperation`注解对这个方法进行了说明，`@ApiParam`注解对方法参数进行了说明。关于这两个注解的使用，可以参看源码。这样子，Swagger就可以扫描接口方法，得到我们自定义的接口说明内容。

说明： 
     其中`@ApiOperation`和`@ApiParam`为添加的API相关注解，个参数说明如下： 
     @ApiOperation(value = “接口说明”, httpMethod = “接口请求方式”, response = “接口返回参数类型”, notes = “接口发布说明”；其他参数可参考源码； 
     @ApiParam(required = “是否必须参数”, name = “参数名称”, value = “参数具体描述”
     
## Restful API 访问  

`http://IP:port/{context-path}/swagger-ui.html  `

### Swagger-UI配置

  Swagger扫描解析得到的是一个json文档，对于用户不太友好。下面介绍swagger-ui，它能够友好的展示解析得到的接口说明内容。

  从<https://github.com/swagger-api/swagger-ui> 获取3.0版本以下，2.0版本以上。其所有的 dist 目录下东西放到需要集成的项目里，本文放入 src/main/webapp/WEB-INF/swagger/ 目录下。

  修改swagger/index.html文件，默认是从连接http://petstore.swagger.io/v2/swagger.json获取 API 的 JSON，这里需要将url值修改为http://{ip}:{port}/{projectName}/api-docs的形式，{}中的值根据自身情况填写。比如我的url值为：http://localhost:8083/arrow-api/api-docs

  因为swagger-ui项目都是静态资源，restful形式的拦截方法会将静态资源进行拦截处理，所以在springmvc配置文件中需要配置对静态文件的处理方式。

```xml
//所有swagger目录的访问，直接访问location指定的目录
<mvc:resources mapping="/swagger/**" location="/WEB-INF/swagger/"/>
```

# swagger注解说明

[swagger注释API详细说明](https://blog.csdn.net/xupeng874395012/article/details/68946676)

1. **与模型相关的注解,用在bean上面**

   - **@ApiModel：用在bean上，对模型类做注释；**表明这是一个被swagger框架管理的model
   - **@ApiModelProperty：用在属性上，对属性做注释**

2. **与接口相关的注解**

   - **@Api：用在controller上，对controller进行注释；**
   - **@ApiOperation：用在API方法上，对该API做注释，说明API的作用；**

   - **@ApiImplicitParams：用来包含API的一组参数注解，可以简单的理解为参数注解的集合声明；** 

   - **@ApiImplicitParam：用在@ApiImplicitParams注解中，也可以单独使用，说明一个请求参数的各个方面，该注解包含的常用选项有：**

     - paramType：参数所放置的地方，包含query、header、path、body以及form，最常用的是前四个。
     - name：参数名；
     - dataType：参数类型，可以是基础数据类型，也可以是一个class；
     - required：参数是否必须传；
     - value：参数的注释，说明参数的意义；
     - defaultValue：参数的默认值；

   - **@ApiResponses：通常用来包含接口的一组响应注解，可以简单的理解为响应注解的集合声明；**

   - **@ApiResponse：用在@ApiResponses中，一般用于表达一个响应信息**

     - **code：即httpCode，例如400** 
     - **message：信息，例如"操作成功"**
     - **response = UserVo.class  这里UserVo是一个配置了@ApiModel注解的对像，该是对像属性已配置 ****
   - **@ApiModelProperty,swagger可以通过这些配置，生成接口返回值**



　　**注意事项:**

1. 为了在swagger-ui上看到输出，**至少需要两个注解：@Api和@ApiOperation**
2. 即使只有一个@ApiResponse，也需要使用@ApiResponses包住
3. 对于@ApiImplicitParam的paramType：query、form域中的值需要使用@RequestParam获取， header域中的值需要使用@RequestHeader来获取，path域中的值需要使用@PathVariable来获取，body域中的值使用@RequestBody来获取，否则可能出错；而且如果paramType是body，name就不能是body，否则有问题，与官方文档中的“If paramType is "body", the name should be "body"不符。

---

参考目录：

> https://github.com/springfox/springfox
>
> https://www.cnblogs.com/woshimrf/p/5863318.html
>
> https://blog.csdn.net/blackmambaprogrammer/article/details/72354007
>
> https://www.cnblogs.com/exmyth/p/7183753.html
>
> https://www.cnblogs.com/yuebintse/p/5950322.html