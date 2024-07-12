# 设计目标&设计理念&核心
设计目标
	一站式轻量级应用开发平台
设计理念
	充分支持OO（面向对象）设计方法，使用IOC实现对象耦合关系管理、依赖反转，实现解耦
核心
	ioc容器和aop模块，ioc容器管理pojo对象和之间的耦合关系，通过aop动态增强服务
# 优缺点
## 优点
+ 解耦，方便开发
+ aop，面向切面变成，权限拦截、运行监控
+ 声明式事务，通过配置完成事务的管理
+ 方便测试，自测
+ 可以集成其他优秀框架
+ 降低javaEE API饿使用难度
## 缺点
+ 大而全
+ 依赖反射
# spring框架都用到了那些设计模式
+ 工厂模式，BeanFactory就是简单工厂模式
+ 单例模式，Bean默认为单例
+ 代理模式，aop用了JDK的动态代理和字节码生成技术
+ 模版方法，用来解决代码重复问题
+ 观察者模式，广播
# 核心容器springcontext模块
？

# spring控制反转（IOC） inversion of control
## 什么是Spring IOC
控制反转，把传统的由程序代码直接操控对象的调用权交给容器，通过容器来实现对象逐渐的装配和管理。
IOC负责创建对象，管理度喜庆，装配对象，配置对象，并管理这些对象的整个生命周期
## IOC作用
+ 管理对象的创建、依赖关系
+ 解耦
+ 托管类的产生，通过spring提供的接口同一化在生命周期做事
+ 配置管理，负责读取配置信息
## IOC的优点
+ IOC把应用的代码量降低
+ 使服务易于测试
+ 松散耦合
+ ioc容器支持加载服务时使用饿汉或者懒汉
## IOC的实现原理
工厂模式+反射
## IOC支持的功能
+ 依赖注入
+ 依赖检查
+ 自动装配
+ 支持集合
+ 制定初始化和销毁方法
+ 支持回调
## BeanFactory和ApplicationContext有什么区别
都可以当Spring的容器，其中ApplicationContext是BeanFactory的子接口

### 依赖关系
BeanFactory
	是spring里面最底层的接口，包含各种bean的定义，读取bean配置文件，管理bean的加载、实例化，控制bean的生命周期，维护bean的依赖关系
ApplicationContext
	作为BeanFactory的派生，除了BeanFactory的功能还提供了
	1.继承MessageSoruce，支持国际化
	2.同意的资源文件访问方式
	3.提供在监听器中注册bean的时间
	4.同时加载多个配置文件
	5.载入多个（有继承关系）的上下文
### 加载方式
BeanFactory
	延迟加载，只有在使用的bean（调用getBean（）），才会对该Bean进行加载实例化
ApplicationContext
	在容器启动的时候，一次性创建所有的Bean，启动后会预载入所有的单例Bean，这样会占用内存空间
### 创建方式
BeanFactory
	以编程的方式创建
ApplicationContext
	还能以生命的方式创建，例如使用ContextLoader
## Spring如何设计容器，BeanFactory和ApplicationContext的关系
BeanFactory可以理解为就是个HashMap，key是Beanname，value是Bean实例。
ApplicationContext继承了多个接口，可以有更多的功能

## <font color=#f2071f>IOC在spring的流程</font>
	1.加载配置文件，解析成BeanDefinition放在Map中
	2.调用getBean的时候，从BeanDefinition所属的map中，拿出class对象进行实例化，如果存在依赖关系，将递归调用getBean方法

# 什么是spring的依赖注入
依赖注入
	组件之间的依赖关系由容器在应用系统运行期来决定，由容器动态的将某种依赖关系的目标对象注入到相关联的组件之中
## 依赖注入的基本原则
应用组件不应该负责查找资源，配置对象的工作应该由IOC容器负责，它会将符合依赖关系的对象通过set方法或者构造器传入
## 依赖注入的优势
+ 与代码无关
+ 不依赖于容器的api
+ 不需要特殊的接口
# Spring Beans
## 什么是Spring Beans
是形成Spring应用的主干的java对象，它们被spring IOC容器初始化，装配，和管理
## 一个Spring Beans包含什么
包含容器必知的所有配置元数据，包括如何创建一个bean，生命周期详情，它的依赖
## spring的几种配置方式
+ xml配置文件
+ 注解
+ java配置
## spring的bean的作用域
+ singleton，bean在每个springioc容器中只有一个实例
+ prototype，每次请求bean都会产生新的
+ request，每次http请求都会创建一个bean
+ session
+ global-session
# spring中的单例bean不是线程安全的

# 解释spring框架中的bean的生命周期
![[Pasted image 20240710193022.png]]

实例化
	创建bean
属性赋值
	填充属性，赋值
初始化
	1.执行实现了Aware接口的方法
	2.执行实现了了BeanPostProcessor接口的before方法
	3.执行配置好的init-method方法
	4.执行实现了了BeanPostProcessor接口的after方法
销毁
销毁后
	1.执行实现了DisposableBean接口的destroy方法
	2.追星destory-method方法
## 生命周期常用扩展点
### 影响多个Bean的接口
instantiationAwareBeanPostProcessor
	实例化前后调用
BeanPostProcessor
	初始化前后调用
### 只调用一次的接口
1.Aware类型的接口
	初始化之前调用，为了能够拿到spring容器里的一些资源
	![[Pasted image 20240710191731.png]]
## 生命周期接口
initializingBean
	初始化bean相关的接口，读去玩配置文件后执行
DisposableBean
	对应的销毁后执行
## BeanPostProcessor注册的时机与执行顺序
![[Pasted image 20240710192502.png]]
1.读取配置信息到spring容器中的定义注册表（beandefinition？）
2.根据定义注册表注册处实例化bean。
3.将bean放到缓存中
### 注册时机
在refresh方法里，将注册beanPostProcesser的方法放到注册bean之前
### 执行顺序
+ PriorityOrdered，先被执行，根据接口返回值来确定顺序
+ Ordered，后被执行，同样通过接口返回值

# 使用@Autowired自动装配的过程
要使用注解来自动装配，需要在spring配置文件里进行配置<cont:annotation-config/>
在启动IOC时，容器会自动装载一个AutowiredAnnotationBeanPostProcessor后置处理器，
当容器扫描到到注解后，就会在容器中查找需要的bean，并装配
+ 查询结果1个，自动
+ 多个，按照名字
+ 没有，则报错

# FactoryBean和BeanFactory有什么区别
BeanFactory
	bean的工厂，ApplicationContext的父类，IOC容器的核心，负责生产和管理Bean对象
	不允许被直接使用
Factorybean
	是个bean，可以通过实现FactoryBean接口定制实例化Bean的逻辑，代理一个Bean对象
	
# 循环依赖
循环依赖
	两个或两个以上的bean互相引用对方，形成闭环
构造器的循环依赖
	无法解决
field属性的循环依赖
	scope为singletion的循环依赖
	scope为prototype的循环依赖，无法解决
# 三级缓存
+ 成品：一级缓存singleton，用来保存实例化、注入、初始化后的bean实例
+ 半成品：二级earlySingleton，用来保存实例化完成的bean实例，只有一个引用一个地址
+ 原材料工厂：三级singletonFactories，保存bean的创建工厂，便于创建代理对象

![[Pasted image 20240710214907.png]]

1. **创建 Bean A**：
    - Spring 尝试创建 Bean A，首先将 Bean A 放入三级缓存 `singletonFactories` 中。
    - 在填充 Bean A 的属性时，发现 Bean A 依赖于 Bean B。
2. **创建 Bean B**：
    - Spring 尝试创建 Bean B，首先将 Bean B 放入三级缓存 `singletonFactories` 中。
    - 在填充 Bean B 的属性时，发现 Bean B 依赖于 Bean A。
3. **解决 Bean B 对 Bean A 的依赖**：
    
    - Spring 从三级缓存 `singletonFactories` 中获取 Bean A 的工厂对象，并创建一个早期暴露的 Bean A，将其放入二级缓存 `earlySingletonObjects` 中。
    - Bean B 获取到早期暴露的 Bean A，完成属性填充，Bean B 初始化完成，并放入一级缓存 `singletonObjects` 中。
4. **解决 Bean A 对 Bean B 的依赖**：
    
    - Bean A 获取到完全初始化好的 Bean B，完成属性填充，Bean A 初始化完成，并放入一级缓存 `singletonObjects` 中。

## 必须要三级缓存吗，二级可以吗
第二级缓存的意义：
1. 存储已经实例化但是尚未完全初始化的bean的早期引用
2. 避免重复创建bean
	1. 三级缓存中放的是生成具体对象的匿名内部类，可以生产代理对象或普通对象
	2. 如果只有二级缓存，那么每一次生产的对象在多线程环境下可能不一样

# 注解
## @Component、@Controller、@Repository、@Service
@Component
	普通bean
@Controller
	将类标记为Spring Web MVC控制器，只有被这个注解标注的才会被拦截器拦截
@Service
	标注为业务处理
@Repository
- 用于标记一个类为数据访问层组件。
- 通常用于数据库操作，如DAO（Data Access Object）。
- 是`@Component`的特化，表示这个类是一个数据访问组件。
- Spring会对`@Repository`注解的类进行一些额外的异常转换处理，使其更适合数据访问层的操作。

## @Autowired和@Resource的区别
@Autowired
	按照类型装配注入，默认情况下依赖对象必须存在
@Resource
	按照名称装配，找不到名称才去找类型来装配
## RequestMapping
用于映射url

# AOP
面向切面编程，将于业务无关却会影响到多个对象的公共行为和逻辑，抽取封装一个模块。
可以减少重复代码，降低耦合度。可用于权限认证、日志、事务处理等
## Spring AOP的实现模式
使用动态代理，不会修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含目标对象的全部方法，并做了增强，回调原方法。


# 事务
1. 编程式事务，通过编程方式管理事务
2. 声明式事务，通过注解和xml管理事务
事务的本质就是数据库对事务的支持
## 事务隔离
1. 使用数据库的隔离级别
2. 读未提交，最低隔离级别，事务未提交前就可以被读取（幻读、脏读、不可重复读）
3. 读提交，事务提交后才可被读取，默认级别（幻读，不可重复读）
4. 读可重复，保证多次读取同一数据，其值都和事务开始时相同，禁止读取别的事务未提交的数据（幻读），mysql默认级别
5. 序列化（串行化），无上述三个
## 事务管理的优点
1. 为不同的事务api提供一个模式
2. 为编程式事务管理提供一套简单的API
3. 支持声明式事务
## 声明式事务的优缺点
优点：
	对代码影响小
缺点：
	只能作用于方法级别
