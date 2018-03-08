[Java 编程的动态性，类和类装入](https://www.ibm.com/developerworks/cn/java/j-dyn0429/index.html)

~~~
在装入并初始化类时，JVM 内部会完成许多操作，包括解码二进制类格式、检查与其它类的兼容性、验证字节码操作的顺序以及最终构造 java.lang.Class 实例来表示新类。
这个 Class 对象成了 JVM 创建新类的所有实例的基础。它还是已装入类本身的标识 ― 对于装入到 JVM 的同一个二进制类，可以有多个副本，每个副本都有其自己的 Class 实例。
即使这些副本都共享同一个类名，但对 JVM 而言它们都是独立的类。
~~~

~~~
Java 类定义和 JVM 规范一起为运行时组装代码定义了功能极其强大的框架。通过使用类装入器，Java 应用程序能使用多个版本的类，否则这些类就会引起冲突。
类装入器的灵活性甚至允许动态地重新装入已修改的代码，同时应用程序继续执行。
~~~

[Java 编程的动态性，引入反射](https://www.ibm.com/developerworks/cn/java/j-dyn0603/)
~~~
带 Declared 与接入类型无关（公有 私有）
不带 Declared 则是取公有
~~~
~~~
不过－至少某些时候，围绕这些限制有一种简单的方法。
我在前面实例中使用的 Constructor 、 Field 和 Method 类都扩展了一个普通的基本类--&#160 java.lang.reflect.AccessibleObject 类。
该类定义一种 setAccessible 方法，使您能够启动或关闭对这些类中其中一个类的实例的接入检测。
唯一的问题在于如果使用了安全性管理器，它将检测正在关闭接入检测的代码是否许可了这样做。
如果未许可，安全性管理器抛出一个例外。
如果您在命令行添加了JVM参数 -Djava.security.manager 以实现安全性管理器，它将再次失败，除非您定义了 ReflectSecurity 类的许可权限。
~~~

[Java 编程的动态性，应用反射](https://www.ibm.com/developerworks/cn/java/j-dyn0715/index.html?ca=drs-)