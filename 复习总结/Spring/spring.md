# spring

**AOP**即面向切面编程，我的理解是实现一个纵切面，纵切面挡在某个数据对象或事务前，所有依赖数据对象或代码块的方法被访问前都需要先被这个切面阻挡并且进行预处理。这个和python中装饰器的功能很相似，装饰器也是对朴实函数的预 处理和后处理。 

**Spring IoC** 的实现机制：Spring 中的 IoC 的实现原理就是工厂模式加反射机制。容器管理着 Bean 的生命周期，控制着 Bean 的依赖注入。BeanFactory 粗暴简单，可以理解为就是个 HashMap，Key 是 BeanName，Value 是 Bean 实例。通常只提供注册（put），获取（get）这两个功能。我们可以称之为 “低级容器”。低级容器 加载配置文件（从 XML，数据库，Applet），并解析成 BeanDefinition 到低级容器中。加载成功后，高级容器启动高级功能，例如接口回 调，监听器，自动实例化单例，发布事件等等功能。 

**bean循环依赖**。先调用构造函数进行实例化，然后填充属性，再接着进行其他附加操作和初始化，正是这样的生命周期，才有了Spring的解决循环依赖，这样的解决机制是根据Spring框架内定义的三级缓存来实现的，也就是说：三级缓存解 决了Bean之间的循环依赖。我们从源码中来说明。检测循环依赖相对比较容易，Bean在创建的时候可以给该Bean打标，如果递归调用回来发现正在创建中的话，即说明了循环依赖了。如果发生循环依赖，spring就用未进行属性填充的bean作为 其属性。“A的某个field或者setter依赖了B的实例对象，同时B的某个field或者setter依赖了A的实例对象”这种循环依赖的情况。A首先完成了初始化的第一步，并且将自己提前曝光到singletonFactories中，此时进行初始化的第二步，发现 自己依赖对象B，此时就尝试去get(B)，发现B还没有被create，所以走create流程，B在初始化第一步的时候发现自己依赖了对象A，于是尝试get(A)，尝试一级缓存singletonObjects(肯定没有，因为A还没初始化完全)，尝试二级缓存 earlySingletonObjects（也没有），尝试三级缓存singletonFactories，由于A通过ObjectFactory将自己提前曝光了，所以B能够通过ObjectFactory.getObject拿到A对象(虽然A还没有初始化完全，但是总比没有好呀)，B拿到A对象后顺利完 成了初始化阶段1、2、3，完全初始化之后将自己放入到一级缓存singletonObjects中。此时返回A中，A此时能拿到B的对象顺利完成自己的初始化阶段2、3，最终A也完成了初始化，进去了一级缓存singletonObjects中，而且更加幸运的 是，由于B拿到了A的对象引用，所以B现在hold住的A对象完成了初始化。 

**单例，无状态**：无状态Scope是最简单、生存期间最短的一种Scope，我们所有通过new出来的非静态对象都属于这种Scope，它随着使用这个对象的对象（可能是依赖者类，也可能是依赖者类里的一个方法）的消亡而消亡。反之，单例则是 生存期间最长的一种Scope，它以静态的模式生存在JVM的“旧年代区域”，永远不会被JVM回收。在自动注入的框架中，所谓单例是指以依赖注入容器（Injector）为单位唯一存在的非静态变量，从而避免了过多占用JVM的旧年代区域的内 存等问题 

**BeanFactory：**

是Spring里面最低层的接口，提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能；

**ApplicationContext：**

应用上下文，继承BeanFactory接口，它是Spring的一各更高级的容器，提供了更多的有用的功能；

1) 国际化（MessageSource）

2) 访问资源，如URL和文件（ResourceLoader）

3) 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层  

4) 消息发送、响应机制（ApplicationEventPublisher）

5) AOP（拦截器）

**两者装载bean的区别**

**BeanFactory：**

BeanFactory在启动的时候不会去实例化Bean，中有从容器中拿Bean的时候才会去实例化；

**ApplicationContext：**

ApplicationContext在启动的时候就把所有的Bean全部实例化了。它还可以为Bean配置lazy-init=true来让Bean延迟实例化； 

