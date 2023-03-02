---
title: Tars学习记录
description: 支持多语言的微服务框架Tars
slug: study-tars
date: 2021-09-02 16:00:00+0000
image: tars.png
math: true
comments: false
categories:
    - 学习
tags:
    - Tars
    - 微服务
    - java
---

# Tars学习记录
> 参考官方文档 https://doc.tarsyun.com/#/default-index 
如果本文内容与官方文档出现偏差以官方文档为准

## SpringBoot 客户端 调用方法
### 1.引入*.tars文件
tar文件放于resources文件夹下面
```
module graph
{
    interface Person{
        string findPersonById(string id);
        string findShortPathByNumber(string type,string number1,string number2);
        string getLinkByName(string number,string type);
    };
};
```
### 2.修改pom.xml文件
pom文件中添加依赖与打包插件
```
        <!--tars-->
        <dependency>
            <groupId>com.tencent.tars</groupId>
            <artifactId>tars-client</artifactId>
            <version>1.7.2</version>
            <type>jar</type>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```
打包插件
```
					<plugin>
                <groupId>com.tencent.tars</groupId>
                <artifactId>tars-maven-plugin</artifactId>
                <version>1.7.2</version>
                <configuration>
                    <tars2JavaConfig>
                        <!-- tars文件位置 -->
                        <tarsFiles>
                            <tarsFile>${basedir}/src/main/resources/sda.tars</tarsFile>
                        </tarsFiles>
                        <!-- 源文件编码 -->
                        <tarsFileCharset>UTF-8</tarsFileCharset>
                        <!-- 生成代码，PS：客户端调用，这里需要设置为false -->
                        <servant>false</servant>
                        <!-- 生成源代码编码 -->
                        <charset>UTF-8</charset>
                        <!-- 生成的源代码目录 -->
                        <srcPath>${basedir}/src/main/java/</srcPath>
                        <!-- 生成源代码包前缀 -->
                        <packagePrefixName>cn.edu.seu.matrix.tars</packagePrefixName>
                    </tars2JavaConfig>
                </configuration>
            </plugin>
```
### 3.执行mvn tars:tars2java 生成文件
mvn执行后在定义的目录里生成文件
### 4.调用
在@service或者@Controller类中添加
```
    private CommunicatorConfig cfg = new CommunicatorConfig();
    private Communicator communicator = CommunicatorFactory.getInstance().getCommunicator(cfg);
    private String ip_tars="SERVER_IP"; // 在tars上运行的服务用 173 的，其余用机器地址
    private final String tars_app_name="SERVER_NAME"; // 举例 SciDataAnalysis.SocialNetwork
```
然后生成代理，调用方法
```
PersonPrx proxy = communicator.stringToProxy(PersonPrx.class, tars_app_name + ".PersonObj@tcp -h " + ip_tars + " -t 60000 -p 27430 -e 0");
proxy.findPersonById(number)
```
## 服务端 提供服务方法
### 1. 写tars文件
tars文件的编写请参考官方文档
> 参考官方文档 https://doc.tarsyun.com/#/base/tars-protocol.md
如果本文内容与官方文档出现偏差以官方文档为准
### 2.修改pom.xml文件
pom文件中添加依赖与打包插件
```
        <dependency>
            <groupId>com.tencent.tars</groupId>
            <artifactId>tars-spring-boot-starter</artifactId>
            <version>1.7.2</version>
            <type>jar</type>
        </dependency>
```
打包插件
```
					            <plugin>
                <groupId>com.tencent.tars</groupId>
                <artifactId>tars-maven-plugin</artifactId>
                <version>1.7.2</version>
                <configuration>
                    <tars2JavaConfig>
                        <!-- tars文件位置 -->
                        <tarsFiles>
                            <tarsFile>${basedir}/src/main/resources/socialNetwork.tars</tarsFile>
                        </tarsFiles>
                        <!-- 源文件编码 -->
                        <tarsFileCharset>UTF-8</tarsFileCharset>
                        <!-- 生成服务端代码 -->
                        <servant>true</servant>
                        <!-- 生成源代码编码 -->
                        <charset>UTF-8</charset>
                        <!-- 生成的源代码目录 -->
                        <srcPath>${basedir}/src/main/java</srcPath>
                        <!-- 生成源代码包前缀 -->
                        <packagePrefixName>cn.edu.seu.socialNetwork</packagePrefixName>
                    </tars2JavaConfig>
                </configuration>
            </plugin>
```
### 3.执行mvn tars:tars2java 生成文件
mvn执行后在定义的目录里生成文件,其中Servant接口类是自动生成的
### 4.实现
将Servant接口类实现一下
其中PersonObj与页面上的定义一致
```
@TarsServant("PersonObj")
public class PersonObj implements PersonServant {
}
```
类中需要@Override所有的 方法
### 5.定义服务
### 6.打包上传
## 基于Docker容器的TARS JAVA环境搭建

### 安装前准备

本教程需要预先安装**Docker**环境，所使用的IDE为**IntelliJ IDEA**。所有安装都基于Windows环境下。文中涉及到的目录均以 *C:/User/xxx/* 写法表示 

#### 获取Docker镜像
拉取最新的TARS容器（Java版）：

```
docker pull tarscloud/tars:java
```

如果需要不同JDK版本的容器，可以基于Dockerfile来修改和构建自己的容器镜像（install.sh内容也要做相应修改）。由于该镜像未安装mysql，故还需要安装mysql镜像，以5.7版本为例。

```
docker pull mysql:5.7

```

### 快速上手例程

本教程使用代码仓库中的
quickstart-server 和 quickerstart-client
 例程作为搭建代码，可以先下载到本地使用IDEA打开。 

### 开发方式

使用docker镜像进行Tars相关的开发非常方便，例如可以把项目放置在某个本地目录下，如

***C:/Users/xxx/tars_data***

 ，再将该目录挂载到镜像的 

***/data***

 目录，这样就能在本地使用编辑器或IDE对项目文件进行开发。
如果需要进入Tars环境进行编译或测试，可以使用命令 

***docker exec***

 进入容器内部查看。 

### 服务端配置

#### 启动容器

首先执行下面的命令来启动TARS（Windows命令行不支持 \ 命令换行），注意需要先在 *C:/User/xxx/* 目录下新建目录 *mysql_data* 和 *tars_data* 。

```
docker run -d --name mysql \
        -e MYSQL_ROOT_PASSWORD=password \
        -p 3306:3306 \
        -v C:/User/xxx/mysql_data:/var/lib/mysql \
        mysql:5.7 --innodb_use_native_aio=0

docker run -d -it --name tars_java \
        --link mysql \
        --env DBIP=mysql \
        --env DBPort=3306 \
        --env DBUser=root \
        --env DBPassword=password \
        -p 3000:3000 \
        -p 18600:18600 \
        -v C:/Users/xxx/tars_data:/data \
        tarscloud/tars:java


```

待容器启动完毕，此时可以在本地访问

[*http://127.0.0.1:3000*](http://127.0.0.1:3000)

 查看TARS的web管理界面了。
 

*注：如果你使用的mysql不是5.6的版本，可能需要调整docker run的参数*


## Java 本地单元测试方法
> 参考 https://tarscloud.github.io/TarsDocs/dev/tarsjava/tarsspringboot-shi-yong-shuo-ming.html
{.is-info}

- 拷贝node生成的模板文件到本地（在服务器 tasnode/data/服务名/conf 目录下）
- 修改其中每个servant的启动ip和端口文本地ip端口 配置启动参数
- -Dconfig=(模板路径) 通过ide启动MainClass 