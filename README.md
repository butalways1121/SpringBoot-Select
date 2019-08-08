# SpringBoot-Select
SpringBoot实现的查询数据库操作
（[点击这里](https://butalways1121.github.io/2019/08/06/Spring-Boot%E5%88%9B%E5%BB%BA%E6%8E%A5%E5%8F%A3%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E6%9F%A5%E8%AF%A2%E6%93%8D%E4%BD%9C/)跳转到页面查看）
## 工具及环境
eclipse j2ee 2019、apache-maven-3.6.1、jdk1.8、Navicat for mysql 12、postman。
## 配置Maven环境
下载apache-maven-3.6.1压缩包，解压到某个文件夹，右键计算机-属性-高级系统设置-环境变量中新建MAVEN_HOME，设置值为maven解压的目录路径，编辑Path变量，新增%MAVEN_HOME%\bin。打开cmd.exe，输入命令mvn –version检测是否安装成功，出现Maven版本号字样则表示成功。
<!-- more -->
## 创建数据库
在Navicat中的test数据库新建test表并设计如下，其中id属性选择为自增：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/1.png)

并添加数据如下：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/2.png)
## 创建项目
Eclipse中创建Maven Project，输入相应的包名及项目名，这里的项目名是Select，包名为com,创建好的项目结构如下:
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/3.png)

接着使用如下代码替换pom.xml里面的内容:
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>1.0.0</groupId>
	<artifactId>dms-get-ecall</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>springboot-restful</name>
	<url>http://maven.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.7</java.version>
		<mybatis-spring-boot>1.2.0</mybatis-spring-boot>
	</properties>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath />
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<!-- Spring Boot Mybatis 依赖 -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>${mybatis-spring-boot}</version>
		</dependency>
		<!-- MySQL 连接驱动依赖 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<fork>true</fork>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

在src/main下新建resources文件夹，新建application.properties文件，将如下内容复制：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/4.png)
```
banner.charset=UTF-8
server.tomcat.uri-encoding=UTF-8
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
spring.messages.encoding=UTF-8

server.port=8083
spring.datasource.url=jdbc:mysql://数据库地址:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

将com.Select下的App.java代码更新如下：
```
package com.Select;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Hello world!
 *
 */
@SpringBootApplication
public class App 
{
    public static void main( String[] args )
    {
    	SpringApplication.run(App.class, args);
        System.out.println( "Hello World!" );
    }
}

```
## 创建项目
### entity层
完成之后右键项目，选择Maven/Update Projects更新项目。
接下来在src/main/java下新建entity包，创建Info实体类，该实体类封装的变量对应test数据库表中的属性，代码如下:
```
package com.Select.entity;

public class Info {
	private int id;
	private String name;
	private String sex;
	private String birthday;
	private String address;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public String getBirthday() {
		return birthday;
	}
	public void setBirthday(String birthday) {
		this.birthday = birthday;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	
}
```
继续在entity包下创建InfoResult类，作用是将Info类的参数封装成列表形式，代码如下：
```
package com.Select.entity;

import java.util.ArrayList;

public class InfoResult {
	private ArrayList<Info> info;

	public ArrayList<Info> getInfo() {
		return info;
	}

	public void setInfo(ArrayList<Info> info) {
		this.info = info;
	}
}
```
### dao层
在src/main/java下新建dao包,新建InfoDao接口，实现对数据库的查询操作：
```
package com.Select.dao;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import com.Select.entity.Info;

@Mapper
public interface InfoDao {
	@Select("select\t\n"+
			"id,\t\n"+
			"name,\t\n"+
			"sex,\t\n"+
			"birthday,\t\n"+
			"address\t\n"+
			"from\t\n"+
			"test\t\n"
			)
	List<Info> selectInfo();
}

```
### service层
在src/main/java下新建service包，新建InfoService接口，添加要用到的方法：
```
package com.Select.service;

import com.Select.entity.InfoResult;

public interface InfoService {
	InfoResult selectInfoResult();
}
```
在src/main/java下新建serviceimpl包,新建InfoServiceImpl类，重写InfoService中声明的方法：
```
package com.Select.serviceimpl;


import java.util.ArrayList;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.Select.dao.InfoDao;
import com.Select.entity.Info;
import com.Select.entity.InfoResult;
import com.Select.service.InfoService;

@Service
public class InfoServiceImpl implements InfoService{
	@Autowired
	private InfoDao infoDao;

	@Override
	public InfoResult selectInfoResult() {
		InfoResult infoResult = new InfoResult();
		infoResult.setInfo((ArrayList<Info>) infoDao.selectInfo());
		return infoResult;
	}
}
```
### controller层
在src/main/java下新建controller包，新建InfoController类来处理请求返回数据:
```
package com.Select.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;


import com.Select.entity.InfoResult;
import com.Select.service.InfoService;

@RestController
@RequestMapping(value="/api")
public class InfoController {
	@Autowired
	private InfoService infoService;
	
	@PostMapping("/select")
	InfoResult selInfoResult() {
		return infoService.selectInfoResult();
	}
}
```
***
至此，整个项目已搭建完毕，整体的框架如下：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/5.png)
然后就可以运行程序了：右键App.java，选择运行方式-Java应用程序，如果不出意外控制台会出现Hello World！，然后打开postman设置如下信息：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/6.png)
点击send之后就可以成功获取到test数据库表的所有数据：
![](https://raw.githubusercontent.com/butalways1121/img-SpringBoot/master/7.png)
***
## OVER！
