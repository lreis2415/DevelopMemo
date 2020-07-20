# IntelliJ IDEA插件推荐

**“工欲善其事必先利其器”**

IntelliJ IDEA是一款优秀的Java开发IDE，结合各种插件，可以极大提高开发效率

**注意**：插件安装的越多，IDEA可能运行越慢，所以可以取消（勾选掉）某些不常用插件，例如自带的ASP等

以下推荐几款比较好用的IDEA插件
常用插件参考

https://blog.csdn.net/sujun10/article/details/72852939

https://blog.csdn.net/xlgen157387/article/details/78970079?utm_source=tuicool&utm_medium=referral 等

## 必备

-  插件，简化Java 代码编写。安装使用简介：https://zhuanlan.zhihu.com/p/32779910

    注意，安装之后需要启用IDEA的 **Enabling Annotation Processing**

- VueJs插件

- FreeMyBatisPlugin

### 编辑器增强
- **Key promoter** 快捷键提示插件

- **CodeGlance** 代码缩略图

- JavaDoc

- **Stack Overflow**

- 代码折叠
  不需要插件，使用 
  ```java
    //region [description]  
    //代码行   
    //endregion
  ```

- **GenerateSerialVersionUID**

  serialVersionUID是一个非常重要的字段，因为 Java 的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM 会把传来的字节流中的serialVersionUID与本地相应实体（类）的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。
   [详述 IntelliJ IDEA 中自动生成 serialVersionUID 的方法](https://blog.csdn.net/qq_35246620/article/details/77686098)

- **String Manipulation**  文本操作，比如转换大小写

- **HighlightBracketPair**  自动化高亮显示光标所在代码块对应的括号，可以定制颜色和形状 

- **IdeaVim**  Vim仿真插件。IdeaVim支持许多Vim功能，包括normal/insert/visual模式，motion键，删除/更改，标记，寄存器，一些Ex命令，Vim正则表达式，通过〜/ .ideavimrc，宏，窗口命令等进行配置的功能。
### 代码规范、检查
- **Alibaba Java Coding Guidelines** 【必备】

   代码规范性检查。直接安装可能下载超时。此时手动[下载](https://plugins.jetbrains.com/plugin/10046-alibaba-java-coding-guidelines)到本地并安装  
   [参考]( https://www.jianshu.com/p/e32c486b419e)

- **FindBugs-IDEA**

   检测代码中可能的bug及不规范的位置 

- 


### 框架支持
- VueJS
  Vue.js

- [Free Mybatis plugin](https://github.com/wuzhizhan/free-idea-mybatis)

  [下载地址](https://plugins.jetbrains.com/plugin/download?rel=true&updateId=40617)

- MyBatisCodeHelper

- Maven Helper

- 