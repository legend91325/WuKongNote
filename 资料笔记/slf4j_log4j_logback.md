### 官方文档的这一段话已经明确描述了三者的关系。slf4j译为简单日志门面，是日志框架的抽象。而log4j和logback是众多日志框架中的几种。
    

![concrete-bindings](/pics/concrete-bindings.png)


### 可以看到logback是直接实现了slf4j的接口，是不消耗内存和计算开销的。而log4j不是对slf4j的原生实现，所以slf4j api在调用log4j时需要一个适配层。


### 常见文件 互相无限递归调用
---
log4j-over-slf4j和slf4j-log4j12是跟java日志系统相关的两个jar包，当它们同时出现在classpath下时，就可能会引起堆栈溢出异常。
原因是循环桥接，导致stack-overflow
    
[SLF4J user manual](https://www.slf4j.org/manual.html)

[slf4j log4j logback关系详解和相关用法](https://www.cnblogs.com/Sinte-Beuve/p/5758971.html)

[log4j-over-slf4j与slf4j-log4j12共存stack overflow异常分析](https://blog.csdn.net/kxcfzyk/article/details/38613861)
