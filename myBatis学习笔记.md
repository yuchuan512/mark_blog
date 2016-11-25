title: myBatis学习笔记
date: 2016-06-09 22:40:46
tags: [myBatis]
---
### mybatis 接触
configure.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias alias="User" type="com.yihaomen.mybatis.model.User"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
        <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://127.0.0.1:3306/test" />
            <property name="username" value="root"/>
            <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="com/yihaomen/mybatis/model/User.xml"/>
    </mappers>
</configuration>

user.xml

<mapper namespace="com.yihaomen.mybatis.models.UserMapper">
    <select id="selectUserByID" parameterType="int" resultType="User">
        select * from `user` where id = #{id}
    </select>
</mapper>


public class Test {
	private static SqlSessionFactory sqlSessionFactory;
	private static Reader reader;

	static {
		try {
			reader = Resources.getResourceAsReader("Configuration.xml");
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public static void main(String[] args) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			User user = (User) session.selectOne(
					"com.yihaomen.mybatis.models.UserMapper.selectUserByID", 1);
			System.out.println(user.getUserAddress());
			System.out.println(user.getUserName());
		} finally {
			session.close();
		}
	}
}

### 使用接口实现
user.xml ,改变namespace的值，指向接口
<mapper namespace="com.yihaomen.mybatis.inter.IUserOperation">
    <select id="selectUserByID" parameterType="int" resultType="User">
        select * from `user` where id = #{id}
    </select>
</mapper>

public static void main(String[] args) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session.getMapper(IUserOperation.class);
			User user = userOperation.selectUserByID(1);
			System.out.println(user.getUserAddress());
			System.out.println(user.getUserName());
		} finally {
			session.close();
		}
	}

### mybatis实现数据的增删改查
一、 查找
User.xml 配置
<mapper namespace="com.yihaomen.mybatis.inter.IUserOperation">
    <select id="selectUserByID" parameterType="int" resultType="User">
        select * from `user` where id = #{id}
    </select>
    <resultMap type="User" id="resultListUser">
        <id column="id" property="id" />
        <result column="userName" property="userName" />
        <result column="userAge" property="userAge" />
        <result column="userAddress" property="userAddress" />
    </resultMap>
    <select id="selectUsers" parameterType="string" resultMap="resultListUser">
        select * from user where userName like #{userName}
    </select>
</mapper>

接口增加方法
public interface IUserOperation {
	public User selectUserByID(int id);
	public List<User> selectUsers(String userName);
}

测试类
	public static void main(String[] args) {
		Test testUser = new Test();
		testUser.getUserList("%");
	}

	public void getUserList(String userName){
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session
					.getMapper(IUserOperation.class);
			List<User> users = userOperation.selectUsers(userName);
			for (User user : users) {
				System.out.println(user.getId() + ":" + user.getUserName()
						+ ":" + user.getUserAddress());
			}
		} finally {
			session.close();
		}
	}

二、 增加
user.xml
<insert id="addUser" parameterType="User"
        useGeneratedKeys="true" keyProperty="id">
        insert into user(userName,userAge,userAddress)
             values(#{userName},#{userAge},#{userAddress})
</insert>

public interface IUserOperation {
	public User selectUserByID(int id);
	public List<User> selectUsers(String userName);
	public void addUser(User user);
}

public static void main(String[] args) {
		User user = new User();
		user.setUserAddress("人民广场");
		user.setUserName("飞鸟");
		user.setUserAge(20);
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session.getMapper(IUserOperation.class);
			userOperation.addUser(user);
			session.commit();
			System.out.println("id : "+user.getId());
		} catch (Exception e) {
			// TODO: handle exception
		}finally{
			session.close();
		}
	}

三、更新数据
<update id="updateUser" parameterType="User" >
        update user set userName=#{userName},userAge=#{userAge},userAddress=#{userAddress} where id=#{id}
</update>

public interface IUserOperation {
	public User selectUserByID(int id);
	public List<User> selectUsers(String userName);
	public void addUser(User user);
	public void updateUser(User user);
}

public static void main(String[] args) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session.getMapper(IUserOperation.class);
			User user = userOperation.selectUserByID(4);
			user.setUserAddress("hell");
			userOperation.updateUser(user);
			session.commit();
		} catch (Exception e) {
			// TODO: handle exception
		}finally{
			session.close();
		}
	}

四、删除数据
 <delete id="deleteUser" parameterType="int">
    	delete from user where id=#{id}
 </delete>

 public interface IUserOperation {
	public User selectUserByID(int id);
	public List<User> selectUsers(String userName);
	public void addUser(User user);
	public void updateUser(User user);
	public void deleteUser(int id);
}

public static void main(String[] args) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session.getMapper(IUserOperation.class);
			userOperation.deleteUser(4);
			session.commit();
		} catch (Exception e) {
			// TODO: handle exception
		}finally{
			session.close();
		}
	}
五、表关联
	别名：
	<typeAliases>
		<typeAlias alias="Article" type="com.yihaomen.mybatis.model.Article"/>
	</typeAliases>

	<resultMap id="resultUserArticleList" type="com.yihaomen.mybatis.model.Article">  // 写全类名，否则提示找不到类
    	<id property="id" column="aid"/>
    	<result property="title" column="title"/>
    	<result property="content" column="content"/>

    	<association property="user" javaType="User">
    		<id property="id" column="id"/>
    		<result property="userName" column="userName"/>
    		<result property="userAddress" column="userAddress"/>
    	</association>
    </resultMap>

    <select id="getUserArticles" parameterType="int" resultMap="resultUserArticleList">
       select user.id,user.userName,user.userAddress,article.id aid,article.title,article.content from user,article
              where user.id=article.userid and user.id=#{id}
    </select>

public class Article {
	private int id;
    private User user;
    private String title;
    private String content;
}

public interface IUserOperation {
	public User selectUserByID(int id);
	public List<User> selectUsers(String userName);
	public void addUser(User user);
	public void updateUser(User user);
	public void deleteUser(int id);
	public List<Article> getUserArticles(int id);
}

public void getUserArticles(int userid){
		SqlSession session = sqlSessionFactory.openSession();
		try {
			IUserOperation userOperation = session.getMapper(IUserOperation.class);
			List<Article> articles = userOperation.getUserArticles(userid);
			for (Article article : articles) {
				System.out.println(article.getTitle()+":"+article.getContent()+":"+article.getUser().getUserName()+":"
						+article.getUser().getUserAddress());
			}
		} catch (Exception e) {
		} finally{
			session.close();
		}
	}


---------------------------------------
mybaits实例教程
jar包
1. mybatis-3.1.1.jar
2. mysql-connector-java-5.1.7-bin.jar

sql语句
```
CREATE DATABASE mybatis;
USE mybatis;
CREATE TABLE users(id INT PRIMARY KEY AUTO_INCREMENT, NAME VARCHAR(20), age INT);
INSERT INTO users(NAME, age) VALUES('孤傲苍狼', 27);
INSERT INTO users(NAME, age) VALUES('白虎神皇', 27);
```

conf.xml 文件
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <!-- 配置数据库连接信息 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 注册userMapper.xml文件 -->
        <mapper resource="userMapper.xml"/>
    </mappers>
</configuration>
```

userMapper.xml 文件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 为这个mapper指定一个唯一的namespace，namespace的值习惯上设置成包名+sql映射文件名，这样就能够保证namespace的值是唯一的
例如namespace="me.gacl.mapping.userMapper"就是me.gacl.mapping(包名)+userMapper(userMapper.xml文件去除后缀)
 -->
<mapper namespace="me.gacl.mapping.userMapper">
    <!-- 在select标签中编写查询的SQL语句， 设置select标签的id属性为getUser，id属性值必须是唯一的，不能够重复
    使用parameterType属性指明查询时使用的参数类型，resultType属性指明查询返回的结果集类型
    resultType="me.gacl.domain.User"就表示将查询结果封装成一个User类的对象返回
    User类就是users表所对应的实体类
    -->
    <!--
        根据id查询得到一个user对象
     -->
    <select id="getUser" parameterType="int"
            resultType="com.ali.User">
        select * from users where id=#{id}
    </select>
</mapper>
```

User.java
```
public class User {
    //实体类的属性和表的字段名称一一对应
    private int id;
    private String name;
    private int age;
}
```

Test测试
```
package com.ali;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class Test {
    public static void main(String[] args) {
        String resource = "conf.xml";
        //使用类加载器加载mybatis的配置文件（它也加载关联的映射文件）
        InputStream is = Test.class.getClassLoader().getResourceAsStream(resource);
        //构建sqlSession的工厂
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(is);
        //使用MyBatis提供的Resources类加载mybatis的配置文件（它也加载关联的映射文件）
        //Reader reader = Resources.getResourceAsReader(resource);
        //构建sqlSession的工厂
        //SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(reader);
        //创建能执行映射文件中sql的sqlSession
        SqlSession session = sessionFactory.openSession();
        /**
         * 映射sql的标识字符串，
         * me.gacl.mapping.userMapper是userMapper.xml文件中mapper标签的namespace属性的值，
         * getUser是select标签的id属性值，通过select标签的id属性值就可以找到要执行的SQL
         */
        String statement = "me.gacl.mapping.userMapper.getUser";//映射sql的标识字符串
        //执行查询返回一个唯一user对象的sql
        User user = session.selectOne(statement, 1);
        System.out.println(user);

    }
}

```
> 可以指定别名,这样就可以用别名访问该POJO了
```
<typeAliases>
        <typeAlias alias="User" type="com.ali.User"></typeAlias>
</typeAliases>
```

改为接口方式编程，需要修改如下部分
增加IUserDAO
```
public interface IUserDao {
    public User getUser(int id);
}
```

测试代码中
```
IUserDao dao = session.getMapper(IUserDao.class);
        User user = dao.getUser(1);
```
userMapper.xml中　
```
<mapper namespace="com.ali.IUserDao">
	<select id="getUser" parameterType="int"
            resultType="User">
        select * from users where id=#{id}
    </select>
</mapper>
```
maven学习: http://www.yiibai.com/maven/





