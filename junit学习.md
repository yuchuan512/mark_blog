title: junit学习
date: 2016-06-09 22:34:14
categories: [java,junit]
tags:
---
JUnit
### Junit简介

xUnit是一套基于测试驱动开发的测试框架，包括PythonUnit CppUnit Junit
jUnit使用断言的机制
测试不是为了证明你是对的，而是证明你没有错
1.测试方法上必须使用@Test进行修饰
2.测试方法必须使用public void 进行修饰，不带任何参数
3.新建一个源代码目录来存放我们的测试代码
4.测试类的包和被测试类保持一致
5.测试方法使用test作为方法名的前缀

### JUnit

JUnit执行顺序  beforeClass -> before ->test ->after ->before ->test2->after ->afterClass
@BeforeClass 修饰的方法会在所有方法被调用前被执行，而且方法是静态的，在内存中它只会存在一份实例，适合加载配置文件
@AfterClass 修饰的方法通常用来对资源的清理，如关闭数据库的连接
@Before和@After会在每个测试方法的前后各执行一次

@Test可以加参数 eg:
@Test(expected=ArithmeticException.class)  捕获到该异常，显示测试成功
@Test(timeout=2000)    超时停止

@Ignore所修饰的测试方法会被测试运行期忽略

### 测试套件的使用
```
@RunWith(Suite.class)
@Suite.SuiteClasses({TaskTest1.class,TaskTest2.class,TaskTest3.class})
public class SuiteTest {
}
```
### JUnit的参数化设置
```
@RunWith(Parameterized.class)
public class ParameterTest {
	/*
	 * 1.更改默认的测试环境为RunWith(Parameterized.class)
	 * 2.声明变量来存放预期值和结果值
	 * 3.声明一个返回值为Collection的公共静态方法，并使用@Parameters来修饰
	 * 4.为测试类声明一个带有参数的公共构造函数，并在其中为之声明变量赋值
	 *
	 */
	int expected = 0;
	int input1= 0;
	int input2 = 0;

	@Parameters
	public static Collection<Object[]> t(){
		return Arrays.asList(new Object[][]{
				{3,1,2},
				{4,2,2}

		});
	}
	public ParameterTest(int expected,int input1,int input2){
		this.expected = expected;
		this.input1 = input1;
		this.input2 = input2;
	}
	@Test
	public void testAdd(){
		assertEquals(expected, new Calculate().add(input1, input2));
	}
```
