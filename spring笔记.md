title: spring笔记
date: 2016-06-09 22:30:50
categories: [java,spring]
tags:
---
spring构造注入
public class Dog implements Animal{

	@Override
	public void eat() {
		System.out.println("dog eat");
	}

}

public class Person {
	private Dog dog;

	public Person(Dog dog) {
		this.dog = dog;
	}

	public void sayEat() {
		dog.eat();
	}
}
每个<bean.../> 元素默认驱动Spring调用 **该类无参数的构造器** 来创建实例，并将该实例作为Spring容器中的Bean

<?xml version="1.0" encoding="UTF-8"?>
<beans>
	<!--  <property/> 元素的name属性值决定执行哪个setter方法，
	value（基本类型/包装类）或ref（引用类型）决定执行setter方法的传入参数 -->
	<!-- constructor-arg 元素代表一个构造器参数，如果包含N个元素，则调用带N个参数的构造器创建对象 -->
	<bean id="person" class="com.bean.Person">
		<constructor-arg ref="dog"></constructor-arg>
	</bean>
	<bean id="dog" class="com.bean.Dog"></bean>
</beans>

public class Test {
	public static void main(String[] args) {
		ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
		Person p = (Person) ctx.getBean("person");
		p.sayEat();
	}
}

若要改为设值注入
spring.xml改为property方式
<!--  <property/> 元素的name属性值决定执行哪个setter方法，
	value（基本类型/包装类）或ref（引用类型）决定执行setter方法的传入参数 -->
<bean id="person" class="com.bean.Person">
		<property name="dog" ref="dog"></property>
	</bean>
Person.java去掉构造方法，加set方法
public void setDog(Dog dog){
		this.dog = dog;
	}

bean配置项
id class  scope   Constructor arguments    Properties   Autowiring mode
lazy-initialization mode   Initialization/destruction method
配置单例
<bean id="beanScope" class="com.scope.BeanScope" scope="singleton">

A对象需要调用B对象方法的情形，被Spring成为依赖。即A对象依赖B对象。
设值注入
<property name="axe" ref="axe"></property>
构造注入
<constructor-arg ref="axe"></constructor-arg>
建议采用设值注入为主，构造方法为辅的注入策略。对于依赖关系无需变化的注入，尽量采用构造注入；而其他依赖关系的注入，则考虑采用设值注入
<constructor-arg.../> 元素可以指定index属性，用于指定该构造参数值将作为第几个构造参数值。index="0" 表明该构造参数值将作为第一个构造参数值
为了更明确的指定数据类型，Spring允许为<constructor-arg.../> 指定一个type属性 eg<constructor-arg value="23" type="int">
如果需要加载多个佩文文件来创建Spring 容器 ，则应该采用BeanFactory的子接口ApplicationContext
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml","second.xml");
当系统创建ApplicationContext 容器时，默认会预初始化所有的singleton Bean,包括调用构造器创建该Bean的实例，并根据<property/>元素执行setter方法。
为了阻止Spring容器初始化容器中的single bean,可以为<bean>元素指定lazy-init="true",阻止容器预初始化该bean

ApplicationContext的事件机制
```
public class EmailEvent extends ApplicationEvent

public class EmailNotifier implements ApplicationListener {
	@Override
	public void onApplicationEvent(ApplicationEvent event) {
		if(event instanceof EmailEvent){
			EmailEvent emailEvent = (EmailEvent) event;
			System.out.println("receive address "+emailEvent.getAddress());
			System.out.println("content is  "+emailEvent.getText());

		}else{
			System.out.println("other event: ");
		}
	}
}
测试代码
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
		EmailEvent emailEvent = new EmailEvent("test", "spring@163.com", "this is a test");
		ctx.publishEvent(emailEvent);
输出
other event:
receive address spring@163.com
content is  this is a test
```
让bean获取spring容器
private ApplicationContext ctx;
实现 ApplicationContextAware 接口 ，覆写 public void setApplicationContext(ApplicationContext ctx){
	this.ctx = ctx;
}
当spring容器创建该bean后，自动调用该方法时，它会把自身作为参数传入该方法。

Bean 的基本定义和Bean别名
<beans.../> 可以指定如下属性
default-lazy-init  指定该beans元素下配置的所有Bean默认的延迟初始化行为
default-merge   指定所有Bean默认的merge行为
default-autowire  指定所有Bean默认的自动装配行为
default-autowire-candidates: 指定所有Bean默认是否作为自动装配的候选Bean
default-init-method: 指定所有Bean默认的初始化方法
default-destroy-method: 所有Bean默认的回收方法

<beans>元素所能指定的属性都可以在每个<bean> 子元素中指定。当二者所指定的属性不一致时，<bean>会覆盖<beans>指定的属性
构造注入就是通过<constructor-arg.../> 驱动Spring执行有参数的构造器；设值注入就是通过<property.../> 驱动Spring执行setter方法

注入集合值
java代码 如下
```
public class Chinese {
	private List<String> schools;
	private Map scores;
	private Map<String,Axe> phaseAxes;
	private Properties health;
	private Set axes;
	private String[] books;
}
```
spring.xml 如下
```
	<bean id="chinese" class="com.yuchuan.Chinese">
		 <property name="schools">
		 	<list>
		 		<value>small</value>
		 		<value>medium</value>
		 		<value>big</value>
		 	</list>
		 </property>
		 <property name="scores">
		 	<map>
		 		<entry key="math" value="8"></entry>
		 		<entry key="english" value="8"></entry>
		 		<entry key="chinese" value="8"></entry>
		 	</map>
		 </property>

		 <property name="phaseAxes">
		 	<map>
		 		<entry key="origin" value-ref="stoneAxe"></entry>
		 		<entry key="modern" value-ref="stealAxe"></entry>
		 	</map>
		 </property>
		 <property name="health">
		 	<props>
		 		<prop key="AA">normal</prop>
		 		<prop key="BB">175</prop>
		 	</props>
		 </property>
		 <property name="axes">
		 	<set>
		 		<value>普通字符串</value>
		 		<bean class="com.yuchuan.StoneAxe"></bean>
		 		<ref bean="stoneAxe"/>
		 		<ref bean="stealAxe" />
		 		<list>
		 			<set>
		 				<value type="int">30</value>
		 			</set>
		 		</list>
		 	</set>

		 </property>
		 <property name="books">
		 	<list>
		 		<value>java</value>
		 		<value>C++</value>
		 		<value>python</value>
		 	</list>
		 </property>
	</bean>
	<bean id="stoneAxe" class="com.yuchuan.StoneAxe"></bean>
	<bean id="stealAxe" class="com.yuchuan.StoneAxe"></bean>

</beans>
```
说明：
value 指定集合元素是基本类型或字符串类型
ref  集合元素是容器中的另一个bean实例
bean  指定集合元素是一个嵌套bean
list、set、map 指定集合
key-ref  如果map key 是容器中的另一个bean实例，则可使用该属性指定容器中其他bean的id
value-ref  如果map value是容器中的另一个bean实例，则可使用该属性指定容器中其他bean的id
数组和list用法一样

建议Spring中的bean应满足如下原则：
1. 尽量为每个bean实现类提供无参数的构造器
2. 接受构造注入的bean，则应提供对应的、带参数的构造函数
3. 接受设值注入的bean，则应提供对应的setter方法，并不要求提供对应的getter方法

spring提供两种方式在bean全部属性设置成功后执行特定行为：
1. 使用init-method属性
2. 实现InitializingBean 接口。覆写 afterPropertiesSet()方法
如果同时采用这两种方法，则先执行 InitializingBean 接口中定义的方法，然后执行init-method 方法

```
public class China implements Person, InitializingBean, BeanNameAware, ApplicationContextAware {
	private Axe axe;

	public void setAxe(Axe axe){
		System.out.println("axe setter...");
		this.axe = axe;
	}
	public China(){
		System.out.println("china构造方法");
	}
	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		System.out.println("====setApplicationContext===");
	}

	@Override
	public void setBeanName(String name) {
		System.out.println("====setBeanName====");
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("afterPropertiesSet");
	}

	@Override
	public void useAxe() {
		System.out.println(axe.chop());
	}

	public void init(){
		System.out.println("init...");
	}
}
xml配置　　
	<bean id="china" class="com.yuchuan.China"
		init-method="init">
		<property name="axe" ref="stoneAxe"></property>
	</bean>
	<bean id="stoneAxe" class="com.yuchuan.StoneAxe"></bean>

输出：
china构造方法
axe setter...
====setBeanName====
====setApplicationContext===
afterPropertiesSet
init...
axe...
```

Spring 也提供两种方式定制Bean实例销毁之前的特定行为
1. 使用destroy-method方法
2. 实现DisposableBean接口，覆写destroy()方法

协调作用于不同步的Bean
当singleton作用域的Bean依赖于prototype作用域的Bean时，会产生不同步的现象
public abstract class Chinese {
	private Dog dog;
	public abstract Dog getDog();
	public void hunt(){
		System.out.println("我带着" + getDog());
		System.out.println(getDog().run());
	}
}
定义了一个抽象的getDog()方法，通常情况下，程序不能调用这个抽象方法，程序也不能使用抽象类创建实例。
接下来需要在配置文件中为<bean.../>元素添加<lookup-method.../> 子元素，<lookup-method.../> 子元素告诉Spring需要实现哪些抽象方法。Spring 为抽象方法提供实现体之后，这个方法就会变成具体方法，这个类就变成了具体类，接下来Spring就可以创建该Bean的实例了。
<lookup-method.../> 中的name属性 指定需要让Spring实现的方法。 bean属性 指定Spring实现该方法的返回值
	<bean id="chinese" class="com.yuchuan.Chinese">
		<lookup-method name="getDog" bean="gunDog"/>
	</bean>
	<bean id="gunDog" class="com.yuchuan.Dog" scope="prototype">
		<property name="name" value="旺财"></property>
	</bean>
	指定Spring应该负责实现getDog()方法，该方法的返回值是容器中的gundog实例
Test
Chinese p = ctx.getBean("chinese",Chinese.class);
p.hunt();
Chinese p2 = ctx.getBean("chinese",Chinese.class);
p2.hunt();

输出：
我带着com.yuchuan.Dog@47c3666a
旺财 run
我带着com.yuchuan.Dog@6d78ddf4
旺财 run

基于XML Schema 的简化配置方式
使用P命名空间，C 命名空间简化配置
<bean id="per" class="com.yuchuan.Per" p:age="29" p:axe-ref="stoneAxe"></bean>
<bean id="per2" class="com.yuchuan.Per" c:axe-ref="stoneAxe" c:age="29"></bean>
<bean id="stoneAxe" class="com.yuchuan.StoneAxe"></bean>
以上是同时配置使用构造方法和setter方法，注意要加入无参构造方法，才能使得setter方法设置注入和构造器注入共存
Spring 还支持一种通过索引来配置构造器参数的方式，上面Bean也可以写为如下形式：
<bean id="per" class="com.yuchuan.Per"
	c:_0-ref="stoneAxe" c:_1="29" />



Bean的生命周期
方案一：Bean开始和销毁前执行的方法
<bean id="beanLifeCycle" class="com.life.BeanLifeCycle" init-method="start" destroy-method="stop">
public class BeanLifeCycle {
	public void start(){
		System.out.println("bean start");
	}

	public void stop(){
		System.out.println("bean stop");
	}
}

方案二：实现两个接口
spring.xml
<bean id="beanLifeCycle" class="com.life.BeanLifeCycle" >

public class BeanLifeCycle implements InitializingBean,DisposableBean{

	@Override
	public void destroy() throws Exception {
		System.out.println("bean destroy2");
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("bean start2");

	}
}

DI是一种IOC的实现方式
容器负责创建对象，组装对象之间的关系
获得对象的过程被反转了

spring中提供了一些以Aware结尾的接口，实现了Aware接口的bean在被初始化之后，可以获取相应资源
为对spring进行简单的扩展提供了方便入口
ApplicationContextAware : 向实现了该接口的bean提供上下文信息
ApplicationEventPublisherAware :
BeanClassLoaderAware
BeanFactoryAware
BootstrapContextAware
LoadTimeWeaverAware
BeanNameAware ：
MessaageSourceAware
NotificationPublisherAware
PortletConfigAware
PortletContextAware
ResourceLoaderAware
ServletConfigAware
ServletContextAware

public class MoocApplicationContext implements ApplicationContextAware{

	private ApplicationContext applicationContext;
	@Override
	public void setApplicationContext(ApplicationContext applicationContext)
			throws BeansException {
		this.applicationContext = applicationContext;
		System.out.println(applicationContext.getBean("moocApplicationContext").hashCode());
	}

}

public class MoocBeanName implements BeanNameAware,ApplicationContextAware{

	private String beanName;

	@Override
	public void setBeanName(String name) {
		this.beanName = name;
		System.out.println(name);

	}

	@Override
	public void setApplicationContext(ApplicationContext applicationContext)
			throws BeansException {
		System.out.println(applicationContext.getBean(beanName));
	}

}

@Test
	public void test() {
		System.out.println(super.getBean("moocBeanName"));
	}
输出 ==>
moocBeanName
// 得到上下文applicationContext 引用，所以两个对象一样
com.aware.MoocBeanName@64bb8a39
com.aware.MoocBeanName@64bb8a39

Resource 用法
public class MoocResource implements ApplicationContextAware{

	private ApplicationContext context;
	@Override
	public void setApplicationContext(ApplicationContext context)
			throws BeansException {
		this.context = context;
	}

	public void resource() throws IOException{
		Resource resource = context.getResource("file:C:\\Users\\chuan\\workspace\\packTest\\src\\spring.xml");
		System.out.println(resource.getFilename());
		System.out.println(resource.contentLength());

	}
}
测试类
public void test() {
		MoocResource resource = super.getBean("moocResource");
		try {
			resource.resource();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

** Spring 可以自动检测类并注册Bean到ApplicationContext中 **

为了能够检测这些类并注册相应的Bean
<context:component-scan base-package="org.exam">
component-scan包含了 annotation-config

类被自动发现并注册bean的条件是：使用@Component,@Repository,@Service，@Controller 注解或者使用@Component的自定义注解
可以通过过滤器修改上面的行为
<beans>
	<context:component-scan base-package="org.exam">
		<context:include-filter type="regex"
			expression=".*Stub.*Repository"/>
		<context:exclude-filter type="annotation"
			expression="org.stereotype.Repository"/>
	</context:component-scan>
</beans>
自动查找的组件，scope是单例的

@scope("prototype")
@Repository
public class MovieFinderImpl implements MovieFinder(){

}
自定义scope，使用ScopeMetadataResolver接口并提供一个无参构造器

@Component   // 可以自定义 @Componet("bean") 在获取的时候使用该名称
public class BeanAnnotation {
	public void say(String arg){
		System.out.println("BeanAnnotation : "+arg);
	}
}

<context:component-scan base-package="com.res"></context:component-scan>

BeanAnnotation bean =   super.getBean("beanAnnotation");  // 未指定名称，默认首字母小写

@Required 注解适用于bean属性的setter方法
这个注解仅仅表示，受影响的bean属性必须在配置时被填充，通过在bean定义或通过自动装配一个明确的属性值
@Autowired 范围更广，可以用在构造器或setter方法上
默认情况下，如果因找不到合适的bean将会导致autowired失败抛出异常，  @autowired(required=false)
每个类只能有一个构造器被标记为 required=true
@Autowired 的必要属性，建议使用@Required 注解

case: 使用注解 @Autowired
spring.xml
<context:component-scan base-package="com.res"></context:component-scan>

@Service
public class InjectServiceImpl implements InjectService{
	@Autowired
	private InjectDao injectDaoImpl;

	@Override
	public void save() {
		System.out.println("service save");
		injectDaoImpl.save();
	}

}

@Repository
public class InjectDaoImpl implements InjectDao{

	@Override
	public void save() {
		System.out.println("injectDaoImpl save...");
	}

}

@Test
	public void test() {
		InjectService bean =   super.getBean("injectServiceImpl");
		bean.save();
	}

换成设值注入
// @Autowired
	private InjectDao injectDaoImpl;
	@Autowired
	public void setInjectDaoImpl(InjectDao injectDaoImpl) {
		this.injectDaoImpl = injectDaoImpl;
	}

换成构造器注入
// @Autowired
	private InjectDao injectDaoImpl;
	// @Autowired
	public void setInjectDaoImpl(InjectDao injectDaoImpl) {
		this.injectDaoImpl = injectDaoImpl;
	}
	@Autowired
	public InjectServiceImpl(InjectDao injectDaoImpl){
		this.injectDaoImpl = injectDaoImpl;
	}

可以使用@Autowired 注解那些中所周知的解析依赖性接口，比如BeanFactory,ApplicationContext,Environment,ResourceLoader,ApplicationEventPublisher ,MessageSource
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-12/41292963.jpg)
@Autowired 是由 Spring BeanPostProcessor 处理的，所以不能在自己的BeanPostProcessor或BeanFactoryPostProcessor类型应用这些注解，这些类型必须通过XML或Spring的@Bean注解

@Order(value=1)  // 指定放入顺序
@Component
public class BeanImplOne implements BeanInterface {

}

@Order(value=2)
@Component
public class BeanImplTwo implements BeanInterface{

}

@Component
public class BeanInvoker {
	@Autowired
	private List<BeanInterface> list;
	@Autowired
	private Map<String,BeanInterface> map;

	public void say(){
		if(null != list){
			for (BeanInterface beanInterface : list) {
				System.out.println(beanInterface.getClass().getName());
			}
		}else{
			System.out.println("null");
		}

		if(null != map){
			for(Map.Entry<String,BeanInterface> entry:map.entrySet()){
				System.out.println(entry.getValue().getClass().getName());
			}
		}
	}
}

测试
@Test
	public void test() {
		BeanInvoker bir = super.getBean("beanInvoker");
		bir.say();
	}


基于java容器注解
spring.xml
<context:component-scan base-package="com.res"></context:component-scan>

public class StringStore implements Store{
	public void init(){
		System.out.println("this is init");

	}

	public void stop(){
		System.out.println("this is stop");
	}
}


@Configuration
public class StoreConfig {

	@Bean(name = "store",initMethod="init",destroyMethod="stop")
	public Store getStringStore(){
		return new StringStore();
	}
}

public class TestStore extends UnitTestBase{
	public TestStore(){
		super("spring.xml");
	}

	@Test
	public void test() {
		Store st = super.getBean("store");
		System.out.println(st.getClass().getName());
	}
}

使用@ImportResource 和 @Value 注解进行资源文件读取
@Configuration
@ImportResource("classpath:/com/acme/properties-config.xml")
public class AppConfig{
	@Value("${jdbc.url}")
	private String url;
	@Value("${jdbc.username}")
	private String username;
	@Value("${jdbc.password")
	private String password;
	@Bean
	public DataSource dataSource(){
		return new DriverManagerDataSource(url,username,password);
	}
}
--------------------------------------
case :
spring.xml
<context:component-scan base-package="com.imooc"></context:component-scan>

public class MyDriverManager {
	public MyDriverManager(String url,String username,String password){
		System.out.println("url "+url);
		System.out.println("username "+username);
		System.out.println("password "+password);
	}
}

-- config.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context-2.5.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop-2.5.xsd
           ">
	<context:property-placeholder location="classpath:/config.properties" />
</beans>

config.properties
password = root
username = root
url = 127.0.0.1

StoreConfig
@Configuration
@ImportResource("classpath:config.xml")
public class StoreConfig {
	@Value("${url}")
	private String url;

	@Value("${username}")
	private String username;

	@Value("${password}")
	private String password;

	@Bean(name="mydriver")
	public MyDriverManager myDriverManager(){
		return new MyDriverManager(url, username, password);
	}
}

public class TestStore extends UnitTestBase{
	public TestStore(){
		super("spring.xml");
	}

	@Test
	public void test() {
		MyDriverManager mydriver = super.getBean("mydriver");
		System.out.println(mydriver.getClass().getName());
	}
}

---------------------------------------------------------
Bean 的作用于包括 singleton,prototype,request,session,global session
配置@Scope

	@Bean(name="store")
	@Scope(value="prototype")
	public Store getStringStore(){
		return new StringStore();
	}
-----------------
基于泛型的自动装配
public interface Store<T> {

}

public class IntegerStore implements Store<Integer>{
	// 此类实现泛型接口
}

public class StringStore implements Store<String>{
	// 此类实现泛型接口
}

@Configuration
public class StoreConfig {
	// 自动装配，s1对应  return new StringStore()
	// 自动装配，S2对应  return new IntegerStore()
	@Autowired
	private Store<String> s1;
	@Autowired
	private Store<Integer> s2;

	@Bean
	public StringStore getStringStore(){
		return new StringStore();
	}

	@Bean
	public IntegerStore getIntegerStore(){
		return new IntegerStore();
	}
	// 此处返回不能为StirngStore或者IntegerStore,因为会造成冲突，
	// 使得自动装配不能正确识别s1和s2
	@Bean(name="stringStoreTest")
	public Store stringStoreTest(){
		System.out.println("s1 "+s1.getClass().getName());
		System.out.println("s2 "+s2.getClass().getName());
		return new StringStore();
	}
}

AOP : Aspect Oriented Programming 面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术
主要功能是：日志记录、性能统计、安全控制、事务处理、异常处理
![AOP相关概念](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/33879222.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/15887480.jpg)

提供了声明式的企业服务，特别是EJB的替代服务的声明
允许用户定制自己的方面，以完成OOP与AOP的互补使用
Spring AOP 侧重于提供一种AOP实现和Spring IOC 容器之间的整合，用于帮助企业应用中的常见问题
Spring AOP 默认使用标准的JavaSE动态代理作为AOP代理，这使得任何接口都可以被代理
Spring AOP 中可以使用CGLIB代理

Spring 所有的切面和通知器都必须放在一个<aop:config>内（可以配置包含多个<aop:config>元素）每一个<aop:config>可以包含pointcut,advisor和aspect元素（必须按照这个顺序进行声明）
<aop:config> 风格大量使用了Spring的自动代理机制
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/51529359.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/27499441.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/18386608.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-13/73688262.jpg)

[Maven]
```
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
    	<groupId>javax.servlet</groupId>
    	<artifactId>servlet-api</artifactId>
    	<version>3.0-alpha-1</version>
    </dependency>
  </dependencies>
```

Bean 后处理器会在Bean实例创建成功之后，对Bean实例进一步的增强处理
Spring 容器负责把各Bean创建出来，Bean 后处理器可以依次对每个Bean进行某种修改、增强，从而可以对容器中的Bean集中增加某种功能
@Component @Controller @Service @Repository

指定Bean的作用域
@Scope("prototype")
@Component
public class SteelAxe{ }

使用@Resource 配置依赖，与<property.../> 元素的ref属性有相同的效果
@Component
public class Chinese{
	private Axe axe;
	@Resource(name="stoneAxe")
	public void setAxe(Axe axe){
		this.axe = axe;
	}
}
@Resource 不仅可以修饰setter方法，还可以修饰变量
@Resource(name="stoneAxe")
private Axe axe

Spring 3.0 新增 @DependsOn({"steelAxe","abc"})  初始化之前，会强制初始化steelAxe,abc两个Bean
@Lazy(true) 指定当Spring容器初始化时，不会预初始化之

当使用@Autowired 标注setter方法时，默认采用byType自动装配策略
Spring4.0 @Autowired 可以根据泛型进行自动装配

spring缓存机制
在Spring配置文件中添加<cache:annotation-driven cache-manager="缓存管理器ID"> 该元素指定Spring根据注解来启用Bean级别或方法级别的缓存
如果将容器中缓存管理器的ID设置为cacheManager,则可省略<cache:annotation-driven> 的cache-manager属性
一般来说，应用有多少个组件需要缓存，程序就应该配置多少个缓存区
EhCache缓存实现配置：ehcache-core-2.4.3.jar 和 slf4j-api-1.6.1.jar

使用@Cacheeable执行缓存
1. 类级别的缓存
@service("userService")
@Cacheable(value="users")
程序调用该类的任意方法时，只要传入的参数相同，Spring就会使用缓存
同一个类不管调用哪个方法，只要调用方法时传入的参数相同，Spring都会直接利用缓存区中的数据
2. 方法级别的缓存
使用@Cacheable 修饰方法时，就可控制Spring在方法级别进行缓存，这样当程序调用该方法时，只要传入的参数相同，Spring就会使用缓存

使用@CacheEvict清除缓存
@CacheEvict(value="users")  清楚users缓存区中的数据
@CacheEvict(value="users",allEntries=true) 清空整个users缓存区





