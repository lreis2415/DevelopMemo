---
title: Spring MVC 利用Mockito和JUnit进行单元测试
toc: true
description: 'Spring MVC 利用Mockito和JUnit进行单元测试'
date: 2016-12-8 
tags: [Spring MVC,Mockito,JUnit,单元测试]
categories: 开发-测试
---

## Spring MVC 利用Mockito和JUnit进行单元测试
>参考文章：
>1. [Unable to mock Service class in Spring MVC Controller tests](http://stackoverflow.com/questions/16170572/unable-to-mock-service-class-in-spring-mvc-controller-tests "mock Service class in Spring MVC Controller tests")
>2. Spring 官方参考: [Part IV. Testing](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#testing)
>3. [Junit测试Controller（MockMVC使用），传输@RequestBody数据解决办法](http://www.cnblogs.com/0201zcr/p/5756642.html "Junit测试Controller（MockMVC使用），传输@RequestBody数据解决办法")
>4. [基于Spring MVC做单元测试（一）——使用Spring Test框架](http://zhaozhiming.github.io/blog/2014/06/16/spring-mvc-unit-test-part-1/) 
>5. [玩转单元测试之Testing Spring MVC Controllers](http://www.cnblogs.com/wade-xu/p/4311657.html)
>6. [使用 Spring 进行单元测试](https://www.ibm.com/developerworks/cn/java/j-lo-springunitest/#ibm-pcon) (IBM)
>7. Spring实战（第四版）
>8. [Spring and Mockito - Happy Together](https://solutiondesign.com/blog/-/blogs/spring-and-mockito-happy-together)
>9. [MockMVC 测试文件上传带参数](http://tobato.iteye.com/blog/2315174)
>

## 相关maven依赖：

```xml
<dependency>
     <groupId>junit</groupId>
     <artifactId>junit</artifactId>
     <version>4.11</version>
     <scope>test</scope>
</dependency>
<dependency>
     <groupId>org.mockito</groupId>
     <artifactId>mockito-all</artifactId>
     <version>1.10.19</version>
     <scope>test</scope> 
</dependency>
<dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-test</artifactId>
     <version>${spring.version}</version>
</dependency>
```
## 注意

程序入口类（Application）必须位于包的最顶层，包括测试类所在包。

或者，在测试类中使用`@SpringApplicationConfiguration(classes = Application.class)`指定程序入口类

## 准备代码

### Controller代码

```java

```
### DAO 与Service代码

```

```



## 测试

测试有三种类型：

-   **单独测试，模拟对象测试**。单独测试函数方法的逻辑、功能是否满足需求，是否能够正常工作。此时可以模拟一个真实的对象来进行测试，这样测试人员能够完全掌控依赖对象的行为。——***Mockito tests***
-   **集成测试，真实对象测试**。测试函数方法是否能在整个系统环境下正常工作。影响测试结果的不仅包括测试的目标函数，还包括相关的环境配置、依赖的类和方法。——***Spring tests***
-   **模拟部分依赖，而其他依赖使用真实对象**。在部分依赖只有接口还没有实现类，或使用真实的依赖对象测试时时间较长，或测试某种特殊条件下的异常处理，又或者依赖于第三方的系统时，可能需要采用这种测试方法。——***Mockito Spring tests***

### 独立单元测试（不需要spring配置文件）

`standaloneSetup` ，这不是真实的 Spring MVC 环境（所以可以不用加载配置文件）

`@Mock` : 需要被Mock的对象，如Service

`@InjectMocks` : 需要将Mock对象注入的**对象**, 如使用了被Mock的Service的Controller。`@InjectMocks` 标注的属性不能使用接口，因为`@InjectMocks` 不能传入参数指明实现类

`@Before` setup方法

初始化Mock对象， 通过`MockMvcBuilders.standaloneSetup`模拟一个Mvc测试环境，注入controller, 通过`build`得到一个MockMvc， 后面就用MockMvc的一些 API 做测试。 

-   `MockitoAnnotations.initMocks(this)`: 将打上Mockito标签的对象起作用，使得Mock的类被Mock，使用了Mock对象的类自动与Mock对象关联。

#### 测试Service

`MockitoJunitRunner `会自动处理`@Mock`与`@InjectMocks` 对象的关系。

测试类会模拟出`NotificationService` 和`AccountDAO ` 对象并将其注入到`AccountServiceImpl` .

```java
@RunWith(MockitoJUnitRunner.class)
public class AccountServiceTest {
 
    @Mock
    private NotificationService notificationService;
 
    @Mock
    private AccountDAO accountDAO;
 
    @InjectMocks
    private AccountServiceImpl accountService = new AccountServiceImpl();
 
    @Test
    public void createNewAccount() {
 
        // Expected objects
        String name = "Test Account";
        Account accountToSave = new Account(name);
        long accountId = 12345;
        Account persistedAccount = new Account(name);
        persistedAccount.setId(accountId);
 
        // Mockito expectations
        //import static org.mockito.Mockito.*;
        when(accountDAO.save(any(Account.class))).thenReturn(persistedAccount);
        doNothing().when(notificationService).notifyOfNewAccount(accountId);
 
        // Execute the method being tested     
        Account newAccount = accountService.createNewAccount(name);
 
        // Validation  
        assertNotNull(newAccount);
        assertEquals(accountId, newAccount.getId());
        assertEquals(name, newAccount.getName());
 		//验证方法是否被调用
        verify(notificationService).notifyOfNewAccount(accountId);
        verify(accountDAO).save(accountToSave);
    }
}
```

#### 测试Controller

```java
@RunWith(MockitoJUnitRunner.class)
public class ControllerTest {
    @Mock
    private ResService resService;
    @InjectMocks
    private IndexController indexController;
 
    private MockMvc mockMvc;
 
    //初始化Mock对象
    @Before
    public void setup() {
        // initialize mock object    			  /* this must be called for the @Mock annotations above to be processed and for the mock service to be injected into the controller under test. */
        MockitoAnnotations.initMocks(this);
        // Setup Spring test in standalone mode
        //模拟一个Mvc测试环境，注入controller, 通过build得到一个MockMvc， 
        //后面就用MockMvc的一些API做测试。
        this.mockMvc = MockMvcBuilders.standaloneSetup(indexController).build();
    }
    
    @Test
    public void test() throws Exception {
        
        //prepare test data
        FieldComparisonFailure e1 = new FieldComparisonFailure("Number", "3", "4");
        FieldComparisonFailure e2 = new FieldComparisonFailure("Number", "1", "2");
        
        //actually parameter haven't use, service was mocked
        String expect = "";
        String actual = "";
        
        //Sets a return value to be returned when the method is called
        when(resService.compare(expect, actual)).thenReturn(ImmutableList.of(e1, e2));
        
        //construct http request and expect response
        //MockMvcRequestBuilders.post()
       this.mockMvc
            .perform(post("/jsonCompare")
                     .accept(MediaType.APPLICATION_JSON) // 指定返回json类型数据
               .param("actual", actual)
               .param("expect", expect))
               //.contentType(MediaType.APPLICATION_JSON).content(projJson) // 发送Json数据给Controller
               .andDo(print()) //print request and response to Console: MockMvcResultHandlers.print()
               .andExpect(status().isOk())//MockMvcResultMatchers
               .andExpect(content().contentType("application/json;charset=UTF-8"))
               .andExpect(jsonPath("$", hasSize(2)))
               .andExpect(jsonPath("$[0].field").value("Number"))
               .andExpect(jsonPath("$[0].expected").value("3"))
               .andExpect(jsonPath("$[0].actual").value("4"))
               .andExpect(jsonPath("$[1].field").value("Number"))
               .andExpect(jsonPath("$[1].expected").value("1"))
               .andExpect(jsonPath("$[1].actual").value("2"));
         //verify Interactions with any mock
         //验证函数在传入特定参数的时候是否被调用。
         verify(testProjectService, times(1)).compare(expect, actual);
         verifyNoMoreInteractions(testProjectService);
    }
```

-   **perform**：**执行一个RequestBuilder请求**，会自动执行SpringMVC的流程并映射到相应的控制器执行处理；
-   **get**:声明发送一个get请求的方法。MockHttpServletRequestBuilder get(String urlTemplate, Object... urlVariables)：根据uri模板和uri变量值得到一个GET请求方式的。另外提供了其他的请求的方法，如：**post、put、delete**等。
-   **param**：添加request的参数，如上面发送请求的时候带上了了pcode = root的参数。假如使用需要**发送json数据格式**的时将不能使用这种方式.被@ResponseBody注解参数的解决方法为：`.contentType(MediaType.APPLICATION_JSON).content(jsonString)`  jsonString 为json格式请求参数。可以使用FastJSON等对数据进行转换得到
-   **andExpect**：添加ResultMatcher验证规则，验证控制器执行完成后**结果是否正确**（对返回的数据进行的判断）；
-   **andDo**：添加ResultHandler结果处理器，比如调试时打印结果到控制台（对返回的数据进行的判断）；
-   **andReturn**：最后返回相应的MvcResult；然后进行自定义验证/进行下一步的异步处理（对返回的数据进行的判断）；

## 集成Web测试

和独立单元测试唯一的区别就是，这次真实的启一个web服务，传入的是真实的参数，调用真实的service取得返回值，待单元测试跑完之后再将web服务停掉。

```java
//------------------类-----------------------//
@RunWith(SpringJUnit4ClassRunner.class) // 告诉Junit使用 Spring-Test 框架, 允许加载web 应用程序上下文。
@WebAppConfiguration //表明该类会使用web应用程序的默认根目录来载入ApplicationContext, 默认`value = "src/main/webapp" `
@ContextConfiguration //指定需要加载的 spring 配置文件的地址 ("file:src/main/resources/applicationContext.xml")

//------------------属性-----------------------//
//注入web环境的ApplicationContext容器；
@Autowired
private WebApplicationContext wac;

private MockMvc mockMvc;//这个对象是Controller单元测试的关键，它的初始化在setup方法里面。

//-------------------setup方法----------------------//
//创建一个MockMvc进行测试, 此为模拟真实的Spring MVC环境
MockMvcBuilders.webAppContextSetup(wac).build();

//--------------------test方法中---------------------//
mockMvc.perform// 发起一个http请求。
post(url)//表示一个post请求，url对应的是Controller中被测方法的Rest url。
param(key, value)//表示一个request parameter，方法参数是key和value。
andDo（print()）// 表示打印出request和response的详细信息，便于调试。
andExpect（status().isOk()）//表示期望返回的Response Status是200。
andExpect（content().string(is（expectstring））//表示期望返回的Response Body内容是期望的字符串。  
```

>   如果测试类继承自`AbstractJUnit4SpringContextTests` 那么不需要再使用`@RunWith(SpringJUnit4ClassRunner.class)`注解了。
>
>   如果继承自`AbstractTransactionalJUnit4SpringContextTests`，那么`@Transactional` 注解也不需要再使用。
>
>   Spring 4.2开始，`@Rollback` 取代`@TransactionConfiguration` 。事务管理器的配置使用`@Transactional(transactionManager = "")` 。默认是“ ”（空）

测试代码

```java
@ContextConfiguration(locations = "classpath:config/spring.xml")
//是否回滚数据，默认为true
@Rollback
public class MvcTest extends AbstractTransactionalJUnit4SpringContextTests
{
    @Autowired
    private WebApplicationContext wac;
    private MockMvc mockMvc;
  
    @Before
    public void setup() {
         MockMvcBuilders.webAppContextSetup(wac);
    }
    @Test
    public void testIndex() throws Exception
    {
        mockMvc.perform(get("/index/randSelect"))
                .andExpect(status().isOk());
    }
}
```

### 综合测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration({ "classpath:test-context.xml" })
@TransactionConfiguration(transactionManager = "transactionManager", defaultRollback = true)
@Transactional
public class AccountServiceSpringAndMockitoTest {
 
    @Mock
    private AuditService mockAuditService;
 
    @InjectMocks
    @Autowired
    private AccountService accountService;
 
    @Before
    public void setup() {
        MockitoAnnotations.initMocks(this);
    }
 
    @Test
    @Transactional
    public void deleteWithPermission() {
 
        // Set up Spring Security token for testing
        SecuredUser user = new SecuredUser();
        user.setUsername("test1");
        TestingAuthenticationToken token = new TestingAuthenticationToken(user, null, "accountFullAccess");
        SecurityContextHolder.getContext().setAuthentication(token);
 
        // Create account to be deleted
        Account accountToBeDeleted = accountService.createNewAccount("Test Account");
        long accountId = accountToBeDeleted.getId();
         
        // Mockito expectations
        doNothing().when(mockAuditService).notifyDelete(accountId);
 
        // Execute the method being tested
        accountService.delete(accountToBeDeleted);
 
        // Validation
        assertNull(accountService.get(accountId));
        verify(mockAuditService).notifyDelete(accountId);
    }
}
```
