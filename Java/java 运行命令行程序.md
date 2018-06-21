# java 运行命令行程序

## 使用Java的API
>http://wuhongyu.iteye.com/blog/461477
>http://blog.csdn.net/fd_mas/article/details/50147701
>http://www.cnblogs.com/xxpal/articles/824963.html

Java管理进程，API级别是使用：`Runtime.getRuntime().exec("cmdline");`这个方法。
1. `exec(String command)`
2. `exec(String command, String envp[], File dir)`
3. `exec(String cmd, String envp[])`
4. `exec(String cmdarray[])`
5. `exec(String cmdarray[], String envp[])`
6. `exec(String cmdarray[], String envp[], File dir)`

Java在执行命令时输出到某个Buffer里，这个Buffer是有容量限制的，如果满了一直没读取，就会一直等待，造成进程锁死的现象。
通过Process的`getInputStream()`，`getOutputStream()`和`getErrorStream()`方法可以得到输入输出流，然后通过`InputStream`可以得到程序对控制台的**输出信息**，通过`OutputStream`可以给程序输入指令,这样就达到了程序的交换功能。
```java
Process p = Runtime.getRuntime().exec(".\\p.exe");
BufferedInputStream in = new BufferedInputStream(p.getInputStream());  
BufferedReader inBr = new BufferedReader(new InputStreamReader(in));  
String lineStr;  
while ((lineStr = inBr.readLine()) != null){
      //获得命令执行后在控制台的输出信息  
      System.out.println(lineStr);// 打印输出信息  
} 
//process.waitfor();//等待子进程完成再往下执行。
if (p.waitFor() != 0) {  
    if (p.exitValue() == 1)//p.exitValue()==0表示正常结束，1：非正常结束  
       System.err.println("命令执行失败!");  
}  
inBr.close();  
in.close();  
```

使用原生Runtime和Process方式时，必须手工为调用bat脚本加上`cmd /c`，比如把`test.bat`脚本拼接成`cmd /c`才向`Runtime.exec`方法传入这个脚本作为第一个参数(?)

---
## 使用 Apache Commons Exec
>https://commons.apache.org/proper/commons-exec/tutorial.html
```xml
<dependency>
     <groupId>org.apache.commons</groupId>
     <artifactId>commons-exec</artifactId>
     <version>1.3</version>
</dependency>
```
### 例子
#### 基本用法
```java
String line = "AcroRd32.exe /p /h " + file.getAbsolutePath(); // 命令：打印pdf
CommandLine cmdLine = CommandLine.parse(line);// 解析命令行
DefaultExecutor executor = new DefaultExecutor(); // 执行器
int exitValue = executor.execute(cmdLine); //执行，exitValue 为 1
```
注意：命令中涉及到**文件目录**的部分最好用双引号包裹起来，避免其中的 **空格** 等造成执行失败
`String line = "AcroRd32.exe /p /h \"" + file.getAbsolutePath() + "\"";`

#### 设置正常退出值
上述代码执行**成功**后会抛出异常：Acrobat Reader返回的退出值 exitValue 为 `1` ，而 `1` 经常被认为是**非正常结束**（`0`才是正常结束）。因此，需要修改程序为：
```java
String line = "AcroRd32.exe /p /h " + file.getAbsolutePath();
CommandLine cmdLine = CommandLine.parse(line);
DefaultExecutor executor = new DefaultExecutor();
executor.setExitValue(1); // 设置正常退出值为1。即若命令执行后返回1，表示正常结束
int exitValue = executor.execute(cmdLine);
```
如果程序有多个退出值，可使用`executor.setExitValues(int[]);`函数进行处理。

#### 进程监测 Watchdog
当命令执行超时，或由于其他原因需要结束进程时，可以使用 Watchdog
```java
String line = "AcroRd32.exe /p /h " + file.getAbsolutePath();
CommandLine cmdLine = CommandLine.parse(line);
DefaultExecutor executor = new DefaultExecutor();
ExecuteWatchdog watchdog = new ExecuteWatchdog(60000); //设置超时时间，毫秒
executor.setWatchdog(watchdog);
executor.setExitValue(1);
int exitValue = executor.execute(cmdLine);
```
强制结束进行
`watchdog.destroyProcess();//终止进程 `

注：因为执行超时而被终止时，退出值是 1 

#### 递增式构建命令
 commons-exec 会将字符串命令根据单、双引号分割转换为字符串数组。因此，如果在文件目录/文件名等中存在空格，必须使用双引号包裹起来。这种书写命令的方法，经常容易出错，忘记添加必要的引号。
因此，commons-exec 推荐使用递增式命令构建方法：
```java
Map map = new HashMap();
map.put("file", new File("invoice.pdf")); // 使用File对象，屏蔽不同OS的区别
CommandLine cmdLine = new CommandLine("AcroRd32.exe");
cmdLine.addArgument("/p");
cmdLine.addArgument("/h");
cmdLine.addArgument("${file}");
cmdLine.setSubstitutionMap(map);
// create the executor and consider the exitValue '1' as success
DefaultExecutor executor = new DefaultExecutor();
executor.setExitValue(1);
ExecuteWatchdog watchdog = new ExecuteWatchdog(60000);
executor.setWatchdog(watchdog);
int exitValue = executor.execute(cmdLine);
```

#### 非阻塞式/异步执行
上面的执行外部命令都是阻塞式，也就是在执行外部命令时，**当前线程是阻塞的**，直到线程执行完毕或被watchdog关闭。
```java
CommandLine cmdLine = new CommandLine("AcroRd32.exe");
cmdLine.addArgument("/p");
cmdLine.addArgument("/h");
cmdLine.addArgument("${file}");
HashMap map = new HashMap();
map.put("file", new File("invoice.pdf"));
commandLine.setSubstitutionMap(map);
//使用 DefaultExecuteResultHandler 处理外部命令执行的结果，释放当前线程。
DefaultExecuteResultHandler resultHandler = new DefaultExecuteResultHandler();

ExecuteWatchdog watchdog = new ExecuteWatchdog(60*1000);
Executor executor = new DefaultExecutor();
executor.setExitValue(1);
executor.setWatchdog(watchdog);
executor.execute(cmdLine, resultHandler);

// some time later the result handler callback was invoked so we
// can safely request the exit value
int exitValue = resultHandler.waitFor();//resultHandler.waitFor(5000);//等待5秒。
```
之后，可以通过`resultHandler.hasResult()`、`resultHandler.getExitValue()`、`resultHandler.getException()`获得需要信息。 
注意：`getException();`得到的是Exec自己的异常，不是应用程序(比如JAVA)代码里面抛出的异常。

若报错 `The process has not exited yet therefore no result is available`

说明没有加` resultHandler.waitFor();`

#### 获得进程输出信息
>http://blog.csdn.net/fd_mas/article/details/50147701

通过`PumpStreamHandler`截获进程的各种输出，包括`output` 和 `error stream`。

```java
String cmdStr = "ping www.baidu.com";
final CommandLine cmdLine = CommandLine.parse(cmdStr);
DefaultExecutor executor = new DefaultExecutor();
executor.setExitValue(1);
//ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
//ByteArrayOutputStream errorStream = new ByteArrayOutputStream();

ByteArrayOutputStream out = new ByteArrayOutputStream();
executor.setStreamHandler(new PumpStreamHandler(out));

int exitValue = executor.execute(cmdLine);

final String result =out.toString().trim();// out.toString("gbk"); //设置编码
System.out.println(result);
//这个result就是ping输出的结果。如果是JAVA程序，抛出了异常，也被它获取。
```

### 实例

http://commons.apache.org/proper/commons-exec/xref-test/org/apache/commons/exec/TutorialTest.html