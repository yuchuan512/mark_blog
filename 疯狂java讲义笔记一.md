title: 疯狂java讲义笔记一
date: 2016-05-12 15:47:29
categories: [java]
tags:
---

开发定义类，方法时也可以先添加文档注释，然后使用 javadoc 工具来生成自己的API文档
将字符串转换为数值类型
 Byte.parseByte(String str)
 Integer.parseInt(String str)
 Float.parseFloat(String str)
位运算符 & 按位与  | 按位或  ~按位非   ^按位异或   << 左移运算符 >> 右移运算符 >>>无符号右移运算符
 逻辑运算符 && 必须前后两个均为true,返回true   & 不短路与
 || 只要两个操作数有一个为true,就返回true   | 不短路或
 使用静态初始化的方法来初始化一个二维数组
 String [][] str1 = new String[][]{new String[3],new String[]{"hello"}};

public static void test(int a,String...books){
 for(String tmp : books){
 System.out.println(tmp);
 }
}
 数组形式的形参可以处于形参列表的任意位置，但个数可变的形参只能处于形参列表的最后，也就是，一个方法中最多只能有一个长度可变的形参

类的生命周期：一个类在使用之前要经过类加载，类验证，类准备，类解析，类初始化等几个阶段
对象并不是完全由构造器创建，在这之前系统会为该对象分配内存空间，并为这个对象执行默认初始化，这个对象已经产生了。
 构造器中通过this来引用他，使得这个对象作为构造器的返回值被返回，从而让外部程序可以访问该对象。

编译时的类型由声明该变量使用的类型决定，运行时的类型由实际赋给该变量的对象决定。如果编译时类型和运行时类型不一致，就出现了多态。
一个类里可以多多个初始化块，相同类型的初始化块之间有顺序，前面定义的初始化块先执行，后面定义的后执行
 初始化块总是在构造器执行之前执行

如果定义初始化块使用了static修饰符，则变成了静态初始化块，也被称为类初始化块。静态初始化块总是比普通初始化块先执行
 静态初始化块是类相关的，用于对整个类进行初始化处理。

对于特殊需求，可以通过重写equals方法来实现
 正确的重写equals方法英爱满足下列条件：
 自反性 对任意x,x.equals(x)一定返回true
 对称性  对任意x和y,若 x.equals(y) 返回true,则 y.equals(x)也返回true
 传递性  x->y y->z x->z
 一致性  对任意的x和y,无论调用多少次，结果不变
 对任何不是null的对象，x.equals(null) 一定返回false


public boolean equals (Object obj){
 if(this == obj){return true;}
 if(obj != null && obj.getClass() == Person.class){
  Person PersonObj = (Person)obj;
  if(this.getIdStr().equals(PersonObj.getIdStr()))
  return true;
  }
}

Hash就是把任意长度的输入，通过散列算法，变变换成固定长度的输出，输出就是散列值
 这种转换是一种压缩映射，也就是说，散列值的空间通常远小于输入的空间
 不同的输入可能会散列成相同过的输出，而不可能从散列值来唯一的确定输入值
   对象相等则hashCode 一定相等，hashCode 相等对象未必相等

缓存实例eg:
 public class CacheImmutale {
private final String name;
public CacheImmutale(String name){
this.name = name;
}
private String getName() {
return name;
}
private static CacheImmutale[] cache = new CacheImmutale[10];
private static int pos = 0;
public static CacheImmutale valueOf(String name){
for(int i = 0;i < pos;i++){
if(cache[i] != null && cache[i].getName().equals(name)){
return cache[i];
}
}
if(pos == 10){
cache[0] = new CacheImmutale(name);
pos = 1;
return cache[0];
}else{
cache[pos++] = new CacheImmutale(name);
return cache[pos-1];
}
}
public boolean equals(Object obj){
if(obj instanceof CacheImmutale){
CacheImmutale ci = (CacheImmutale)obj;
if(name.equals(ci.getName()))
return true;
}
return false;
}
public int hashCode(){
return name.hashCode();
}


抽象父类可以只定义需要使用的某些方法，其余则留给其子类实现。
 eg:
 public abstract class SpeedMeter {
private double turnRate;
public SpeedMeter(){

}
public abstract double getRadius();
public void setTurnRate(double turnRate){
this.turnRate = turnRate;
}
public double getSpeed() {
return java.lang.Math.PI * 2 *getRadius() * turnRate;
}
 }


 public class CarSpeedRate extends SpeedMeter{
public double getRadius(){
return 0.23;
}
public static void main(String[] args) {
CarSpeedRate csm = new CarSpeedRate();
csm.setTurnRate(15);
System.out.println(csm.getSpeed());
}
 }


接口和抽象类的不同点
 接口体现的是一种规范，抽象体现的是一种模板设计
 接口里只能包含抽象方法，不包含已经提供实现的方法；抽象类可以包含普通方法
 接口里不能定义静态方法；抽象类里可以定义静态方法
 接口只能定义静态常量属性，不能定义普通属性；抽象类可以静态常量属性，也可以定义普通属性
 接口不包含构造器；抽象类可以包含构造器，抽象类的构造器并不是用于创建对象，而让其子类调用这些构造器来完成属于抽象类的初始化操作
 接口不能包含初始化块，但抽象类可以包含初始化块。
 一个类最多只能有一个直接父类，包括抽象类；但一个类可以直接实现多个接口，通过实现多个接口可以弥补java单继承的不足

Object[] toArray() 把集合装换成一个数组
 int size() 返回集合里元素的个数
 Iterator iterator()  返回一个Iterator对象，用于遍历集合里的对象
 可以使用泛型来限制集合里元素的类型，并让集合记住所有集合元素的类型

当使用Iterator对集合元素进行迭代时，Iterator并不是把集合元素本身传给了迭代变量，而是把集合元素的值传给了迭代变量
 所以修改迭代变量的值对集合元素本身没有任何影响

重写hashCode()方法的基本原则：
 当两个对象通过equals() 方法比较返回true时，这两个对象的hashCode应该相等。
 对象中功能用作equals比较标准的属性，都应该用来计算hashCode值
 HashSet集合判断两个元素相等的标准是两个对象通过equals方法比较相等，并且两个对象的hashCode()方法返回值也相等

TreeSet (排序)是SortedSet接口的唯一实现，可以确保集合元素处于排序状态
如果试图把一个对象添加进TreeSet时，则该对象的类必须实现Comparable接口，否则异常
Comparator comparator() 返回当前Set使用的Comparator或者返回null，表示以自然方式排序
 Object first() 集合中第一个
 Object last()  集合中最后一个
 Object lower(Object e)  返回集合中位于指定元素之前的元素，注意是一个元素
 Object higher(Object e)  之后的元素，注意是一个元素，不是返回集合
 SortedSet subSet(fromElement,toElement)  返回此Set的集合，范围  包含开始，不包含结尾
 SortedSet headSet(toElement)  返回Set的子集，由小于toElement的元素组成
 SortedSet tailSet(fromElement) 返回Set的子集，由大于或等于fromElement的元素组成


操作数组的工具类 Arrays.asList(Object ...a)方法可以把一个数组或指定个数的对象转换成一个List集合，这个list既不是ArrayList实现类
 的实例，也不是Vector实现类的实例，而是Arrays的内部类ArrayList的实例

List方法：
void add(int index,Object element)
boolean addAll(int index,Collection c) 将集合c所包含的所有元素插入在index处
Object get(int index)
int indexOf(Object o)  返回对象o在list出现的位置索引
int lastIndexOf(Object o) 返回对象o在list集合中最后一次出现的位置索引
Object remove(int index) 删除并返回index索引处的元素
Objece set(int index,Object element) 将index处的元素替换成element对象，返回新元素
List subList(int fromIndex,int toIndex) 返回从fromindex(包含)到toindex(不包含)所有集合元素的子集合
List的indexOf 方法判断两个对象相等的条件是通过equals方法比较返回true




List额外提供了一个listIterator()方法,该方法返回一个在Iterator接口基础上增加了如下方法：
boolean hasPrevious() 返回该迭代器关联的集合是否还有上一个元素
Object previous() 返回迭代器的上一个元素
void add()  在指定位置插入一个元素
List<String> booklist = new ArrayList<String>();
ListIterator<String> lt = booklist.listIterator();


ArrayList和Vector类都是基于数组实现的List类，提供了如下两个方法来操作capacity属性
void ensureCapacity(int minCapacity) 将ArrayList或Vector集合的capacity增加minCapacity
void trimToSize() 调整ArrayList或Vector集合的capacity为列表当前大小，可调用该方法减少ArrayList或Vector对象的存储空间
ArrayList和Vector显著区别是：ArrayList是线程不安全的，Vector集合则是线程安全的


vector提供了栈 stack。方法: peek()  pop()  push(Object item)
与java其他集合一样，进栈出栈的都是Object，因此从栈中取出元素后必须做类型转换
Object peek()  返回栈的第一个元素，但不出栈
Object pop()  返回栈的第一个元素，出栈
void push(Object item) 将其进栈


操作数组的工具类 Arrays，asList(Object ...a)方法可以把一个数组或指定个数的对象转换成一个List集合，这个list既不是ArrayList实现类
的实例，也不是Vector实现类的实例，而是Arrays的内部类ArrayList的实例


Queue接口所提供的方法：用于模拟队列
void add(Object e)  加入此队列的尾部
Object element(); 获取队列头部元素，不删除
Object peek()读取队首元素
Object poll()获取队首元素并且删除
Objece remove()


Queue有两个常用的实现类： LinkedList  和   PriorityQueue


Deque接口定义了一些可以双向操作队列的方法
void addFirst(Object e)
void addLast(Object e)
Iterator descendingIterator() 返回以该双向队列对应的迭代器，该迭代器将以逆向顺序迭代队列中元素
Object getFirst() 获取，但不删除双向队列的第一个元素
Object getLast()  获取，但不删除双向队列的末尾
boolean offerFirst(Object e) 将指定元素插入该双向队列的开头
boolean offerLast(Object e)  将指定元素插入该双向队列的末尾
Object peekFirst()  获取，但不删除该双向队列的第一个元素
Object peekLast()  获取，但不删除该双向队列的最后一个元素
Object pollFirst() 获取，并删除双向队列的第一个元素
Object pollLast()获取，并删除双向队列的最后一个元素
Object pop() 出该双向队列表示的栈中的第一个元素
void push(Object e)  将一个元素push进该双向队列所表示的栈中


Object removeFirst() 获取删除该双向队列的第一个元素
Object removeFirstOccurrence(Object o) 删除该双向队列的第一次的出现元素o
removeLast() 获取删除该双向队列的最后一个元素
removeLastOccurrence(Object o) 删除该双向队列的最后一次出现元素o


LinkedList集合不仅提供了List功能，还提供了双向队列，栈的功能
PriorityQueue 保存队列元素的顺序并不是按加入队列的顺序，而是按队列元素的大小进行重新排序


Map接口中定义了如下常用的方法：
void clear() 删除Map对象中所有key-value对
boolean containsKey(Object key) 查询Map中是否包含指定key，如果包含返回true
boolean containsValue(Object value) 查询Map是否包含一个或多个value，如果包含返回true
Set entrySet()返回Map所包含的key-value对所组成的Set集合
Object get(Object key) 返回指定可以所对应的value
boolean isEmpty() 查询该Map是否为空
Set keySet()  返回该Map中所有可以所组成的Set集合
Object put(Object key,Object value)  添加一个key-value对，如果当前Map中已有一个与该key相等的key-value对，则新的key-value会覆盖原来的
void putAll(Map m) 将指定Map中的key-value对复制到本Map中
Object remove(Object key) 删除指定key 所对应的key-value对，返回被删除key所关联的value
int size()  返回Map中的key-value对的个数
Collection values()  返回该Map里所有value组成的collection


Map中博阿含一个内部类，Entry.该类封装了一个key-value对，Entry包含三个方法：
Object getKey() 返回该Entry里包含的key 值
Object getValue()  返回该Entry里包含的value值
Object setValue(V value) 设置该Entry里包含的value值，并返回新设置的value值


Hashtable和HashMap存在两点典型区别：
HashTable 是一个线程安全的Map实现，但HashMap是线程不安全的实现
Hashtable不允许使用null作为key和value


Properties类是HashTable类的自雷，可以把Map对象和属性文件关联起来，从而可以把Map对象中的key-value对写入属性文件
也可以把属性文件中的属性名属性值加载到Map对象中。
Properties提供了三个方法来修改Properties里的key,value值
String getProperty(String key)   获取Properties中指定属性名对应的属性值
String getProperty(String key,String defaultValue) 类似于前一个方法，如果Properties中不存在key时，返回默认值
Object setProperty(String key,String value) 设置属性值
void load(InputStream inStream)   从属性文件(以输入流表示)中加载属性名=属性值
void store(OutputStream out,String comments)  将Properties中的key-value对写入指定属性文件
eg:
Properties props = new Properties();
props.setProperty("username", "yee");
props.setProperty("password", "12334");
props.store(new FileOutputStream("a.ini"), "comment line");
Properties props2 = new Properties();
props2.setProperty("gender", "male");
props2.load(new FileInputStream("a.ini"));


操作集合的工具类 ： Collection
Java提供了一个操作Set,List和Map等集合的工具类 Collections，该工具提供了大量方法对集合排序，查询和修改操作
还提供了将集合对象设置为不可变，对集合对象实现同步控制等方法
Collections提供了如下几个方法用于对List集合元素进行排序
static void reverse(List list)  反转指定List集合中元素的顺序
static void shuffle(List lsit)  随机排序，shuffle方法模拟了洗牌动作
static void sort(List list)  自然顺序升序排序
static void sort(List list,Comparator) 根据Comparator产生的顺序对List集合的元素进行排序
static void swap(List list,int i,int j) 将制定List集合中 i 处元素和 j 处元素交换
static void rotate(List list, int distance) 将制定集合中i处元素和 list.length-i 处元素进行交换


static int binarySearch(List list,Object key)    使用二分搜索法搜索指定List集合
static Object max(Collection coll)   根据自然顺序，返回给定集合最大元素
static Object max(Collection coll,Comparator comp)  根据指定Comparator产生的顺序，返回给定集合最大元素
static Object min(Collection coll)     自然顺序，返回给定集合最小元素
static Object min(Collection coll,Comparator comp)    根据指定Comparator产生的顺序，返回给定集合最小元素
static void fill(List list,Object obj)   使用指定元素obj替换指定List集合的所有元素
static int frequency(Collection c,Object obj)   返回指定集合中等于指定对象的元素数量
static int indexOfSubList(List source,List target)   返回子List对象在母List对象中第一次出现的位置索引，若没有，返回-1
static int lastIndexOfSubList(List source,List target)  返回子List对象在母List对象中最后一次出现的位置索引，若没有，返回-1
static boolean replaceAll(List list,Object oldVal,Object newVal)   替换


同步控制：
Collections类中提供了多个synchronizedXxx方法，该方法返回指定集合对象对应的同步对象，从而可以解决多线程并发访问集合时的线程安全问题
Collections c = Collections.synchronizedCollection(new ArrayList())
List list = Collections.synchronizedList(new ArrayList())
Set s = Collections.synchronizedSet(new HashSet())
Map m = Collections.synchronizedMap(new HashMap())
在上面的实例中，直接将新创建的集合对象传给了Collections的synchronizedXxx方法，这样可以直接获取List,Set,Map 的线程安全时限版本。






泛型中子类重写父类方法时，要与父类的类型一致
List<String> ls = new ArrayList<String>();


java.util.Scanner
sr.useDelimiter("\n");
sr.hasNext()
sr.hasNextLine()








读取文件：Scanner sr = new Scanner(new File("e:\\a.txt"));


使用bufferedreader读取
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String sr = null;
while((sr=br.readLine())!=null){
System.out.println(sr);
}




使用Calendar
Calendar calendar =  Calendar.getInstance()


正则表达式：
boolean matches(String regex)
String replaceAll(String regexx,String replacement)
String replaceFirst(String regex,String replacement)
String[] split(String regex)




网状型数据库
层次性数据库
关系型数据库
面向对象数据库


SQL语句的关键字不区分大小写

java_300


取得选中项目 jcb_dish.getSelectedItem().toString()

for循环 for(String item:value){System.out.println(item+"")}


arr1复制到arr2中  int arr[]=Arrays.copyOfRange(arr1,5,arr1.length);


java的java.util.Arrays类中提供了sort()方法，可以采用二分排序


数组声明初始化：int arr[]=new int[]{22,23,45}
int arr2[]={23,45,34}


java.util.Arrays   Arrays.binarySearch(数组，关键字)
  Arrays.binarySearch(数组，开始（包含），结束（不包含），关键字)


简易for循环一般用来遍历List对象或Iterator对象


面向对象特征：封装，继承，多态


OOP(object-oriented programming) 面向对象编程
关键字abstract用于定义抽象类和抽象方法，抽象类是一种不可以被实例化的类


方法重载：在同一个类中有相同名称，不同参数的方法，这些方法有相同的功能


成员变量VS局部变量


只能通过构造方法创建对象吗 ？
不是，还可以通过工厂方法创建对象，通过反射创建对象


隐藏类的属性，使其不被其他类使用 private


某个变量的值能被所有对象共享，可以将变量声明为静态变量 private static int num=10;
静态常量  public static final double pi=3.14
用static 修饰的方法称为静态方法


static{}静态语句块  比如用在数据库连接，初始化图像等
final修饰常量


浅克隆：被克隆的各个对象都是基本类型，而不是引用类型
深克隆：存在引用类型 ，相当于在浅克隆的基础上对引用类型加以处理


两种方式实现克隆：序列化的方式   一次克隆各个可变的引用类型域的方式


匿名内部类是指没有指定类名的内部类，当某个类不需要重复使用时，就可以把该类定义为匿名内部类，由于匿名内部类 没有类名，所以在程序中只能使用一次，
   在图形化编程的事件监控器代码中，会大量使用匿名内部类


创建class对象的方法：
1.存在某个类完整路径的字符串，通过class类的forName()方法
2.知道某个类student  通过 student.class()
3.知道某个类的实例 如：student.getClass()


重写equals()方法遵循      自反性，对称性，传递性，一致性


在java中使用反射创建对象的两种方法:
1 通过使用class类的newInstance()方法实现   适合无参
2 使用constructor类实现  适合有参


自动装包指的是 基本类型自动转换为包装类
自动拆包指的是 包装类自动转换为基本类型


int 是Java中的基本数据类型，Integer是包装类，java将包装类作为对象调用


基本数据类型有 boolean char float double （四种整数） byte,short,int long






java笔记


数据类型可以从低变量到高变量
高精度到低精度 强制转换 int a=(int)1.2
精度由低到高 byte short int long float duoble
在java中的小数默认是double
class 类名{成员变量（属性），成员函数（方法），构造方法}
一个类可以定义多个构造方法
this只能在类定义内部使用


类变量，类方法
static int total
//total 是静态变量，因此可以被任何一个对象访问
类变量是该类的所有对象共享的变量
类方法是属于所有对象实例的
加上static 成为类变量或者静态变量，否则实例变量
java面向对象编程的四大特征 ：（抽象） 封装，继承，多态
把一类食物的共同属性和行为提取出来，形成一个物理模型，称为抽象


封装就是把抽象出的数据和对数据的操作封装在一起
字体：window--general--appearance--colors and font--basic--YaHei


包名小写
常用的包 java.util.*工具    java.net.*网络


成员变量，成员方法都有访问修饰符
继承： class pupil extends stu
public protected 属性和方法都被继承，若不希望子类继承某个属性或者方法，则声明为私有的
方法重载  方法覆盖  @override
使用“+”与字符串连接的任何类型的数据都会转换成字符串进行连接
eg: "3+5="+3+5  返回的是“3+5=35”


java中String str=null 与String str=""的区别 ？
前者表示声明了一个String对象的引用str，但是没有为其分配内存空间
后者表示创建了一个长度为0的空字符串， and 在内存中为其分配了内存空间


被final关键字修饰的变量是不可以被继承的，没有被final关键字修饰的类是可以被继承的
 String类为final关键字修饰的类，所以不能被继承


str.toLowerCase()  str.toUpperCase()  返回一个处理过的字符串，str本身不会有任何改变


"=="和equals()区别？
“==”比较的是两个对象使用的内存地址和内容；
equals() 比较的是内容


判断字符串是否为空：
1.sVar==null
2.sVar.equals("")
3.sVar.length()==0


去掉字符串的空格： String str=" "  str.replaceAll(" ","") OR  str.trim()


子字符串  str.substring(x[包含],y[不包含])


char[] chars=str.toCharArray();   //将字符串存到字符数组中
for(int i=0;i<chars.length;i++){bool=character.isDigit(char[i]); if(bool==true){break;}  }
str.indexOf("h") 在字符串中搜寻指定字符


进制转换 Integer类的几个方法
二进制to十进制 int no=Integer.parseInt("10001000",2)  parseInt(String s,int radix[基数])


toBinaryString(int i)    toHexString(int i)   toOctalString(int i)


StringBuilder对象是一个可变的字符序列，大大提高了频繁增加字符串的效率  “+”会产生新的String实例，在内存中创建新的字符串对象


StringBuffer 类也是一个包含有缓冲区的字符串对象   StringBuilder类是非线程安全的，StringBuffer是线程安全的


str=str.replace(" ","");   str.replaceFirst(" "," "); str.replaceAll(String regex,String replacement);


编码格式：byte[] btutf=str.getBytes("UTF-8");
String strz=new String(btutf,"UTF-8");


String value="dfasdfds"  byte[] bt=value.getBytes(); //转换为字符数组


使用try...catch,try...catch...finally.以及try...finally等语句块进行异常处理，还可以通过throws关键字声明方法抛出的异常
通过throws声明方法抛出的异常可以简化程序编写，只在最后调用方法时进行异常处理


对于字节文件：FileInputStream  FileOutputStream
对于字符文件：FileReader   FileWriter


Java枚举  values()返回一个包含全部枚举元素的数组
valuesOf()返回当前字符串对应的枚举元素


泛型提高程序健壮性，指的是把一些运行时Bug改为编译时Bug，通过及时修复Bug而提高程序健壮性


泛型声明类型参数，命名要求：E:元素  K:键  N:数字  V:值
 T:类型，S,U，V类型


Java语言不支持多继承，因此在限制类型参数范围是，不能使用两个类


在java中，数组是协变的，例如：Integer类是Number类的子类，那么可以
将Integer类型的数组复制给Number类型的数组


泛型不能协变
泛型中的擦除是指在程序编译后，就将类型信息擦除了


新建一个进程，两种方法。
1.public class Test extends Thread{} //继承Thread
2.public class Test implements Runnable{}  //Runnable接口


由于java仅支持单继承，却可以实现多个接口，推荐用第二种方法


java
并发性和并行性区别：
并行是指在同一时刻，有多条指令在多个处理器上同事执行；并发是指在同一时刻只能有一条指令执行，
但多个的进程指令被快速轮换执行，使得在宏观上具有多个进程同时执行的效果


一个进程你可以拥有多个线程，一个线程必须有一个父进程。线程可以拥有自己的堆栈，自己的程序计数器和局部变量
但不拥有系统资源，它与父进程的其他线程共享 该进程所拥有的全部资源。
使用继承thread时获得当前线程对象比较简答，直接使用this就可以了；使用时限Runnable接口时要获得当前
对象必须使用Thread.currentThread()方法。


每个线程的优先级都与创建它的父线程具有相同的优先级，在默认情况下，main线程具有普通优先级
synchronized关键字可以i修饰方法，可以修饰代码块


yield让出cpu的使用权，让给同优先级的或者更高优先级的
join()线程等待被join的线程结束才继续运行


重写hashCode()方法和equals()方法
public int hashCode(){
return accountNo.hashCode();
}
public boolean equals(Object obj){
if(obj!=null&&obj.getClass()==Account.class){
Account target = (Account)obj;
return target.getAccountNo().equals(accountNo);
}
return false;
}


public synchronized void draw(){}通过synchronized关键字来声明加锁


Lock是控制多个线程对共享资源进行访问的工具。
class x{
private final ReentrantLock lock = new ReentrantLock();
public void m(){
lock.lock();
try{


}
finally}
lock.unlock();
}
}
加锁和释放锁要出现在一个块结构中，而且当获取了多个锁时，他们必须以相反的顺序释放
ReentrantLock锁具有重入性，也就是可以对已经加锁的ReentrantLock再次加锁。
Object类提供的wait(),notify()和notifyAll()三个方法，这三个方法不属于Thread类，而是属于Object
但这三个方法必须由同步监视器对象来调用，分为两种情况
1）使用synchronized修饰的同步方法，因为该类默认实例就是同步监视器，所以在同步方法中直接调用这三个方法
2）使用synchronized修饰的同步代码块，同步监视器是synchronized括号里的对象，必须使用该对象调用这三个方法


自定义线程捕获异常
package com.yu;
public class ExHandler {
public static void main(String[] args) {
Thread.currentThread().setUncaughtExceptionHandler(new MyExHandler());
int a = 5/0;
}
}
class MyExHandler implements Thread.UncaughtExceptionHandler{
@Overrideto
public void uncaughtException(Thread t, Throwable e) {
// TODO Auto-generated method stub
System.out.println(t+"线程出现问题"+e);
}
}


使用线程池可以很好的提高性能，尤其是当程序需要创建大量生存期很短暂的线程时，更应该考虑使用线程池
public class ThreadPoolTest {
public static void main(String[] args) {
ExecutorService pool = Executors.newFixedThreadPool(6);
pool.submit(new TestThread());
pool.submit(new TestThread());
pool.shutdown();
}
}
class TestThread implements Runnable{
@Override
public void run() {
// TODO Auto-generated method stub
for(int i = 0;i < 100;i++){
System.out.println(Thread.currentThread().getName()+"的i为:"+i);
}
}
}


计算机网络协议通常由三部分组成，语义部分，用于决定给双方对话的类型；语法部分，用于决定双方对话的格式
变换规则，用于决定通信双方的应答关系


java为网络支持提供了java.net包，该包下的URL和URLConnection等类提供了以编程方式访问Web服务的功能
而URLDecoder和URLEncoder则提供了普通字符串和application/x-www-form-urlencoded MIME字符串相互转换的静态方法


多线程下载；如果要实现断点下载，则还需要额外增加一个配置文件（所有支持断点下载的工具都会在下载
开始生成两个文件，一个是与网络资源大小相同的文件夹，一个是配置文件），该配置文件分别记录每个线程
已经下载到了那个字节，当网络断开后再次开始下载时，每个线程根据配置文件里记录的位置向后下载即可


java使用Socket对象来代表两端的通信端口，并通过Socket产生IO流来进行网络通信


java获取指定元素的节点
public static void main(String[] args) throws ParserException {
String url = "http://www.ttmeishi.com/CaiXi/";
Parser parser = new Parser(url);
   parser.setEncoding("utf-8");
   NodeFilter filter = new TagNameFilter("a");
   NodeList list = parser.extractAllNodesThatMatch(filter);
System.out.println(list.toHtml());
= list.elementAt(1);
}


byte  -128 ~ 127
short -32768 ~ 32767
NullPointerException 空指针异常，引用数据类型，观察其是否被正确实例化
匿名对象 new Person("zhang",20).tell()
数组静态初始化： int[] data = new int[]{20,32,32}
数组动态初始化： int[] data = new int[3]
数组排序  Arrays.sort(data)
System.arrayCopy(dataB,5,dataA,2,3);
每一个字符串都是String的匿名对象。
在String类进行设计的时候采用了一种共享设计模式的概念，在每一个运行的JVM层存在一个字符串的对象池，如果用户直接赋值，会将字符串
放入到池中，以供其他继续使用直接赋值方式的String对象使用。


String str = new String("hello")创建了两个实例化对象，一个是String类的匿名对象hello，一个是使用关键字new实例化的String对象


String类的常用方法：
字符串与字符：
public String(char[] value) 将全部的字符数组 ------> 字符串
public String(char[] value,int offset,int count)  将部分字符数组变为字符串
public charAt(int index)  取得指定索引位置上的字符
public char[] toCharArray()  将字符串-----> 字符数组
字符串与字节
public String(byte[] bytes)
public String(byte[] bytes,int offset,int length)
public byte[] getBytes()
public Bytes[] getBytes(String charSetName)
字符串比较： equals(String str)   equalsIgnoreCase(String str)   compareTo(String str)
字符串查找
public boolean contains(String s)
public int indexOf(String s) //查找字符串的位置
public int indexOf(String str,int fromIndex)
public int lastIndexOf(String str)
public int lastIndexOf(String str,int fromIndex)
public boolean startsWith(String prefix)  //是否以指定字符串开头
public boolean startsWith(String prefix,int offset)
public boolean endsWith(String suffix)
字符串替换
public String replaceAll(String regex,String replacement)
public String replaceFirst(String regex,String replacement)
字符串截取：
public String subString(int beginIndex)
public String subString(int beginIndex,int endIndex)
字符串拆分：
public String[] split(String regex)
public String[] split(String regex,int limit)
其他方法：
public boolean isEmpty()
public int length()
public String trim()
public String toLowerCase()
public String toUpperCase()
public String concat(String str)


引用传递：
public static void main(String args[]){
String str = "hello";
fun(str);
System.out.println(str);


}
public static void fun(){
temp = "world";
}
结果输出  hello，因为temp改变了指向


static 定义的属性保存在全局数据区， 使用 类名称.static属性 的方式来完成
java主要存在四块内存空间
1.栈内存空间，保存所有的对象名称
2.堆内存空间 保存每个对象的具体属性内容
3.全局数据区 保存static类型的属性
4.全局代码区  保存所有的方法定义


private static int count = 0;
public Person(){
System.out.println("num is:" + ++count);
}
public static void main(String[] args) {
new Person();
new Person();
new Person();
}


代码块有 普通代码块，构造块，静态块
构造块优先于构造方法执行
静态块优先于构造块执行，而且不管有多少个实例化对象产生，静态块只会执行一次，主要作用是为类中的static属性初始化
主类中的静态块的执行优先于主方法


内部类的用法:
class Outer{
private String msg="hello";
class Inner{
public void print(){
System.out.println(msg);
}
}
public void fun(){
Inner in = new Inner();
in.print();
}
}
public  class TestDemo{
public static void main(String[] args) {
Outer out = new Outer();
out.fun();
}
}


子类对象的实例化操作会调用父类的构造方法，super调用父类构造时一定要放在构造方法的首行上。
final:
1.final修饰的类不能有子类，无法被其他类继承。
2.使用final定义的方法不能被子类所重写
3.使用final定义的变量，就表示敞亮，常量在定义时必须设置默认值，且不能修改。


构造方法私有化---> 隐藏构造方法，单例模式
eg:
class Singleton{
private final static Singleton instance = new Singleton();
private Singleton(){}
public static Singleton getInstance()
{return instance;}
public void print(){
System.out.println("hello");
}
}
public class Test{
public static void main(String args[]){
Singleton inst = Singleton.getInstance();
inst.print();
}
}


多例模式：
class Sex {
private static final Sex MALE = new Sex("男");
private static final Sex FEMALE = new Sex("女");
private String title;
private Sex(String title){
this.title = title;
}
 public static Sex getInstance(String msg){
switch(msg){
case "male":
return MALE;
case "female":
return FEMALE;
default:
return null;
}
}
}


如果在try语句中有一个return语句，那么紧跟在try后面的finally一定会被执行，并且是在return 前执行
RuntimeException和Exception区别，并列举出 几个常见的RuntimeException
Exception定义了必须处理的异常，而RuntimeException定义的异常可以选择性的进行处理
RuntimeException是Exception的子类，常见的RuntimeException有： NumberFormatException ClassCastException NullPointerException
ArithmeticException ArrayIndexOutOfBoundException


断言指的是程序执行到某行后，其结果一定是预期的结果
自定义异常：
class MyException extends Exception{
public MyException(String msg) {
// TODO Auto-generated constructor stub
super(msg);
}
}
main:  throw new MyException("it's a exception");
异常是导致程序中断运行的一种指令流
throws用在方法声明处，表示本方法不处理异常
throw表示在方法中手工抛出一个异常
不同包的同名类，则导入的时候要指定完整名称 eg: cn.mldn.util.Message msg = new cn.mldn.util.Message()


命名规范：
类名称  每一个单词的开头首字母大写，如TestDemo
变量名称 第一个单词的首字母小写，之后每个单词的首字母大写 如stuName
方法名称  第一个单词的首字母小写，之后每个单词的首字母大写 如 printInfo()
常量名称  每个字母大写 如FLAG
包名称  所有字母小写  如 cn.mldnjava.util


可变参数： 可以比较直观的传递任意多个参数
public static int add(int... data) {
int sum = 0;
for (int x = 0; x < data.length; x++) {
sum += data[x];
}
return sum;
}


foreach 语句
for(int x:data){
sum+=x;
}


泛型：类中操作的属性或方法的参数类型不在定义时声明，而是在使用时动态设置
枚举来写多例设计模式
enum Color{
RED,GREEN,BLUE;
}
Color c = Color.RED;


jdk1.7之后，switch可以操作Strint数据
可以使用通配符？接受全部的泛型类型对象
通过<？extend 类>可以设置泛型的上限，<？super 类>可以设置泛型的下限
在java中使用enum关键字定义一个枚举类，每个枚举类都是集成Enum类
enum Color{
RED,GREEN,BLUE;
}
for(Color c:Color.values())  //取得全部的枚举值
System.out.println(c.ordinal()+" " + c.name())


线程的命名和取得
public Thread(Runnable target ,String name)  //实例化线程对象，接受Runnable接口子类对象，同时设置线程名称
public final void setName(String name) //设置线程名称
public final String getName()  //取得线程名称
线程的休眠  在run方法中，Thread.sleep(100)   System.out.println(Thread.currentThread().getName())
线程优先级设置
public static final int MAX_PRIORITY
public static final int NORM_PRIORITY
public static final int MIN_PRIOTITY
public final void setPriority(int newPriority) // Runnable mt = new Runnable() ; Thread t = new Thread(mt,"线程A")
public final int getPriority() //  t.setPriority(Thread.MAX_PRIORITY)  System.out.println(Thread.currentThread().getPriority())


同步：使用同步代码块，或者使用同步方法
同步代码块只需要在执行的大括号前加上 synchronized(this)
同步方法 public synchronized void sale()
static native synchronized 都不能和abstract同时声明方法


解决重复的问题需要等待及唤醒机制，这一机制的实现只能依靠Object类完成，Object定义了3个方法来完成线程操作
public final void wait() throws InterruptedException
public final void notify()
public final void nofityAll()


sleep()和wait()区别
sleep是Thread类定义的static方法，表示线程休眠，将执行机会让给其他线程，但监控状态依然保持，到时候会自动恢复
wati是Object类定义的方法，表示线程等待，一直到执行了notify或notifyAll方法后才结束


一般 通过设置一个标志位的方式停止一个线程的运行
String不适合被频繁修改的字符串，这种情况下可以使用StringBuffer，方便用户进行内容的修改
一个类是否可以继承String类？ 不能，String类使用了final定义，不能被其他类继承


操作一：将String变为StringBuffer
方法一 利用StringBuffer的构造方法   public StringBuffer(String str)  ---> StringBuffer bf = new StringBuffer(str);
方法二   StringBuffer的append()方法   buf.append(str)


操作二 将StringBuffer变为String  StringBuffer的toString()方法  String str = buf.toString() 方法
StringBuffer常用方法：
bf.reverse()  bf.replace(int start,int end,String str)  br.insert(int offset,数据)


System类提供了两个常用方法：
public static void arrayCopy(Object src,int srcPos,Object dest,int destPos,int length)
public static long currentTimeMillis()
eg:long start = System.currentTimeMillis();    long end = System.currentTimeMillis(); --->(end - start)
日期操作类
public Date()
public Date(long date) //将数字变为Date类对象，long为日期时间数据
eg: Date date = new Date(System.currentTimeMillis());
public long getTime()  //将当期那日期时间变为long型
eg: Date date = new Date();
long num = date.getTime();


日期格式化操作类 SimpleDateformat
public SimpleDateFormat(String pattern)  //传入日期时间标记实例化对象
public final String format(Date date)   //将日期格式化为字符串数据
public Date parse(String source) throws ParserException  //将字符串格式化为日期数据
eg:
Date date = new Date();
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String str = sdf.format(date);   ----->   2015-04-28 11:13:54   //日期格式化为字符串


eg:
String str = "1991-08-20 12:12:12.122";
SimpleDateFormat sdf = new SimpleDateFormat("yy-MM-dd HH:mm:ss.SSS");
Date date = sdf.parse(str);   //将字符串格式化为日期数据


Random类
Random rand = new Random();  rand.nextInt(101);
大数字操作类：
java.math.BigInteger和java.math.BigDecimal 这两个类属于Number的子类
数组操作类： Arrays类
public static boolean equals(int[] a,int[] b)  //Arrays.equals(dataA,dataB)
public static void fill(int[] a,int val)   //Arrays.fill(dataA,3)
public static void sort(int[] a)   //Arrays.sort(dataA)
public static int binarySearch(int[] a,int key)    //Arrays.binarySearch(dataA,key)
public static String toString(int[] a)  //Arrays.toString(dataA)
普通的对象数组是不能够进行比较的，如果要比较需要比较器（Comparable接口就是一种比较器）的支持


比较器：
欲进行比较，需要实现Comparable接口
public class Person implements Comparable<Person>
重写compareTo方法
public int compareTo(Person o){
if(this.age > o.age){
return 1;
}else if(this.age < o.age){
return -1;
}else{
return 0;
}
}




对象克隆
实现Cloneable接口，但这个接口属于标识接口，只表示一种能力。
class Person implements Cloneable{  }
@Override
public String toString(){return "  ";}
@Override
protected Object clone() throws CloneNotSupportedException
 {return super.clone();}


正则表达式
所有正则匹配的符号都在  java.util.regex.Pattern  类中进行定义
//  \\ 匹配转移字符 "\"
\t匹配转移字符"\t"
^开始
$结束
.标识任意的一位字符
\d  表示任意的一位数字  \D非数字
\w 任意的一位字母,数字，下划线      \W非字母，数字，下划线
？0或1次
* 0次，1次或多次
+ 1次或多次
{n} 正好n次
{n,} n次以上
{n,m} 最少n次，最多m次
eg: 123.1   regex = "\\d+(\\.\\d+)"
String类对正则的支持
public boolean matches(String regex)   str.matches(regex)
public String replaceAll(String regex,String replacement)
public String[] split(String regex )
eg: String str = "a1bb2cc3dddd4eeeee5fff6eddd7oooo8iiiiii9";
String regex="\\d";
String result[] = str.split(regex);
public String[] split(String regex,int limit)


字符串---> 数字
int data = Integer.parseInt(str)
double data = Double.parseDouble(str)
表示任意一个用[],如[a-zA-z0-9\\.]，表示或者关系的用(xx|yy)
判断电话号码的regex      String regex = "((\\d{3,4}|\\(\\d{3,4}\\))-?)?\\d{7,8}";
简单的邮箱地址验证 String regex = "[a-zA-Z][a-zA-Z0-9]*@[a-zA-Z_0-9\\.]+\\.(com|cn|net)"


反射
Java IO操作
所有的IO操作都在java.io包中进行定义，而且整个java.io包实际上就是五个类和一个接口
五个类：File InputStrearm OutStream Reader Writer
一个接口： Serializable
public File(String pathname)  构造方法，给定一个要操作文件的完整路径
File file = new File("D:\\demo.txt");
file.exists()  file.createNewFile()  file.delete()
public boolean createNewFile() throws ArrayIndexOutOfBoundException
public boolean delete()
public boolean exists()
File file = new File("D:"+File.separator+"demo.txt");   File.separator是为了在不同平台运行


创建有目录的文件
public File getParentFile()  找到一个指定路径的父路径
public boolean mkdirs()   创建指定目录
File file = new File("D:"+File.separator+"hello"+File.separator+"test"+File.separator+"demo.txt");
if(!file.getParentFile().exists()){
file.getParentFile().mkdirs();            //若不存在父文件，则创建之
}
if(file.exists()){
file.delete();        //若文件不存在，创建之
}else{
file.createNewFile();
}


File的其他用法：
public String getName()   //取得文件名称
public boolean isDirectory()
public boolean isFile()  //file.isFile()
public boolean isHidden()
public long lastModified()   //文件的最后一次修改日期
public long length()  //取得文件大小，以字节为单位返回
public boolean renameTo(File dest)   //为文件重命名      file.renameTo(newName)
public File[] listFiles()   //将目录中所有文件以File对象数组的方式返回   File result[] = file.listFiles()
递归输出目录下的文件
public static void print(File file){
	if(file.isDirectory()){
	File result[] = file.listFiles();
		if(result!=null){
			for(int x = 0; x < result.length; x++)
				print(result[x]);
		}
	}
	System.out.println(file);
}

yuchuan-----------------
字节操作流  OutputStream  InputStream
字符操作流  Writer  Reader


FileOutputStream类的构造方法：
public FileOutputStream(FIle file) throws FileNotFoundException  实例化FileOutputStream，主要用于新建数据
public FileOutputStream(File file,boolean append) throws FileNotFoundException   主要用于追加数据
OutputStream定义的方法
pubic FileOutpubStream(File file)throws FileNotFoundExceptin
public void write(byte[] b) throws IOException
public void write(byte[] b,int off,int len) throws IOException
对于OutputStream而言，它本身定义的是一个抽象类，按照抽象类的使用原则，需要定义抽象类的子类，子类要为抽象类进行对象的实例化
而后调用的方法以父类为主，而具体的实现是实例化这个父类的子类完成的，关心的是子类的构造方法。


InputStream定义方法：
public abstract int read() throws IOException   //读取一个字节，到结尾返回 -1
public int read(byte[] b)throws IOException   //读取多个字节，返回的是读取的数据个数
public int read(byte[] b,int off,int len) throws IOException


如果希望对输出的内容增加换行显示，可以使用"\r\n"来表示
eg：把内容输出到指定文件
 File file = new File("D:"+File.separator+"test.txt");
OutputStream output = new FileOutputStream(file);
OutputStream output = new FileOutputStream(file,true); true表示追加数据
String data = "hello world";
output.write(data.getBytes());
output.close();
eg:从指定文件读取内容：
 InputStream input = new FileInputStream(file);
byte[] data = new byte[1024];
int len = input.read(data);
input.close();
System.out.println("data is:"+new String(data,0,len));
字节流和字符流的区别
字节流在IO操作时，直接针对的是操作的数据端(如文件)，而字符流操作时不是直接针对于终端，而是针对于缓存区的操作，而后由缓存区
操作终端，属于间接操作。
字节流没有使用缓冲区，而字符流使用了缓冲区
处理各种数据都可以通过字节流完成，而在处理中文时使用字符流会更好


单个字节读取：
while((temp=input.read())!=-1){
data[foot++]=(byte) temp;
}
字符输出流 Writer类本身是一个抽象类，那么要使用它依然要依靠其子类，比如FileWriter类
public abstract void close() throws IOException
public void write(String str) throws IOException
public void wirte(char[] buf) throws IOException
public abstract void flush() throws IOException
eg:
Writer out = new FileWriter(file);
String data = "\r\nnice to meet you .\n";
out.write(data);        //直接把字符串输出到文件中
out.close();


字符输入流
public abstract void close() throws IOException
public int read() throws IOException
public int read(char[] cbuf) throws IOException
Reader in = new FileReader(file);
char data[] = new char[1024];
int len = in.read(data);
System.out.println("data is:" +new String(data,0,len));
in.close();


转换流
将字节输出流变为字符输出流 OutputSteam -> Writer   OutputStreamWriter
将字节输入流变为字符输入流 InputStream ->Reader   InputStreamReader
eg:
OutputStream output = new FileOutputStream(file);
Writer out = new OutputStreamWriter(output);
out.write("hello world");
out.close();


InputStream抽象类下面 有   FileInputStream 和 ByteArrayInputStream
OutputStream抽象类下面有   FileOutputStream 和 ByteArrayOutputStream


缓冲区操作 BufferedReader de 构造方法： public BufferedReader(Reader in)
读取操作  public String readLine() throws IOException


BufferedReader buf = new BufferedReader(new InputStreamReader(System.in));
String str = buf.readLine()      //利用BufferedReader进行循环判断
if(str.matches("\\d+")){


num = Integer.parseInt(str);

System.out.println(num*num);


}
Scanner 方法
pubic Scanner(InputStream source)
public boolean hasNext(Pattern pattern)  //判断输入的数据是否符合指定的正则标准
public boolean hasNext()  //判断有输入内容
public boolean hasNextxxx()  //判断输入的是否为指定的数据类型
public String next()          //接受内容
public String next(Pattern pattern)  //接受内容，进行正则验证
public int nextXxx()   //接受指定的输入类型
public Scanner useDelimiter(String pattern)      sn.useDelimiter("\n");    //设置读取的分隔符
由输入验证生日：
eg:
if(sn.hasNext("\\d{4}-\\d{2}-\\d{2}")){
String str = sn.next("\\d{4}-\\d{2}-\\d{2}");
Date date = new SimpleDateFormat("yyyy-MM-dd").parse(str);


对象序列化
对象序列指的是可以将内存中保存的对象数据（主要指的是一个对象里面所包含属性内容）进行二进制数据传输的一种操作，要想
实现这样的二进制操作，对象所在的类就必须实现java.io.Serializable 接口（这个接口和Cloneable接口一样，都属于一种标识接口
表示一种能力）可以使用transient关键字定义不被序列化的属性。


ServerSocket类的常用操作方法
public ServerSocket(int port) throws IOException
public Socket accept() throws IOException
public void close() throws IOException
Socket的常用操作方法
public Socket(String host,int port)
public OutputStream getOutputStream()
public InputStream getInputStream()

java习惯用法总结


1. 实现equals()


class Person {
  String name;
  int birthYear;
  byte[] raw;
  public boolean equals(Object obj) {


    if (!obj instanceof Person)
      return false;
    Person other = (Person)obj;
    return name.equals(other.name)
        && birthYear == other.birthYear
        && Arrays.equals(raw, other.raw);
  }
  public int hashCode() { ... }
}
参数必须是Object类型，不能是外围类。


foo.equals(null) 必须返回false，不能抛NullPointerException。（注意，null instanceof 任意类 总是返回false，
因此上面的代码可以运行。）


基本类型域（比如，int）的比较使用 == ，基本类型数组域的比较使用Arrays.equals()。


覆盖equals()时，记得要相应地覆盖 hashCode()，与 equals() 保持一致。


2. 实现hashCode()


class Person {
  String a;
  Object b;
  byte c;
  int[] d;


  public int hashCode() {
    return a.hashCode() + b.hashCode() + c + Arrays.hashCode(d);
  }


  public boolean equals(Object o) { ... }
}
当x和y两个对象具有x.equals(y) == true ，你必须要确保x.hashCode() == y.hashCode()。
根据逆反命题，如果x.hashCode() != y.hashCode()，那么x.equals(y) == false 必定成立。
你不需要保证，当x.equals(y) == false时，x.hashCode() != y.hashCode()。但是，如果你可以尽可能地使它成立的话，这会提高哈希表的性能。




3.实现compareTo()


class Person implements Comparable<Person> {
  String firstName;
  String lastName;
  int birthdate;


  // Compare by firstName, break ties by lastName, finally break ties by birthdate
  public int compareTo(Person other) {
    if (firstName.compareTo(other.firstName) != 0)
      return firstName.compareTo(other.firstName);
    else if (lastName.compareTo(other.lastName) != 0)
      return lastName.compareTo(other.lastName);
    else if (birthdate < other.birthdate)
      return -1;
    else if (birthdate > other.birthdate)
      return 1;
    else
      return 0;
  }
}


4.实现clone()


class Values implements Cloneable {
  String abc;
  double foo;
  int[] bars;
  Date hired;


  public Values clone() {
    try {
      Values result = (Values)super.clone();
      result.bars = result.bars.clone();
      result.hired = result.hired.clone();
      return result;
    } catch (CloneNotSupportedException e) {  // Impossible
      throw new AssertionError(e);
    }
  }
}
使用 super.clone() 让Object类负责创建新的对象。
基本类型域都已经被正确地复制了。同样，我们不需要去克隆String和BigInteger等不可变类型。
手动对所有的非基本类型域（对象和数组）进行深度复制（deep copy）。
实现了Cloneable的类，clone()方法永远不要抛CloneNotSupportedException。因此，
需要捕获这个异常并忽略它，或者使用不受检异常（unchecked exception）包装它。


5.使用StringBuilder或StringBuffer


// join(["a", "b", "c"]) -> "a and b and c"
String join(List<String> strs) {
  StringBuilder sb = new StringBuilder();
  boolean first = true;
  for (String s : strs) {
    if (first)
    first = false;
    else
    sb.append(" and ");
    sb.append(s);
  }
  return sb.toString();
}
不要像这样使用重复的字符串连接：s += item ，因为它的时间效率是O(n^2)。
使用StringBuilder或者StringBuffer时，可以使用append()方法添加文本和使用toString()方法去获取连接起来的整个文本。
优先使用StringBuilder，因为它更快。StringBuffer的所有方法都是同步的，而你通常不需要同步的方法。


6.
Random rand = new Random();
System.out.println(rand.nextInt(6)+1);


7.使用Iterator.remove()


void filter(List<String> list) {
  for (Iterator<String> iter = list.iterator(); iter.hasNext(); ) {
    String item = iter.next();
    if (...)
      iter.remove();
  }
}


8.字符串反转(逆序)
public static String reverse(String s){
return new StringBuilder(s).reverse().toString();
}


9.启动线程
1)继承Thread类
class MyThread extends Thread{
public void run(){   ...   }
}
new MyThread.start();
2) 实现Runnable接口
class MyRunnable implements Runnable{
public void run(){   ...   }
}
new Thread(new MyRunnable()).start()
不要直接调用run()方法。总是调用Thread.start()方法，这个方法会创建一条新的线程并使新建的线程调用run()。


10.
如果try之前的语句运行失败并且抛出异常，那么finally语句块就不会执行
如果try语句块里面的语句抛出异常，那么程序的运行就会跳到finally语句块里执行尽可能多的语句，然后跳出这个方法（


11. 从输入流里读取字节数据


InputStream in = (...);
try {
  while (true) {
    int b = in.read();
    if (b == -1)
      break;
    (... process b ...)
  }
} finally {
  in.close();
}
read()方法要么返回下一次从流里读取的字节数（0到255，包括0和255），要么在达到流的末端时返回-1。


12. 从输入流里读取块数据


InputStream in = (...);
try {
  byte[] buf = new byte[100];
  while (true) {
    int n = in.read(buf);
    if (n == -1)
      break;
    (... process buf with offset=0 and length=n ...)
  }
} finally {
  in.close();
}
要记住的是，read()方法不一定会填满整个buf，所以你必须在处理逻辑中考虑返回的长度。


13.从文件里读取文本


BufferedReader in = new BufferedReader(
    new InputStreamReader(new FileInputStream(...), "UTF-8"));
try {
  while (true) {
    String line = in.readLine();
    if (line == null)
      break;
    (... process line ...)
  }
} finally {
  in.close();
}
BufferedReader对象的创建显得很冗长。这是因为Java把字节和字符当成两个不同的概念来看待（这与C语言不同）。
你可以使用任何类型的InputStream来代替FileInputStream，比如socket。
当达到流的末端时，BufferedReader.readLine()会返回null。
要一次读取一个字符，使用Reader.read()方法。
你可以使用其他的字符编码而不使用UTF-8，但最好不要这样做。


14.向文件里写文本


PrintWriter out = new PrintWriter(
    new OutputStreamWriter(new FileOutputStream(...), "UTF-8"));
try {
  out.print("Hello ");
  out.print(42);
  out.println(" world!");
} finally {
  out.close();
}
Printwriter对象的创建显得很冗长。这是因为Java把字节和字符当成两个不同的概念来看待（这与C语言不同）。
就像System.out，你可以使用print()和println()打印多种类型的值。
你可以使用其他的字符编码而不使用UTF-8，但最好不要这样做。


15.预防性检测（Defensive checking）数值
预防性检测对象
预防性检测数组索引
预防性检测数组区间


16.填充数组元素
byte[] a =new byte[10];
Arrays.fill(a,(byte)123);
for (byte b : a) {
System.out.println(b);
}


17.把4个字节包装（packing）成一个int


int packBigEndian(byte[] b) {
  return (b[0] & 0xFF) << 24
       | (b[1] & 0xFF) << 16
       | (b[2] & 0xFF) <<  8
       | (b[3] & 0xFF) <<  0;
}


int packLittleEndian(byte[] b) {
  return (b[0] & 0xFF) <<  0
       | (b[1] & 0xFF) <<  8
       | (b[2] & 0xFF) << 16
       | (b[3] & 0xFF) << 24;
}


18.把int分解（Unpacking）成4个字节


byte[] unpackBigEndian(int x) {
  return new byte[] {
    (byte)(x >>> 24),
    (byte)(x >>> 16),
    (byte)(x >>>  8),
    (byte)(x >>>  0)
  };
}


byte[] unpackLittleEndian(int x) {
  return new byte[] {
    (byte)(x >>>  0),
    (byte)(x >>>  8),
    (byte)(x >>> 16),
    (byte)(x >>> 24)
  };
}
总是使用无符号右移操作符（>>>）对位进行包装（packing），不要使用算术右移操作符（>>）。


eg
public class RightDef{
  int num1 = num2+2;
  static int num2 = 0;
}
static 修饰的变量属于类，类变量会随着类初始化得到初始化，因此num2的初始化时机总是处于num1的初始化时机之前

使用 static 修饰的变量是类变量，没有使用 static 修饰的是实例变量
在同一个JVM中，每个类只对应一个Class对象，但每个类可以创建多个Java对象
同一个JVM内的类变量只需一块内存空间，但是该类每创建一次实例，就需要为实例变量分配内存空间
每个类初始化完成之后，系统都会为该类创建一个对应的Class实例，程序可以通过反射来获取某个类对应的Class实例。
eg: 要获取person类的Class实例。通过 Person.class 或者 Class.forName("Person");


通过实例来访问某个类变量的时候，底层依然会转换为通过类访问类变量。


实例变量的初始化时机：
1）定义实例变量时指定初始值
2）非静态初始化块中对实例变量指定初始值
3）构造器中对实例变量指定初始值
1和2先于3执行，1,2的顺序与他们在源程序中的排列顺序相同
eg:
public Cat(String name,int age){ ... }
{weight=2.0}
double weight = 2.3
每次执行之后weight都是 2.3，因为非静态代码块在前面，先执行


类变量的初始化时机：
1）定义类变量时指定初始值
2）静态初始化块中对类变量指定初始值
这两种方式的执行顺序与他们在源程序中的排列顺序相同


final 可修饰变量，被final修饰的变量被赋值后不能重新赋值
final 可修饰方法，被final修饰的方法不能被重写
final 可修饰类  ，被final修饰的类不能派生子类

java
并发性和并行性区别：
并行是指在同一时刻，有多条指令在多个处理器上同事执行；并发是指在同一时刻只能有一条指令执行，
但多个的进程指令被快速轮换执行，使得在宏观上具有多个进程同时执行的效果


一个进程你可以拥有多个线程，一个线程必须有一个父进程。线程可以拥有自己的堆栈，自己的程序计数器和局部变量
但不拥有系统资源，它与父进程的其他线程共享 该进程所拥有的全部资源。
使用继承thread时获得当前线程对象比较简答，直接使用this就可以了；使用时限Runnable接口时要获得当前
对象必须使用Thread.currentThread()方法。


每个线程的优先级都与创建它的父线程具有相同的优先级，在默认情况下，main线程具有普通优先级
synchronized关键字可以i修饰方法，可以修饰代码块


yield让出cpu的使用权，让给同优先级的或者更高优先级的
join()线程等待被join的线程结束才继续运行


重写hashCode()方法和equals()方法
public int hashCode(){
return accountNo.hashCode();
}
public boolean equals(Object obj){
if(obj!=null&&obj.getClass()==Account.class){
Account target = (Account)obj;
return target.getAccountNo().equals(accountNo);
}
return false;
}


public synchronized void draw(){}通过synchronized关键字来声明加锁


Lock是控制多个线程对共享资源进行访问的工具。
class x{
private final ReentrantLock lock = new ReentrantLock();
public void m(){
lock.lock();
try{


}
finally}
lock.unlock();
}
}
加锁和释放锁要出现在一个块结构中，而且当获取了多个锁时，他们必须以相反的顺序释放
ReentrantLock锁具有重入性，也就是可以对已经加锁的ReentrantLock再次加锁。
Object类提供的wait(),notify()和notifyAll()三个方法，这三个方法不属于Thread类，而是属于Object
但这三个方法必须由同步监视器对象来调用，分为两种情况
1）使用synchronized修饰的同步方法，因为该类默认实例就是同步监视器，所以在同步方法中直接调用这三个方法
2）使用synchronized修饰的同步代码块，同步监视器是synchronized括号里的对象，必须使用该对象调用这三个方法


自定义线程捕获异常
package com.yu;
public class ExHandler {
public static void main(String[] args) {
Thread.currentThread().setUncaughtExceptionHandler(new MyExHandler());
int a = 5/0;
}
}
class MyExHandler implements Thread.UncaughtExceptionHandler{
@Overrideto
public void uncaughtException(Thread t, Throwable e) {
// TODO Auto-generated method stub
System.out.println(t+"线程出现问题"+e);
}
}


使用线程池可以很好的提高性能，尤其是当程序需要创建大量生存期很短暂的线程时，更应该考虑使用线程池
public class ThreadPoolTest {
public static void main(String[] args) {
ExecutorService pool = Executors.newFixedThreadPool(6);
pool.submit(new TestThread());
pool.submit(new TestThread());
pool.shutdown();
}
}
class TestThread implements Runnable{
@Override
public void run() {
// TODO Auto-generated method stub
for(int i = 0;i < 100;i++){
System.out.println(Thread.currentThread().getName()+"的i为:"+i);
}
}
}


计算机网络协议通常由三部分组成，语义部分，用于决定给双方对话的类型；语法部分，用于决定双方对话的格式
变换规则，用于决定通信双方的应答关系


java为网络支持提供了java.net包，该包下的URL和URLConnection等类提供了以编程方式访问Web服务的功能
而URLDecoder和URLEncoder则提供了普通字符串和application/x-www-form-urlencoded MIME字符串相互转换的静态方法


多线程下载；如果要实现断点下载，则还需要额外增加一个配置文件（所有支持断点下载的工具都会在下载
开始生成两个文件，一个是与网络资源大小相同的文件夹，一个是配置文件），该配置文件分别记录每个线程
已经下载到了那个字节，当网络断开后再次开始下载时，每个线程根据配置文件里记录的位置向后下载即可


java使用Socket对象来代表两端的通信端口，并通过Socket产生IO流来进行网络通信


java获取指定元素的节点
public static void main(String[] args) throws ParserException {
String url = "http://www.ttmeishi.com/CaiXi/";
Parser parser = new Parser(url);
   parser.setEncoding("utf-8");
   NodeFilter filter = new TagNameFilter("a");
   NodeList list = parser.extractAllNodesThatMatch(filter);
System.out.println(list.toHtml());
= list.elementAt(1);
}


byte  -128 ~ 127
short -32768 ~ 32767
NullPointerException 空指针异常，引用数据类型，观察其是否被正确实例化
匿名对象 new Person("zhang",20).tell()
数组静态初始化： int[] data = new int[]{20,32,32}
数组动态初始化： int[] data = new int[3]
数组排序  Arrays.sort(data)
System.arrayCopy(dataB,5,dataA,2,3);
每一个字符串都是String的匿名对象。
在String类进行设计的时候采用了一种共享设计模式的概念，在每一个运行的JVM层存在一个字符串的对象池，如果用户直接赋值，会将字符串
放入到池中，以供其他继续使用直接赋值方式的String对象使用。


String str = new String("hello")创建了两个实例化对象，一个是String类的匿名对象hello，一个是使用关键字new实例化的String对象


String类的常用方法：
字符串与字符：
public String(char[] value) 将全部的字符数组 ------> 字符串
public String(char[] value,int offset,int count)  将部分字符数组变为字符串
public charAt(int index)  取得指定索引位置上的字符
public char[] toCharArray()  将字符串-----> 字符数组
字符串与字节
public String(byte[] bytes)
public String(byte[] bytes,int offset,int length)
public byte[] getBytes()
public Bytes[] getBytes(String charSetName)
字符串比较： equals(String str)   equalsIgnoreCase(String str)   compareTo(String str)
字符串查找
public boolean contains(String s)
public int indexOf(String s) //查找字符串的位置
public int indexOf(String str,int fromIndex)
public int lastIndexOf(String str)
public int lastIndexOf(String str,int fromIndex)
public boolean startsWith(String prefix)  //是否以指定字符串开头
public boolean startsWith(String prefix,int offset)
public boolean endsWith(String suffix)
字符串替换
public String replaceAll(String regex,String replacement)
public String replaceFirst(String regex,String replacement)
字符串截取：
public String subString(int beginIndex)
public String subString(int beginIndex,int endIndex)
字符串拆分：
public String[] split(String regex)
public String[] split(String regex,int limit)
其他方法：
public boolean isEmpty()
public int length()
public String trim()
public String toLowerCase()
public String toUpperCase()
public String concat(String str)


引用传递：
public static void main(String args[]){
String str = "hello";
fun(str);
System.out.println(str);


}
public static void fun(){
temp = "world";
}
结果输出  hello，因为temp改变了指向


static 定义的属性保存在全局数据区， 使用 类名称.static属性 的方式来完成
java主要存在四块内存空间
1.栈内存空间，保存所有的对象名称
2.堆内存空间 保存每个对象的具体属性内容
3.全局数据区 保存static类型的属性
4.全局代码区  保存所有的方法定义


private static int count = 0;
public Person(){
System.out.println("num is:" + ++count);
}
public static void main(String[] args) {
new Person();
new Person();
new Person();
}


代码块有 普通代码块，构造块，静态块
构造块优先于构造方法执行
静态块优先于构造块执行，而且不管有多少个实例化对象产生，静态块只会执行一次，主要作用是为类中的static属性初始化
主类中的静态块的执行优先于主方法


内部类的用法:
class Outer{
private String msg="hello";
class Inner{
public void print(){
System.out.println(msg);
}
}
public void fun(){
Inner in = new Inner();
in.print();
}
}
public  class TestDemo{
public static void main(String[] args) {
Outer out = new Outer();
out.fun();
}
}


子类对象的实例化操作会调用父类的构造方法，super调用父类构造时一定要放在构造方法的首行上。
final:
1.final修饰的类不能有子类，无法被其他类继承。
2.使用final定义的方法不能被子类所重写
3.使用final定义的变量，就表示敞亮，常量在定义时必须设置默认值，且不能修改。


构造方法私有化---> 隐藏构造方法，单例模式
eg:
class Singleton{
private final static Singleton instance = new Singleton();
private Singleton(){}
public static Singleton getInstance()
{return instance;}
public void print(){
System.out.println("hello");
}
}
public class Test{
public static void main(String args[]){
Singleton inst = Singleton.getInstance();
inst.print();
}
}


如果在try语句中有一个return语句，那么紧跟在try后面的finally一定会被之星，并且是在return 前执行
RuntimeException和Exception区别，并列举出 几个常见的RuntimeException
Exception定义了必须处理的异常，而RuntimeException定义的异常可以选择性的进行处理
RuntimeException是Exception的子类，常见的RuntimeException有： NumberFormatException ClassCastException NullPointerException
ArithmeticException ArrayIndexOutOfBoundException


断言指的是程序执行到某行后，其结果一定是预期的结果
自定义异常：
class MyException extends Exception{
public MyException(String msg) {
// TODO Auto-generated constructor stub
super(msg);
}
}
main:  throw new MyException("it's a exception");
异常是导致程序中断运行的一种指令流
throws用在方法声明处，表示本方法不处理异常
throw表示在方法中手工抛出一个异常
不同包的同名类，则导入的时候要指定完整名称 eg: cn.mldn.util.Message msg = new cn.mldn.util.Message()


命名规范：
类名称  每一个单词的开头首字母大写，如TestDemo
变量名称 第一个单词的首字母小写，之后每个单词的首字母大写 如stuName
方法名称  第一个单词的首字母小写，之后每个单词的首字母大写 如 printInfo()
常量名称  每个字母大写 如FLAG
包名称  所有字母小写  如 cn.mldnjava.util


可变参数： 可以比较直观的传递任意多个参数
public static int add(int... data) {
int sum = 0;
for (int x = 0; x < data.length; x++) {
sum += data[x];
}
return sum;
}


foreach 语句
for(int x:data){
sum+=x;
}


泛型：类中操作的属性或方法的参数类型不在定义时声明，而是在使用时动态设置
枚举来写多例设计模式
enum Color{
RED,GREEN,BLUE;
}
Color c = Color.RED;


jdk1.7之后，switch可以操作Strint数据
可以使用通配符？接受全部的泛型类型对象
通过<？extend 类>可以设置泛型的上限，<？super 类>可以设置泛型的下限
在java中使用enum关键字定义一个枚举类，每个枚举类都是集成Enum类
enum Color{
RED,GREEN,BLUE;
}
for(Color c:Color.values())  //取得全部的枚举值
System.out.println(c.ordinal()+" " + c.name())


线程的命名和取得
public Thread(Runnable target ,String name)  //实例化线程对象，接受Runnable接口子类对象，同时设置线程名称
public final void setName(String name) //设置线程名称
public final String getName()  //取得线程名称
线程的休眠  在run方法中，Thread.sleep(100)   System.out.println(Thread.currentThread().getName())
线程优先级设置
public static final int MAX_PRIORITY
public static final int NORM_PRIORITY
public static final int MIN_PRIOTITY
public final void setPriority(int newPriority) // Runnable mt = new Runnable() ; Thread t = new Thread(mt,"线程A")
public final int getPriority() //  t.setPriority(Thread.MAX_PRIORITY)  System.out.println(Thread.currentThread().getPriority())


同步：使用同步代码块，或者使用同步方法
同步代码块只需要在执行的大括号前加上 synchronized(this)
同步方法 public synchronized void sale()
static native synchronized 都不能和abstract同时声明方法


解决重复的问题需要等待及唤醒机制，这一机制的实现只能依靠Object类完成，Object定义了3个方法来完成线程操作
public final void wait() throws InterruptedException
public final void notify()
public final void nofityAll()


sleep()和wait()区别
sleep是Thread类定义的static方法，表示线程休眠，将执行机会让给其他线程，但监控状态依然保持，到时候会自动恢复
wati是Object类定义的方法，表示线程等待，一直到执行了notify或notifyAll方法后才结束


一般 通过设置一个标志位的方式停止一个线程的运行
String不适合被频繁修改的字符串，这种情况下可以使用StringBuffer，方便用户进行内容的修改
一个类是否可以继承String类？ 不能，String类使用了final定义，不能被其他类继承


操作一：将String变为StringBuffer
方法一 利用StringBuffer的构造方法   public StringBuffer(String str)  ---> StringBuffer bf = new StringBuffer(str);
方法二   StringBuffer的append()方法   buf.append(str)


操作二 将StringBuffer变为String  StringBuffer的toString()方法  String str = buf.toString() 方法
StringBuffer常用方法：
bf.reverse()  bf.replace(int start,int end,String str)  br.insert(int offset,数据)


System类提供了两个常用方法：
public static void arrayCopy(Object src,int srcPos,Object dest,int destPos,int length)
public static long currentTimeMillis()
eg:long start = System.currentTimeMillis();    long end = System.currentTimeMillis(); --->(end - start)
日期操作类
public Date()
public Date(long date) //将数字变为Date类对象，long为日期时间数据
eg: Date date = new Date(System.currentTimeMillis());
public long getTime()  //将当期那日期时间变为long型
eg: Date date = new Date();
long num = date.getTime();


日期格式化操作类 SimpleDateformat
public SimpleDateFormat(String pattern)  //传入日期时间标记实例化对象
public final String format(Date date)   //将日期格式化为字符串数据
public Date parse(String source) throws ParserException  //将字符串格式化为日期数据
eg:
Date date = new Date();
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String str = sdf.format(date);   ----->   2015-04-28 11:13:54   //日期格式化为字符串


eg:
String str = "1991-08-20 12:12:12.122";
SimpleDateFormat sdf = new SimpleDateFormat("yy-MM-dd HH:mm:ss.SSS");
Date date = sdf.parse(str);   //将字符串格式化为日期数据


Random类
Random rand = new Random();  rand.nextInt(101);
大数字操作类：
java.math.BigInteger和java.math.BigDecimal 这两个类属于Number的子类
数组操作类： Arrays类
public static boolean equals(int[] a,int[] b)  //Arrays.equals(dataA,dataB)
public static void fill(int[] a,int val)   //Arrays.fill(dataA,3)
public static void sort(int[] a)   //Arrays.sort(dataA)
public static int binarySearch(int[] a,int key)    //Arrays.binarySearch(dataA,key)
public static String toString(int[] a)  //Arrays.toString(dataA)
普通的对象数组是不能够进行比较的，如果要比较需要比较器（Comparable接口就是一种比较器）的支持


比较器：
欲进行比较，需要实现Comparable接口
public class Person implements Comparable<Person>
重写compareTo方法
public int compareTo(Person o){
if(this.age > o.age){
return 1;
}else if(this.age < o.age){
return -1;
}else{
return 0;
}
}




对象克隆
实现Cloneable接口，但这个接口属于标识接口，只表示一种能力。
class Person implements Cloneable{  }
@Override
public String toString(){return "  ";}
@Override
protected Object clone() throws CloneNotSupportedException
 {return super.clone();}


正则表达式
所有正则匹配的符号都在  java.util.regex.Pattern  类中进行定义
//  \\ 匹配转移字符 "\"
\t匹配转移字符"\t"
^开始
$结束
.标识任意的一位字符
\d  表示任意的一位数字  \D非数字
\w 任意的一位字母,数字，下划线      \W非字母，数字，下划线
？0或1次
* 0次，1次或多次
+ 1次或多次
{n} 正好n次
{n,} n次以上
{n,m} 最少n次，最多m次
eg: 123.1   regex = "\\d+(\\.\\d+)"
String类对正则的支持
public boolean matches(String regex)   str.matches(regex)
public String replaceAll(String regex,String replacement)
public String[] split(String regex )
eg: String str = "a1bb2cc3dddd4eeeee5fff6eddd7oooo8iiiiii9";
String regex="\\d";
String result[] = str.split(regex);
public String[] split(String regex,int limit)


字符串---> 数字
int data = Integer.parseInt(str)
double data = Double.parseDouble(str)
表示任意一个用[],如[a-zA-z0-9\\.]，表示或者关系的用(xx|yy)
判断电话号码的regex      String regex = "((\\d{3,4}|\\(\\d{3,4}\\))-?)?\\d{7,8}";
简单的邮箱地址验证 String regex = "[a-zA-Z][a-zA-Z0-9]*@[a-zA-Z_0-9\\.]+\\.(com|cn|net)"


反射
Java IO操作
所有的IO操作都在java.io包中进行定义，而且整个java.io包实际上就是五个类和一个接口
五个类：File InputStrearm OutStream Reader Writer
一个接口： Serializable
public File(String pathname)  构造方法，给定一个要操作文件的完整路径
File file = new File("D:\\demo.txt");
file.exists()  file.createNewFile()  file.delete()
public boolean createNewFile() throws ArrayIndexOutOfBoundException
public boolean delete()
public boolean exists()
File file = new File("D:"+File.separator+"demo.txt");   File.separator是为了在不同平台运行


创建有目录的文件
public File getParentFile()  找到一个指定路径的父路径
public boolean mkdirs()   创建指定目录
File file = new File("D:"+File.separator+"hello"+File.separator+"test"+File.separator+"demo.txt");
if(!file.getParentFile().exists()){
file.getParentFile().mkdirs();            //若不存在父文件，则创建之
}
if(file.exists()){
file.delete();        //若文件不存在，创建之
}else{
file.createNewFile();
}


File的其他用法：
public String getName()   //取得文件名称
public boolean isDirectory()
public boolean isFile()  //file.isFile()
public boolean isHidden()
public long lastModified()   //文件的最后一次修改日期
public long length()  //取得文件大小，以字节为单位返回
public boolean renameTo(File dest)   //为文件重命名      file.renameTo(newName)
public File[] listFiles()   //将目录中所有文件以File对象数组的方式返回   File result[] = file.listFiles()
递归输出目录下的文件
public static void print(File file){
if(file.isDirectory()){
File result[] = file.listFiles();
if(result!=null){
for(int x = 0; x < result.length; x++)
print(result[x]);
}
}
System.out.println(file);
}


字节操作流  OutputStream  InputStream
字符操作流  Writer  Reader


FileOutputStream类的构造方法：
public FileOutputStream(FIle file) throws FileNotFoundException  实例化FileOutputStream，主要用于新建数据
public FileOutputStream(File file,boolean append) throws FileNotFoundException   主要用于追加数据
OutputStream定义的方法
pubic FileOutpubStream(File file)throws FileNotFoundExceptin
public void write(byte[] b) throws IOException
public void write(byte[] b,int off,int len) throws IOException
对于OutputStream而言，它本身定义的是一个抽象类，按照抽象类的使用原则，需要定义抽象类的子类，子类要为抽象类进行对象的实例化
而后调用的方法以父类为主，而具体的实现是实例化这个父类的子类完成的，关心的是子类的构造方法。


InputStream定义方法：
public abstract int read() throws IOException   //读取一个字节，到结尾返回 -1
public int read(byte[] b)throws IOException   //读取多个字节，返回的是读取的数据个数
public int read(byte[] b,int off,int len) throws IOException


如果希望对输出的内容增加换行显示，可以使用"\r\n"来表示
eg：把内容输出到指定文件
 File file = new File("D:"+File.separator+"test.txt");
OutputStream output = new FileOutputStream(file);
## ## OutputStream output = new FileOutputStream(file,true); true表示追加数据
String data = "hello world";
output.write(data.getBytes());
output.close();
eg:从指定文件读取内容：
 InputStream input = new FileInputStream(file);
byte[] data = new byte[1024];
int len = input.read(data);
input.close();
System.out.println("data is:"+new String(data,0,len));

字节流和字符流的区别
字节流在IO操作时，直接针对的是操作的数据端(如文件)，而字符流操作时不是直接针对于终端，而是针对于缓存区的操作，而后由缓存区
操作终端，属于间接操作。
字节流没有使用缓冲区，而字符流使用了缓冲区
处理各种数据都可以通过字节流完成，而在处理中文时使用字符流会更好


单个字节读取：
while((temp=input.read())!=-1){
data[foot++]=(byte) temp;
}
字符输出流 Writer类本身是一个抽象类，那么要使用它依然要依靠其子类，比如FileWriter类
public abstract void close() throws IOException
public void write(String str) throws IOException
public void wirte(char[] buf) throws IOException
public abstract void flush() throws IOException
eg:
Writer out = new FileWriter(file);
String data = "\r\nnice to meet you .\n";
out.write(data);        //直接把字符串输出到文件中
out.close();


字符输入流
public abstract void close() throws IOException
public int read() throws IOException
public int read(char[] cbuf) throws IOException
Reader in = new FileReader(file);
char data[] = new char[1024];
int len = in.read(data);
System.out.println("data is:" +new String(data,0,len));
in.close();


转换流
将字节输出流变为字符输出流 OutputSteam -> Writer   OutputStreamWriter
将字节输入流变为字符输入流 InputStream ->Reader   InputStreamReader
eg:
OutputStream output = new FileOutputStream(file);
Writer out = new OutputStreamWriter(output);
out.write("hello world");
out.close();


InputStream抽象类下面 有   FileInputStream 和 ByteArrayInputStream
OutputStream抽象类下面有   FileOutputStream 和 ByteArrayOutputStream


缓冲区操作 BufferedReader de 构造方法： public BufferedReader(Reader in)
读取操作  public String readLine() throws IOException


BufferedReader buf = new BufferedReader(new InputStreamReader(System.in));
String str = buf.readLine()      //利用BufferedReader进行循环判断
if(str.matches("\\d+")){


num = Integer.parseInt(str);

System.out.println(num*num);


}
Scanner 方法
pubic Scanner(InputStream source)
public boolean hasNext(Pattern pattern)  //判断输入的数据是否符合指定的正则标准
public boolean hasNext()  //判断有输入内容
public boolean hasNextxxx()  //判断输入的是否为指定的数据类型
public String next()          //接受内容
public String next(Pattern pattern)  //接受内容，进行正则验证
public int nextXxx()   //接受指定的输入类型
public Scanner useDelimiter(String pattern)      sn.useDelimiter("\n");    //设置读取的分隔符
由输入验证生日：
eg:
if(sn.hasNext("\\d{4}-\\d{2}-\\d{2}")){
String str = sn.next("\\d{4}-\\d{2}-\\d{2}");
Date date = new SimpleDateFormat("yyyy-MM-dd").parse(str);


对象序列化
对象序列指的是可以将内存中保存的对象数据（主要指的是一个对象里面所包含属性内容）进行二进制数据传输的一种操作，要想
实现这样的二进制操作，对象所在的类就必须实现java.io.Serializable 接口（这个接口和Cloneable接口一样，都属于一种标识接口
表示一种能力）可以使用transient关键字定义不被序列化的属性。


ServerSocket类的常用操作方法
public ServerSocket(int port) throws IOException
public Socket accept() throws IOException
public void close() throws IOException
Socket的常用操作方法
public Socket(String host,int port)
public OutputStream getOutputStream()
public InputStream getInputStream()
