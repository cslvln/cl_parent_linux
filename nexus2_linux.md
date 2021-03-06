# linux上怎么搭建nexus2

## 准备工作

系统：LINUX

JDK：已安装（未安装详见jdk安装教程：http://www.cnblogs.com/muzi1994/p/5818099.html）

Maven：已安装（未安装详见maven安装教程：http://www.cnblogs.com/muzi1994/p/6030181.html）

Nexus：http://www.sonatype.org/nexus/go

所有版本下载地址：http://www.sonatype.org/nexus/archived/

Nexus是一个强大的Maven仓库管理器，它极大地简化了自己内部仓库的维护和外部仓库的访问。

## 安装Nexus

解压nexus文件 tar -zvxf nexus-2.12.0-01-bundle.tar.gz

	注：解压后有两个文件夹：
		nexus-2.12.0-01： 是nexus的核心文件
		sonatype-work ：maven下载jar存放地址

启动Nexus  ./bin/nexus start

	默认情况下，不建议以root用户运行Nexus，可以修改bin/nexus中的配置跳过警告（修改RUN_AS_USER=root）
	vi bin/nexus  修改 RUN_AS_USER=root

重新启动Nexus  ./bin/nexus start

	注：Nexus默认端口8081，如果想修改端口。修改/conf/nexus.properties文件

访问网址：http://IP:8081/nexus/

点击右上角的 Log In 按钮即可登陆了。默认登录账号/密码为： admin/admin123 ，登陆成功后的界面，点击Repositories，将列表中所有Type为proxy 的项目的Configuration 中的 Download Remote Indexes 设置为True,将Releases仓库的Deployment Policy设置为 Allow ReDeploy

## settings.xml和pom.xml的配置

[参照不同版本Nexus的官方文档配置](https://help.sonatype.com/repomanager3/maven-repositories)

**setting.xml文件中做如下配置：**

	<?xml version="1.0" encoding="UTF-8"?>
	
	<!--
	Licensed to the Apache Software Foundation (ASF) under one
	or more contributor license agreements.  See the NOTICE file
	distributed with this work for additional information
	regarding copyright ownership.  The ASF licenses this file
	to you under the Apache License, Version 2.0 (the
	"License"); you may not use this file except in compliance
	with the License.  You may obtain a copy of the License at
	
	    http://www.apache.org/licenses/LICENSE-2.0
	
	Unless required by applicable law or agreed to in writing,
	software distributed under the License is distributed on an
	"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
	KIND, either express or implied.  See the License for the
	specific language governing permissions and limitations
	under the License.
	-->
	
	<!--
	 | This is the configuration file for Maven. It can be specified at two levels:
	 |
	 |  1. User Level. This settings.xml file provides configuration for a single user,
	 |                 and is normally provided in ${user.home}/.m2/settings.xml.
	 |
	 |                 NOTE: This location can be overridden with the CLI option:
	 |
	 |                 -s /path/to/user/settings.xml
	 |
	 |  2. Global Level. This settings.xml file provides configuration for all Maven
	 |                 users on a machine (assuming they're all using the same Maven
	 |                 installation). It's normally provided in
	 |                 ${maven.conf}/settings.xml.
	 |
	 |                 NOTE: This location can be overridden with the CLI option:
	 |
	 |                 -gs /path/to/global/settings.xml
	 |
	 | The sections in this sample file are intended to give you a running start at
	 | getting the most out of your Maven installation. Where appropriate, the default
	 | values (values used when the setting is not specified) are provided.
	 |
	 |-->
	<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
	          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
	  <!-- localRepository
	   | The path to the local repository maven will use to store artifacts.
	   |
	   | Default: ${user.home}/.m2/repository
	  <localRepository>/path/to/local/repo</localRepository>
	  -->
	
	  <!-- interactiveMode
	   | This will determine whether maven prompts you when it needs input. If set to false,
	   | maven will use a sensible default value, perhaps based on some other setting, for
	   | the parameter in question.
	   |
	   | Default: true
	  <interactiveMode>true</interactiveMode>
	  -->
	
	  <!-- offline
	   | Determines whether maven should attempt to connect to the network when executing a build.
	   | This will have an effect on artifact downloads, artifact deployment, and others.
	   |
	   | Default: false
	  <offline>false</offline>
	  -->
	
	  <!-- pluginGroups
	   | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
	   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
	   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
	   |-->
	  <pluginGroups>
	    <!-- pluginGroup
	     | Specifies a further group identifier to use for plugin lookup.
	    <pluginGroup>com.your.plugins</pluginGroup>
	    -->
	  </pluginGroups>
	
	  <!-- proxies
	   | This is a list of proxies which can be used on this machine to connect to the network.
	   | Unless otherwise specified (by system property or command-line switch), the first proxy
	   | specification in this list marked as active will be used.
	   |-->
	  <proxies>
	    <!-- proxy
	     | Specification for one proxy, to be used in connecting to the network.
	     |
	    <proxy>
	      <id>optional</id>
	      <active>true</active>
	      <protocol>http</protocol>
	      <username>proxyuser</username>
	      <password>proxypass</password>
	      <host>proxy.host.net</host>
	      <port>80</port>
	      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
	    </proxy>
	    -->
	  </proxies>
	
	  <!-- servers
	   | This is a list of authentication profiles, keyed by the server-id used within the system.
	   | Authentication profiles can be used whenever maven must make a connection to a remote server.
	   |-->
	  <servers>
	    <!-- server
	     | Specifies the authentication information to use when connecting to a particular server, identified by
	     | a unique name within the system (referred to by the 'id' attribute below).
	     |
	     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
	     |       used together.
	     |
	    <server>
	      <id>deploymentRepo</id>
	      <username>repouser</username>
	      <password>repopwd</password>
	    </server>
	    -->
	
	    <!-- Another sample, using keys to authenticate.
	    <server>
	      <id>siteServer</id>
	      <privateKey>/path/to/private/key</privateKey>
	      <passphrase>optional; leave empty if not used.</passphrase>
	    </server>
	    -->
	    <server>
	        <id>releases</id>
	        <username>admin</username>
	        <password>admin123</password>
	    </server>
	    <server>
	        <id>snapshots</id>
	        <username>admin</username>
	        <password>admin123</password>
	    </server>
	  </servers>
	
	  <!-- mirrors
	   | This is a list of mirrors to be used in downloading artifacts from remote repositories.
	   |
	   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
	   | However, this repository may have problems with heavy traffic at times, so people have mirrored
	   | it to several places.
	   |
	   | That repository definition will have a unique id, so we can create a mirror reference for that
	   | repository, to be used as an alternate download site. The mirror site will be the preferred
	   | server for that repository.
	   |-->
	  <mirrors>
	    <!-- mirror
	     | Specifies a repository mirror site to use instead of a given repository. The repository that
	     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
	     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
	     |
	    <mirror>
	      <id>mirrorId</id>
	      <mirrorOf>repositoryId</mirrorOf>
	      <name>Human Readable Name for this Mirror.</name>
	      <url>http://my.repository.com/repo/path</url>
	    </mirror>
	     -->
	    <mirror>
	      <!--This sends everything else to /public -->
	      <id>nexus</id>
	      <mirrorOf>*</mirrorOf>
	      <url>http://nexus.cslvln.com:8081/nexus/content/groups/public</url>
	    </mirror>
	  </mirrors>
	
	  <!-- profiles
	   | This is a list of profiles which can be activated in a variety of ways, and which can modify
	   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
	   | specific paths and repository locations which allow the build to work in the local environment.
	   |
	   | For example, if you have an integration testing plugin - like cactus - that needs to know where
	   | your Tomcat instance is installed, you can provide a variable here such that the variable is
	   | dereferenced during the build process to configure the cactus plugin.
	   |
	   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
	   | section of this document (settings.xml) - will be discussed later. Another way essentially
	   | relies on the detection of a system property, either matching a particular value for the property,
	   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
	   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
	   | Finally, the list of active profiles can be specified directly from the command line.
	   |
	   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
	   |       repositories, plugin repositories, and free-form properties to be used as configuration
	   |       variables for plugins in the POM.
	   |
	   |-->
	  <profiles>
	    <!-- profile
	     | Specifies a set of introductions to the build process, to be activated using one or more of the
	     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
	     | or the command line, profiles have to have an ID that is unique.
	     |
	     | An encouraged best practice for profile identification is to use a consistent naming convention
	     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
	     | This will make it more intuitive to understand what the set of introduced profiles is attempting
	     | to accomplish, particularly when you only have a list of profile id's for debug.
	     |
	     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
	    <profile>
	      <id>jdk-1.4</id>
	
	      <activation>
	        <jdk>1.4</jdk>
	      </activation>
	
	      <repositories>
	        <repository>
	          <id>jdk14</id>
	          <name>Repository for JDK 1.4 builds</name>
	          <url>http://www.myhost.com/maven/jdk14</url>
	          <layout>default</layout>
	          <snapshotPolicy>always</snapshotPolicy>
	        </repository>
	      </repositories>
	    </profile>
	    -->
	
	    <!--
	     | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
	     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
	     | might hypothetically look like:
	     |
	     | ...
	     | <plugin>
	     |   <groupId>org.myco.myplugins</groupId>
	     |   <artifactId>myplugin</artifactId>
	     |
	     |   <configuration>
	     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
	     |   </configuration>
	     | </plugin>
	     | ...
	     |
	     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
	     |       anything, you could just leave off the <value/> inside the activation-property.
	     |
	    <profile>
	      <id>env-dev</id>
	
	      <activation>
	        <property>
	          <name>target-env</name>
	          <value>dev</value>
	        </property>
	      </activation>
	
	      <properties>
	        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
	      </properties>
	    </profile>
	    -->
		<profile>
	      <id>nexus</id>
	      <!--Enable snapshots for the built in central repo to direct -->
	      <!--all requests to nexus via the mirror -->
	      <repositories>
	        <repository>
	          <id>central</id>
	          <url>http://central</url>
	          <releases><enabled>true</enabled></releases>
	          <snapshots><enabled>true</enabled></snapshots>
	        </repository>
	      </repositories>
	     <pluginRepositories>
	        <pluginRepository>
	          <id>central</id>
	          <url>http://central</url>
	          <releases><enabled>true</enabled></releases>
	          <snapshots><enabled>true</enabled></snapshots>
	        </pluginRepository>
	      </pluginRepositories>
	    </profile>
	  </profiles>
	
	  <!-- activeProfiles
	   | List of profiles that are active for all builds.
	   |
	  <activeProfiles>
	    <activeProfile>alwaysActiveProfile</activeProfile>
	    <activeProfile>anotherAlwaysActiveProfile</activeProfile>
	  </activeProfiles>
	  -->
	  <!-- 激活上面profile 与id相同 -->
	  <activeProfiles>
	    <!--make the profile active all the time -->
	    <activeProfile>nexus</activeProfile>
	  </activeProfiles>
	</settings>

**pom.xml文件中做如下配置：**

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://maven.apache.org/POM/4.0.0"
	         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	    <modelVersion>4.0.0</modelVersion>
	
	    <groupId>com.cslvln.pw</groupId>
	    <artifactId>cl-parent</artifactId>
	    <version>${project.release.version}</version>
	    <!-- jar:java工程 war;web工程 pom;非java和web工程（父工程一般用这个,但是不生成target目录）-->
	    <packaging>pom</packaging>
	
	    <properties>
	        <project.release.version>0.1-SNAPSHOT</project.release.version>
	        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	        <maven.compiler.source>1.8</maven.compiler.source>
	        <maven.compiler.target>1.8</maven.compiler.target>
	    </properties>
	
	    <profiles>
	        <profile>
	            <id>release</id>
	            <properties>
	                <project.release.version>0.1</project.release.version>
	            </properties>
	        </profile>
	    </profiles>
	
	    <distributionManagement>
	        <repository>
	            <id>releases</id>
	            <name>Releases</name>
	            <url>http://nexus.cslvln.com:8081/nexus/content/repositories/releases</url>
	        </repository>
	        <snapshotRepository>
	            <id>snapshots</id>
	            <name>Snapshot</name>
	            <url>http://nexus.cslvln.com:8081/nexus/content/repositories/snapshots</url>
	        </snapshotRepository>
	    </distributionManagement>
	
	    <build>
	        <plugins>
	            <!-- maven 是个自动化构建工具，如果我们不告诉它我们的代码要使用什么样的jdk版本编译的话，它就会用maven-compiler-plugin默认的jdk版本来进行编译处理，这样就容易出现版本不匹配的问题，以至于可能导致编译不通过的问题-->
	            <plugin>
	                <groupId>org.apache.maven.plugins</groupId>
	                <artifactId>maven-compiler-plugin</artifactId>
	                <configuration>
	                    <source>${maven.compiler.source}</source>
	                    <target>${maven.compiler.target}</target>
	                    <encoding>${project.build.sourceEncoding}</encoding>
	                </configuration>
	            </plugin>
	            <!-- maven-source-plugin插件打出的包包含远吗，引用包含远吗的包，在IDEA等ide中查看源码文件时可以更容易读些。要生成带source的jar,需要在pom.xml中包含下面的插件
	            然后执行mvn install,打包成功的话，就可以在target文件夹中找到带source的jar包通常命名为xxx-version-sources.jar
	            注意：如果src/main/java中没有源码，则不生成-->
	            <plugin>
	                <groupId>org.apache.maven.plugins</groupId>
	                <artifactId>maven-source-plugin</artifactId>
	                <executions>
	                    <execution>
	                        <id>attach-sources</id>
	                        <goals>
	                            <goal>jar</goal>
	                        </goals>
	                    </execution>
	                </executions>
	            </plugin>
	        </plugins>
	    </build>
	</project>

[参照网址](https://www.cnblogs.com/muzi1994/p/6026144.html)