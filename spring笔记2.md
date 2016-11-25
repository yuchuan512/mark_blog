title: spring笔记2
date: 2016-06-09 22:30:56
categories: [java,spring]
tags:
---
Spring_note.java
![第一阶段](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-17/8333499.jpg)
<br>
![第二阶段](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-17/71841689.jpg)
<br>
![第三阶段](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-17/89899451.jpg)

ApplicationContext 主要实现类：
1. ClassPathXmlApplicationContext
2. FileSystemApplicationContext
属性可以指定类型
<bean id="car" class="com.Po.Car">
		<constructor-arg value="Audi" type="java.lang.String"></constructor-arg>
		<constructor-arg value="Shagnhai" type="java.lang.String"></constructor-arg>
		<constructor-arg value="240" type="int"></constructor-arg>
</bean>
若属性过多，可以指定type类型或index属性来声明
属性字符值有特殊字符 ，使用CDATA设置  <value><![CDATA[^shanghai>]]></value>

通过p的命名空间为bean的属性赋值，需要先导入P命名空间，相对于传统的配置方式更加简洁

<bean id="person" class="com.Po.Person"  p:name="Queen" p:car-ref="car" p:address-ref="address">
自动装配 （根据名字自动装配）
<bean id="person" class="com.Po.Person"  p:name="Queen" autowire="byName">
byName根据bean的名字和当前bean的setter风格的属性名进行自动装配

bean配置之间的继承
<bean id="address" class="com.Po.Address" p:city="beijing" p:street="Wudaokou"></bean>
<bean id="address2"  p:street="Dazhongsi" parent="address"></bean>
子bean从父bean继承配置，可以覆盖配置
可以把需要被继承的bean  加上属性  abstract="true" ,就只能被其他bean继承，不能被实例化

bean之间的依赖关系
depends-on="car" IOC容器初始化的时候没有这个实例则报错，若有多个，通过逗号分隔

PropertyPlaceholderConfigurer

db.properties
user = root
password = root
driverClass = com.mysql.jdbc.Driver
jdbcUrl = jdbc:mysql://127.0.0.1:3306/test

spring.xml
<context:property-placeholder location="classpath:db.properties"/>

	<bean id="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${user}"></property>
		<property name="password" value="${password}"></property>
		<property name="driverClass" value="${driverClass}"></property>
		<property name="jdbcUrl" value="${jdbcUrl}"></property>
	</bean>


Spring
spring 调用无参构造器，使用set方法
通过反射的方式在IOC容器中创建Bean，所有类必须含有无参构造器
使用构造器注入属性值可以指定参数的位置和参数的类型，以区分重载的构造器
<bean id="car2" class="com.atguigu.spring.beans.Car">
		<constructor-arg value="Baoma" type="java.lang.String" ></constructor-arg>
		<constructor-arg value="Shanghai" type="java.lang.String" ></constructor-arg>
		<constructor-arg value="240000" type="int" ></constructor-arg>
</bean>

字面值： 可用字符串表示的值，可以通过value属性或者value子节点
若要包含特殊字符，使用 <value><![CDATA[<ShangHai^]]></value>

自动装配Bean，在<bean>的autowire属性里指定自动装配的模式
byType  byName   根据类型和名称自动装配
赋值为null    <constructor-arg><null/></constructor-arg>
<bean id="person3" class="com.atguigu.spring.beans.collection.Person">
	 	<property name="name" value="Mike"></property>
	 	<property name="age" value="27"></property>
	 	<property name="car">
	 		<list>
	 			<ref bean="car"/>
	 		</list>
	 	</property>
</bean>
使用map
<map>
	<entry key="AA" value-ref="car"></entry>
	<entry></entry>
</map>
通过p命名空间为bean的属性赋值，需要先导入P命名空间
		加入 xmlns:p="http://www.springframework.org/schema/p"
<bean id="person5" class="com.atguigu.spring.beans.collection.Person"
	p:age="30" p:name="Queen" p:car-ref="car"></bean>

autowire="byName" 根据名字自动装配
autowire="byType" 根据bean的类型和当前bean的属性的类型进行自动装配，若IOC容器有1个以上的相同类型，无法装配
autowire 缺点 1.只希望装配个别属性时，不够灵活  2） 要么根据名称装配，要么根据类型装配，不够灵活

parent指定继承哪个配置   eg: parent = "address" ,子bean从父bean继承属性值，也可以覆盖继承的属性值
抽象bean  ，abstract="brue" 其他bean继承它
eg <bean id="address" p:city="Beijing" abstract="true"></bean>
子Bean定义可以从父Bean继承实现类、构造器参数、属性值等配置信息。可以增加新的配置信息，覆盖父类配置信息
子Bean无法从父Bean继承如下属性：depends-on/autowire/singleton/scope/lazy-init，这些属性将总是从子Bean定义中获得，或采用默认值

若某一个bean的class属性没有被指定，则必须是一个抽象bean
并不是bean里面所有的属性都会被继承，比如 abstract autowire不会被继承
前置依赖的bean会在本bean之前创建好  depends-on="car"
scope="singleton"  创建容器的时候创建bean
scope="Prototype"  默认不创建，每次请求时都创建一个新的bean
scope="session"  or  "request"  对应web

PropertyPlaceholderConfigure 的BeanFactory 后置处理器允许用户将Bean配置的部分内容外移到配置文件中
<context:property-placeholder location="classpath:db.properties"/>
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${user}"></property>
		<property name="password" value="${password}"></property>
		<property name="driverClass" value="${driverClass}"></property>
		<property name="jdbcUrl" value="${jdbcUrl}"></property>
</bean>

db.properties
user=root
password=root
driverClass=com.mysql.jdbc.Driver
jdbcUrl=jdbc:mysql://localhost:3306/test
测试：
DataSource dataSource = (DataSource) ctx.getBean("datasource");
		System.out.println(dataSource.getConnection());

spring使用#{...}作为定界符，所有在大框号的字符都将被认为是SpEL
SpEL 为bean的属性进行动态赋值提供了便利
字面量的表示：
整数  <property name="count" value="#{5}"/>
小数  <property name="frequency" value="#{89.7}"/>
boolean <property name="enabled" value="#{false}" />
String可以使用单引号或者双引号作为字符串的定界符号
<property name="name" value="#{'Chunk'}"/> 或 <property name='name' value='#{"Chunk"}' />

算数运算符
<property name="one" value="#{counter.total+42}" />
<property name="one" value="#{2 * T(java.lang.Math).PI * circle.radius}" />
加号还可以作字符串连接
<constructor-arg value="#{performer.firstName + ' ' + performer.lastname" />
比较运算符
<property name="one" value="#{counter.total==100}" />
<property name="two" value = "#{counter.total le 1000}" />
逻辑运算符
<property name="large" value="#{shape.king=='circle' and shape.perimeter gt 10000}" />
<property name="outOfStock" value="#{!product.available}" />
<property name="outofStock" value="#{not product.available}"  />
if-else 运算
<constructor-arg value="{songSelector.selectSong()=='Jingle one' ? 'Jing two':'Jing bells" />
正则表达式
<constructor-arg  value="#{admin.email matches '[a-zA-Z0-9]'} "/>


调用静态方法或静态属性：通过T()调用一个类的静态方法，它将返回一个Class Object ，然后再调用相应的方法或属性
<property name="initValue" value="#{T(java.lang.Math).PI}"></property>
SpEL表达式case
	<bean id="address" class="com.Po.Address">
		<property name="city" value="#{'beijing'}"></property>
		<property name="street" value="WuDaoKou"></property>
	</bean>

	<bean id="car" class="com.Po.Car">
		<property name="brade" value="Audi"></property>
		<property name="price" value="50000"></property>
		<property name="tyrePerimter" value="#{T(java.lang.Math).PI*2}"></property>
	</bean>

	<bean id="person" class="com.Po.Person">
		<property name="car" value="#{car}"></property>
		<property name="address" value="#{address}"></property>
		<property name="name" value="#{car.brade}"></property>
	</bean>

声明注入实例前后要执行的方法
<bean id="car" class="com.Po.Car" init-method="initExe" destroy-method="stopExe">

		<property name="brade" value="Audi"></property>
		<property name="price" value="#{2000}"></property>
		<property name="tyrePerimter" value="#{2*T(java.lang.Math).PI}"></property>
</bean>
或者 后置处理器
<beans>
<bean id="car" class="com.Po.Car">

		<property name="brade" value="Audi"></property>
		<property name="price" value="#{2000}"></property>
		<property name="tyrePerimter" value="#{2*T(java.lang.Math).PI}"></property>
	</bean>

<bean class="com.Po.MyBeanPostProcessor">
</bean>

MyBeanPostProcessor 该类实现BeanPostProcessor接口
public class MyBeanPostProcessor implements BeanPostProcessor{

	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName)
			throws BeansException {
		System.out.println("postProcessAfterInitialization " + bean+", "+beanName );
		return bean;
	}

	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName)
			throws BeansException {
		System.out.println("postProcessBeforeInitialization " + bean+", "+beanName );
		return bean;
	}
}
可以在以上两个方法中修改返回的bean，甚至返回一个新的bean

静态工厂方法
public class StaticCarFactory {
	private static Map<String,Car> cars = new HashMap<String, Car>();
	static{
		cars.put("audi", new Car("audi",30000));
		cars.put("ford", new Car("ford",40000));
	}

	public static Car getCar(String name){
		return cars.get(name);
	}
}
通过静态工厂方法来配置bean，注意不是配置静态工厂方法实例，而是配置bean实例
class属性：指向静态工厂方法的全类名
factory-method:指向静态工厂方法的名字
constructor-arg: 如果工厂方法需要传入参数u，使用constructor-arg 传参
<bean id="car1" class="com.Po.StaticCarFactory" factory-method="getCar">
		<constructor-arg value="audi"></constructor-arg>
</bean>

实例工厂方法
先需要创建工厂本身，在调用工厂的方法
public class InstanceCarFactory {
	private Map<String, Car> cars = null;

	public InstanceCarFactory() {
		cars = new HashMap<String, Car>();
		cars.put("audi", new Car("audi", 30000));
		cars.put("ford", new Car("ford", 30000));
	}

	public Car getCar(String brand) {
		return cars.get(brand);
	}
}

<bean id="carFactory" class="com.Po.InstanceCarFactory"></bean>

<bean id="car2" factory-bean="carFactory" factory-method="getCar">
	<constructor-arg value="ford"></constructor-arg>
</bean>

通过FactoryBean返回bean的实例，class:指向FactoryBean的全类名，property：配置FactoryBean的属性
但实际返回的实例FactoryBean的getObejct()方法返回的实例。
<bean id="car2" class="com.Po.CarFactoryBean">
		<property name="brand" value="AUdi"></property>
</bean>


public class CarFactoryBean implements FactoryBean<Car>{

	private String brand;
	public void setBrand(String brand){
		this.brand = brand;
	}

	@Override
	public Car getObject() throws Exception {
		return new Car("BMW",500000);
	}

	@Override
	public Class<?> getObjectType() {
		return Car.class;
	}

	@Override
	public boolean isSingleton() {
		return true;
	}

}
配置说明
```
静态工厂配置
public class BeingFactory {
	public static Being getBeing(String arg){
		if(arg.equalsIgnoreCase("dog")){
			return new Dog();
		}else
			return new Cat();
	}
}
<bean id="dog" class="com.yuchuan.BeingFactory"
	factory-method="getBeing">
	<!-- constructor-arg 元素用于为静态工厂方法指定参数-->
	<constructor-arg value="dog"></constructor-arg>
	<!-- property 元素驱动spring执行setter方法 -->
	<property name="msg" value="i am dog"></property>
</bean>
<bean id="cat" class="com.yuchuan.BeingFactory"
	factory-method="getBeing">
	<constructor-arg value="cat"></constructor-arg>
	<property name="msg" value="i am cat"></property>
</bean>
实例工厂配置
public class PersonFactory {
	public IPerson getPerson(String ethnic){
		if(ethnic.equalsIgnoreCase("chin")){
			return new Japanese();
		}else
			return new American();
	}
}

<bean id="PersonFactory" class="com.yuchuan.PersonFactory"></bean>
<bean id="american" factory-bean="PersonFactory"
	factory-method="getPerson">
	<constructor-arg value="chin"></constructor-arg>
</bean>
<bean id="japanese" factory-bean="PersonFactory"
	factory-method="getPerson">
	<constructor-arg value="chin2"></constructor-arg>
</bean>
```


基于注解的方式
<context:component-scan>  扫描多个包可以使用逗号分隔
@Service
@Controller
@Respository

<!-- 可以通过resource-patter指定扫描的资源 -->
	<context:component-scan base-package="com.atguigu.spring.beans.annotation"
		resource-pattern="respository/.class"></context:component-scan>

context:exclude-filter  根据表达式排除指定包的类
<context:component-scan base-package="com.atguigu.spring.beans.annotation">
		<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
	</context:component-scan>

context:include-filter 根据表达式只扫描指定包的类，注意要和 use-default-filters="false" 一起使用
<context:component-scan base-package="com.atguigu.spring.beans.annotation" use-default-filters="false">
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
	</context:component-scan>

不包含某个接口和所有实现该接口的类
<context:component-scan base-package="com.atguigu.spring.beans.annotation" >
		<context:exclude-filter type="assignable"
			expression="com.atguigu.spring.beans.annotation.respository.UserRespository"/>
	</context:component-scan>

只包含实现了某个接口
<context:component-scan base-package="com.atguigu.spring.beans.annotation" use-default-filters="false">
		<context:include-filter type="assignable" expression="com.atguigu.spring.beans.annotation.respository.UserRespository"/>
	</context:component-scan>

<context:component-scan> 元素还会自动注册AutowiredAnnotationBeanPostProcessor实例，该实例可以自动装配具有@Autowired 和 @Resource
@Inject 注解的属性

@Autowired 注解自动装配具有兼容类型的单个Bean属性
构造器，普通字段(及时非public)，一切具有参数的方法都可以应用 @Autowired 注解
默认情况下，所有使用@Autowired 注解的属性都需要被设置，当Spring找不到匹配的Bean装配属性时，会抛出异常，若某一属性允许不被设置，可以设置@Autowired 注解的required属性为false   eg: @Autowired(required=false),找不到这个实例不会报错，而是为该实例为null

@Autowired 也可以应用在数据类型上，此时Spring会把所有匹配的Bean进行自动装配
@Autowired 也可以应用在集合上，此时Spring读取该集合的类型信息，自动装配所有与之兼容的Bean
@Autowired 用在Map 上，若该Map的键值为String，那么Spring将自动装配与之Map值类型兼容的Bean，此时Bean的名称作为键，bean本身作为值

若指定包含某个实现了接口的类
可以
@Autowired
@Qualifier("userRepository") 指定名字

泛型依赖注入
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-18/14319610.jpg)
public class BaseRepository<T> {

}
public class BaseService<T>{
	@Autowired
	protected BaseRepository<T> repository;

	public void add(){
		System.out.println("add...");
		System.out.println(repository);
	}
}

@Repository
public class UserRepository extends BaseRepository<User>{

}

@Service
public class UserService extends BaseService<User>{

}

public class User{

}

父类之间建立了联系，子类继承了这种联系，还使用了User具体类型代替泛型 T

动态代理实现AOP
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-19/31932448.jpg)

public class ArithmeticCalculatorImpl implements ArithmeticCalculator{

	@Override
	public int add(int i, int j) {
		int result = i+j;
		return result;
	}

	@Override
	public int sub(int i, int j) {
		int result = i-j;
		return result;
	}

}

public class ArithmeticCalculatorLoggingProxy {
	// 要代理的对象
	private ArithmeticCalculator target;

	public ArithmeticCalculatorLoggingProxy(ArithmeticCalculator target) {
		this.target = target;
	}

	public ArithmeticCalculator getLoggingProxy(){
		ArithmeticCalculator proxy = null;
		// 代理对象由哪一个类加载器负责加载
		ClassLoader loader = target.getClass().getClassLoader();
		// 代理对象的类型，其中有哪些方法
		Class[] interfaces = new Class[]{ArithmeticCalculator.class};
		// 当调用代理对象其中的方法时，该执行的代码
		InvocationHandler h = new InvocationHandler() {
			/**
			 * @proxy： 正在返回的那个代理对象，一般情况下，在invoke方法都不使用该对象
			 * method：正在被调用的方法
			 * args : 调用方法时传入的参数
			 */
			@Override
			public Object invoke(Object proxy, Method method, Object[] args)
					throws Throwable {
				String methodName = method.getName();
				System.out.println("The methodName"+methodName+" begins with" + Arrays.asList(args));
				Object result=method.invoke(target, args);
				System.out.println("method " + methodName +" ends with "+result);
				return result;
			}
		};
		proxy = (ArithmeticCalculator) Proxy.newProxyInstance(loader, interfaces, h);

		return proxy;
	}
}

public class MainTest {
	public static void main(String[] args) {

		ArithmeticCalculator target = new ArithmeticCalculatorImpl();
		ArithmeticCalculator proxy = new ArithmeticCalculatorLoggingProxy(target).getLoggingProxy();


		int result = proxy.sub(2, 1);
		System.out.println("-->"+result);

		result = proxy.add(1, 2);
		System.out.println("-->" + result);

	}
}

输出
The methodNamesub begins with[2, 1]
method sub ends with 1
-->1
The methodNameadd begins with[1, 2]
method add ends with 3
-->3

AOP 的主要编程对象是切面，而切面模块化横切关注点
在应用AOP编程时，仍然需要定义公共功能，但可以明确的定义这个功能在哪里，以什么方式应用，并且不必修改受影响的类，这样一来横切关注点就被模块化到特殊对象里
AOP好处：
每个事物逻辑位于一个位置，代码不分散，便于维护和升级
业务模块更简洁，只包含核心业务代码

切面：横切关注点模块化的对象
连接点：程序执行的某个特定位置
切点： 每个类都拥有多个连接点，连接点是程序类客观存在的事务。AOP 通过切点定位到特定的连接点，类比：连接点相当于数据库中的记录，切点相当于查询条件。切点和连接点不是一对一，一个切点匹配多个连接点，切点通过org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件

AspectJ: java社区里最完整最流行的AOP框架
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-5-19/90018731.jpg)

@Component(value="arithm")
public class ArithmeticCalculatorImpl implements ArithmeticCalculator{

	@Override
	public int add(int i, int j) {
		return i+j;
	}

	@Override
	public int sub(int i, int j) {
		return i-j;
	}

}

// 把这个类声明为一个切面，放入IOC容器中，再声明为一个切面
@Aspect
@Component
public class LoggingAspect {
	// 声明该方法是一个前置通知 ：在目标方法开始之前执行
	@Before("execution(public int com.atguigu.aspect.ArithmeticCalculatorImpl.add(int, int))")
	public void beforeMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		List<Object> args = Arrays.asList(joinPoint.getArgs());
		System.out.println("the method " + methodName + " begins with args "+args);
	}
}

<context:component-scan base-package="com.atguigu.aspect"></context:component-scan>
	<!-- 使用AspectJ注解 自动为匹配的类生成代理对象 -->
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>

1. 切面首先是一个IOC的bean，加入@Component 注解
2. 切面 @Aspect 注解
3. <aop:aspectj-autoproxy></aop:aspectj-autoproxy>

在类中声明通知
1. 声明一个方法
2. 在方法前加入@Before 注解

可以在通知里声明一个类型为JoinPoint的参数，就能访问链接细节，如方法名称和参数值

后置通知使在连接点完成之后执行的，即连接点返回结果或者抛出异常的时候
在后置通知中还不能访问目标方法执行的结果（可以在返回通知里面访问结果）
@After("execution(* com.atguigu.aspect.*.*(int,int))")
	public void afterMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("the method "+methodName+" ends ");
	}

// 把这个类声明为一个切面，放入IOC容器中，再声明为一个切面
@Aspect
@Component
public class LoggingAspect {
	// 声明该方法是一个前置通知 ：在目标方法开始之前执行
	//前置通知
	@Before("execution(* com.atguigu.aspect.*.*(int, int))")
	public void beforeMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		List<Object> args = Arrays.asList(joinPoint.getArgs());
		System.out.println("the method " + methodName + " begins with args "+args);
	}
	// 后置通知
	@After("execution(* com.atguigu.aspect.*.*(int,int))")
	public void afterMethod(JoinPoint joinPoint){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("the method "+methodName+" ends ");
	}
	/**
	 * 方法正常结束执行的代码
	 * 返回通知使可以访问到方法的返回值的
	 * @param joinPoint
	 * @param result
	 */
	@AfterReturning(value="execution(* com.atguigu.aspect.*.*(int,int))",
				returning="result")
	public void afterReturning(JoinPoint joinPoint, Object result){
		String methodName = joinPoint.getSignature().getName();
		System.out.println("the methodName "+methodName+" ends with "+result);
	}
	/**
	 * 在目标方法出现异常时执行
	 * @param joinPoint
	 * @param ex
	 */
	@AfterThrowing(value="execution(* com.atguigu.aspect.*.*(int,int))",
				throwing="ex")
	public void afterThrowing(JoinPoint joinPoint, Exception ex){
		String methodName = joinPoint.getSignature().getName();

	}

}

环绕通知
@Aspect
@Component
public class LoggingAspect {

	/**
	 * 环绕通知需要携带ProceedingJoinPoint 类型的参数
	 * 类似于动态代理的全过程；ProceedingJoinPoint类型的参数可以决定是否执行目标方法
	 * 且环绕通知必须有返回值，返回值即目标方法的返回值
	 * @param pjd
	 * @throws Throwable
	 */
	@Around(value="execution(* com.atguigu.aspect.*.*(int,int))")
	public Object aroundMethod(ProceedingJoinPoint pjd) throws Throwable{
		Object result = null;
		String methodName = pjd.getSignature().getName();
		try {
			// 前置通知
			System.out.println("the method "+methodName + " begins with "+Arrays.asList(pjd.getArgs()));
			// 方法执行
			result = pjd.proceed();
			// 后置通知
			System.out.println("the method "+methodName+" ends with "+result);
		} catch (Exception e) {
			 // 异常通知
		}
		// 后置通知
		System.out.println("ends ");
		return result;
	}
}

指定切面的优先级，值越小优先级越高，添加注解
@Order(2)
@Aspect
@Component



BeanPostProcessor 处理所有bean 接口 ，可以在其中选择过滤哪些bean

静态工厂方法和实例工厂方法  还可以通过  factory-bean 返回实例
<bean id="car1"
	class="com.aiguigu.spring.beans.factory.StaticFactory"
	factory-method="getCar">
	<constructor-arg value="audi"></constructor-arg>
	</bean>

<bean id="carFactory"  class="com.aiguigu.spring.beans.factory.InstanceCarFactory"></bean>
	<bean id="car2" factory-bean="carFactory" factory-method="getCar">
		<constructor-arg value="ford"></constructor-arg>
	</bean>

factory-bean 返回实例
实现 FactoryBean 接口，通过 FactoryBean 来配置Bean的实例，class指向FactoryBean的全类名，property配置FactoryBean
的属性，但实际返回的实例是 FactoryBean的getObject() 方法返回的实例。

@Component  标识一个受Spring管理的组件
@Respository   持久层组件
@Service    服务层组件，业务层
@Controller   表现层组件
<context:component-scan base-package=""  > 扫描当前包及其子包下的类
resource-pattern="repository/*.class"  设置只扫描符合条件的类
<component-scan>可以包含若干 exclude-filter include-filter
<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
注意若要使用 <context:include-filter>  则 <context:component-scan> 配置  <use-default-filters="false">

@Autowired
@Qualifier("userRepositoryImpl") 指定名称

Spring AOP
1. jar包   sopalliance    aspectj.weaver    aop-4.0       aspects-4.0
2. 加入命名空间  aop context  bean
3.基于注解的方式   xml中加入  <aop:aspectj-autoproxy />
把横切关注点的代码抽象到切面的类中，加入@Componet、@Aspect 注解，在方法前加 @Before
@Aspect
@Component
public class LoggingAspect {
	@Before("execution(* com.atguigu.spring.aop.impl.*.*(..))")
	public void beforeMethod(JoinPoint joinPoint) {
		String methodName = joinPoint.getSignature().getName();
		List<Object> args = Arrays.asList(joinPoint.getArgs());
		System.out.println("The method begins " +args);
	}
}
可以在通知方法中声明一个类型为JoinPoint的参数，就能访问链接细节，如方法名称和参数值
@Before 前置通知
后置通知中不论执行成功与否，都会打印出来
返回通知可以访问到执行方法的执行结果
@AfterReturning(value="",returning="result")
public void afterReturning(JoinPoint joinPoint,Object result){
	String methodName = joinPoint.getSignature().getName();
	System.out.println(methodName+" " +result);
}
异常通知，在方法出现异常时，捕获到异常
@AfterThrowing(value="",throwing="ex")
public void afterThrowing(JoinPoint joinPoint,Exception ex){

}
环绕通知需要携带ProceedingJoinPoint类型的参数，类似于动态代理的全过程，环绕通知必须有返回值，返回值即为目标方法
的返回值
@Around("")
public void aroundMethod(ProceedingJoinPoint pjd){
	String methodName = pjd.getSignature().getName();
	System.out.println(pjd.getArgs());
	Object result = pjd.proceed()
}
指定切面的优先级，值越小优先级越高，添加注解 @Order(1)
定义一个方法，用于声明切入点表达式，一般的，该方法不需要添加其他的代码
使用@Pointcut 声明切入点表达式
@Pointcut("execution(* com.atguigu.spring.aop.impl.*.*(..))")
	public void declareJoinPoint() {}
引用之 @Before("declareJoinPoint()")，如果不在一个类，添加类名，类似，不在一个包，添加包名
eg:
@Pointcut("execution(* com.atguigu.aspect.*.*(int, int))")
	public void declareJoinPointExpression(){}

@Before("declareJoinPointExpression()")
	public void beforeMethod(JoinPoint joinPoint) {
		String methodName = joinPoint.getSignature().getName();
		List<Object> args = Arrays.asList(joinPoint.getArgs());
		System.out.println("the method " + methodName + " begins with args "
				+ args);
	}
如果切点跟引用点不在同一个包里面，需要加上包名
eg: @Before("com.atguigu.spring.aop.LoggingAspect.declareJoinPointExpression()")

配置的方式配置AOP
		<bean id="arithmeticCalculator"
			class="com.atguigu.spring.aop.impl.ArithmeticCalculatorImpl"></bean>
		<bean id="loggingAspect"
			class="com.atguigu.spring.aop.impl.LoggingAspect"></bean>
		<bean id="validationAspect"
			class="com.atguigu.spring.aop.impl.ValidationAspect"></bean>

		<aop:config>
			<aop:pointcut expression="execution(* com.atguigu.spring.aop.impl.*.*(..))" id="pointcut"/>
			<aop:aspect ref="loggingAspect">
				<aop:before method="beforeMethod" pointcut-ref="pointcut"/>
				<aop:before method="afterMethod" pointcut-ref="pointcut"/>
			</aop:aspect>
			<aop:aspect ref="validationAspect">
				<aop:before method="validateArgs" pointcut-ref="pointcut"/>
			</aop:aspect>
		</aop:config>

db.properties
<!-- 导入资源文件-->
<context:property-placeholder location="classpath:db.properties"/>
<!-- 配置C3p0数据源-->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="driverClass" value="${jdbc.driverClass}"></property>
		<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
		<property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
		<property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
	</bean>

使用jdbcTemplate
<context:property-placeholder location="classpath:db.properties"/>
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="user" value="${jdbc.user}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="driverClass" value="${jdbc.driverClass}"></property>
		<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
		<property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
		<property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
	</bean>

	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>  // jdbcTemplate需要数据源
	</bean>

	<bean id="namedParameterJdbcTemplate"
		class="org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate">
		<constructor-arg ref= "dataSource"></constructor-arg>
	</bean>

public class JDBCTest {
	ClassPathXmlApplicationContext ctx =null;
	JdbcTemplate jdbcTemplate = null;
	private NamedParameterJdbcTemplate namedParameterJdbcTemplate;

	{
		ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
	    jdbcTemplate = (JdbcTemplate) ctx.getBean("jdbcTemplate");
	    namedParameterJdbcTemplate = (NamedParameterJdbcTemplate) ctx.getBean("namedParameterJdbcTemplate");
	}

	@Test
	public void test() throws SQLException {
		DataSource dataSource = (DataSource) ctx.getBean("dataSource");
		System.out.println(dataSource.getConnection());
	}

	@Test
	public void testUpdate(){
		String sql = "update course set cname=? where tnum=?";
		jdbcTemplate.update(sql,"French","01");
	}

	@Test
	public void testBatchUpdate(){
		String sql = "insert into course values(?,?,?)";
		List<Object[]> batchArgs = new ArrayList<>();
		batchArgs.add(new Object[]{"08","Chinese","02"});
		batchArgs.add(new Object[]{"05","French","05"});
		batchArgs.add(new Object[]{"06","hello","03"});
		batchArgs.add(new Object[]{"090","English","02"});
		jdbcTemplate.batchUpdate(sql, batchArgs);
	}
	/**
	 * 从数据库获取一条记录，实际得到一个对象
	 *  BeanPropertyRowMapper<Employee>(Class<Employee> mappedClass)
	 *  不是调用 queryForObject(String sql, Class<Employee> requiredType, Object... args)
	 *  而是调用  queryForObject(String sql, RowMapper<Employee> rowMapper, Object... args)
	 *  1. 其中RowMapper指定如何去映射结果集的行，常用的实现类为BeanPropertyRowMapper
	 *  2. 使用SQL中列的别名完成列名和类的属性名的映射
	 *  3. 不支持级联属性，不是ORM
	 */
	@Test
	public void testQueryForObject(){
		String sql = "select id,lastname,email from employees where id=?";
		RowMapper<Employee> rowMapper = new BeanPropertyRowMapper<Employee>(Employee.class);
		Employee employee = jdbcTemplate.queryForObject(sql, rowMapper, 1);
		System.out.println(employee);
	}
	/**
	 * 查询结果为集合
	 */
	@Test
	public void testQueryForList(){
		String sql = "select id,lastName,email from employees where id > ?";
		RowMapper<Employee> rowMapper = new BeanPropertyRowMapper<Employee>(Employee.class);
		List<Employee> employees = jdbcTemplate.query(sql, rowMapper,1);
		for (Employee employee : employees) {
			System.out.println(employee);
		}
	}

	/**
	 * 获取单个列的值或统计查询
	 */
	@Test
	public void testQueryForObjectColunmn(){
		String sql = "select count(id) from employees";
		long count = jdbcTemplate.queryForObject(sql, Long.class);
		System.out.println(count);
	}
	/**
	 * 具名参数 ，可以为参数起名字
	 * 1
	 */
	@Test
	public void testNamedParameterJdbcTemplate(){
		String sql = "insert into employees values(:id,:lastname,:email)";
		Map<String,Object> paramMap = new HashMap<>();
		paramMap.put("id",99);
		paramMap.put("lastname","hello");
		paramMap.put("email","yu@163.com");
		namedParameterJdbcTemplate.update(sql, paramMap);
	}

	@Test
	public void testNamedParameterJdbcTemplate2(){
		// values里面的字段名称跟实体类字段一致（大小写）
		String sql = "insert into employees values(:id,:lastName,:email)";
		Employee employee = new Employee();
		employee.setLastName("xyz");
		employee.setEmail("yu@163.com");
		employee.setId(96);
		BeanPropertySqlParameterSource paramSource = new BeanPropertySqlParameterSource(employee);
		namedParameterJdbcTemplate.update(sql, paramSource);
	}

}

spring的声明式事务
1.	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref= "dataSource"></property>
	</bean>

2. 	<!-- 启用事务注解 -->
	<tx:annotation-driven transaction-manager="transactionManager"/>
3. 对应的方法上添加@Transactional注解
	@Transactional
	@Override
	public void purchase(String userName, String isbn) {
		// 1. 获取书的单价
		int price  = bookShopDao.findBookPriceByIsbn(isbn);
		// 2. 更新书的库存
		bookShopDao.updateBookStock(isbn);
		// 3. 更新用户余额
		bookShopDao.updateUserAccount(userName, price);

	}

Spring 支持的事务传播行为
REQUIRED  如果有事务在运行，当前的方法在这个事务内运行，否则就启动一个新的事务，并在自己的事务内运行。买两本书不够，买一本够，两本都买不了
REQUIRED_NEW  当前的方法必须启动新事务，并在它自己的事务内运行，如果有事务正在运行，应该将它挂起。买两本书不够，买一本够，可以买一本
使用 propagetion指定事务的传播行为
eg: @Transactional(propagation=Propagation.REQUIRED) // 事务的默认行为

事务的隔离级别
isolation=Isolation.READ_COMMITTED 读已提交
默认情况下，Spring 的声明式事务对所有的运行时异常进行回滚
使用 readOnly指定事务是否只读，表示这个事务只读取数据不更新数据，有利于数据库引擎优化事务
使用timeout指定强制回滚之前事务可以占用的时间



