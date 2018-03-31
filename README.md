# 编程备忘录

> 授人以渔，不如授人以渔。
>
> ​                                                    ——语出《淮南子·说林训》

## 写在前面

小组的科研工作围绕EGC（含CyberSoLIM、DTA、流域模拟和情景分析）展开。其中涉及到较多的开发工作，目前常用的编程语言有C++、Java、Python、Fortran等，为了促进高效地学习、合作，我们建立了这个编程备忘录库，集入门培训、示例代码、常见问题解答、学习资料推荐等于一体，方便索引学习。

希望此举能够抛砖引玉，集思广益，使编程备忘录逐步丰富、完善！

## 小组项目索引

1. [CyberSoLIM](https://github.com/lreis2415/cybersolim)：SoLIM的Web版，编程语言为 Java 和 JavaScript (ES6)，采用前后端分离方式
2. [SEIMS](https://github.com/lreis2415/SEIMS)：分布式流域过程模型及情景优化工具，编程语言为C++和Python
3. [SimDTA](https://github.com/lreis2415/SimDTA)：简化数字地形分析软件，编程语言为VB6.0
4. [AutoFuzSlpPos](https://github.com/lreis2415/AutoFuzSlpPos)：模糊坡位自动化提取软件，编程语言为C++
5. [PyGeoC](https://github.com/lreis2415/PyGeoC)：简易地学计算Python库，目前已应用于[SEIMS](https://github.com/lreis2415/SEIMS)和[AutoFuzSlpPos](https://github.com/lreis2415/AutoFuzSlpPos)
6. [RasterClass](https://github.com/lreis2415/RasterClass)：基于GDAL和MongoDB（可选）的跨平台栅格数据读写类，目前已集成于[SEIMS](https://github.com/lreis2415/SEIMS)
7. [UtilsClass](https://github.com/lreis2415/UtilsClass)：跨平台C++基本操作类

## 文档编写基本约定

1. 采用Markdown编写，推荐使用Typora或MarkdownPad2
2. 每个分类下推荐包含以下文件：
   + README.md：针对该分类的描述和内容索引
   + 每项培训内容单独一个`md`文件，如`Cpp`类中的“跨平台代码编译和项目管理”为`cross_platform.md`
   + src文件夹：用于存放示例代码
   + QA文件夹：常见问题描述和解决方案，每个问题单独一个`md`文件，并在`README.md`中做好索引






---

### 个人博客

- 侯志伟   https://houzw.github.io/

  一些比较琐碎的、日常学习的内容，会放到博客中，感兴趣的可以参考。