# java基础

### JDK与JRE区别

**JRE(Java Runtime Enviroment)** **是Java的运行环境**。面向Java程序的使用者，而不是开发者。如果你仅下载并安装了JRE，那么你的系统只能运行Java程序。JRE是运行Java程序所必须环境的集合，包含JVM标准实现及 Java核心类库。它包括Java虚拟机、Java平台核心类和支持文件。它不包含开发工具(编译器、调试器等)。

**JDK(Java Development Kit)**又称J2SDK(Java2 Software Development Kit)，是Java开发工具包，它提供了Java的开发环境(提供了编译器javac等工具，用于将java文件编译为class文件)和运行环境(提 供了JVM和Runtime辅助包，用于解析class文件使其得到运行)。如果你下载并安装了JDK，那么你不仅可以开发Java程序，也同时拥有了运 行Java程序的平台。JDK是整个Java的核心，包括了Java运行环境(JRE)，一堆Java工具tools.jar和Java标准类库 (rt.jar)。

### 字符串初始化

String s="tt";

String ss=newString("ff");

new，会在java的堆内存中创建对象, 有几个new就有几个对象。
直接赋值, 是从字符串常量池中取值。
例如 String str1 = "a"; String str2 = "a"; System.out.println(str1==str2);结果为true,地址值
都是字符串长量池中的"a".

String str3 = new String("a");String str4 = new String("a");System.out.println(str3==str4);
结果为false. 应为new会在堆中创建两个字符串对象, 地址值不同

### 字符串不可变的好处

不可变有什么好处

- 安全：多线程下对资源做写操作有危险。不可变对象不能被写，所以线程安全。
- 可以共用一个实例（在多线程中共享一个不可变对象而不用担心线程安全问题）：当代码中出现字面量形式创建字符串对象时，JVM首先会对这个字面量进行检查，如果字符串常量池中存在相同内容的字符串对象的引用，则将这个引用返回，否则新的字符串对象被创建，然后将这个引用放入字符串常量池，并返回该引用。String one = “someString”；String two = “someString”，都用字面量“someString赋值”，他们其实都指向同一个内存地址。当代码中出现字面量形式创建字符串对象时，JVM首先会对这个字面量进行检查，如果字符串常量池中存在相同内容的字符串对象的引用，则将这个引用返回，否则新的字符串对象被创建，然后将这个引用放入字符串常量池，并返回该引用。（PS：运行时常量池是方法区的一部分，用于存放各种字面量和符号引用）
- String是几乎每个类都会使用的类，特别是作为Hashmap之类的集合的key值时候，mutable的String有非常大的风险。