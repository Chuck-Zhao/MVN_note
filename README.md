# MVN_note

相关网站
http://search.maven.org/
http://www.mvnrepository.com/

maven介绍及环境搭建
介绍：
	基于项目对象模型，可以通过一小段描述信息来管理项目的构建、报告和文档的软件项目管理工具
下载 maven.appache.org
	download maven 3.3.3 jdk 1.7的支持
目录结构：
	bin     包含mvn脚本 m2.conf配置文件
	boot	类加载器的框架 使用这个jar加载类库
	config 	配置文件	settings.xml经常用到
	lib		常用的类库除了自己的还有第三方以来的类库
配置环境变量：
	maven_home	maven目录
	path		maven的bin目录
	命令行中 mvn -v 能够查看到maven、java、系统版本号就说明安装成功了
hello maven的小例子
	目录介绍
		src
			-main
				-java
					-package
			-test
				-java
					-package
			resources
	pom.xml配置文件
	mvn test	
		生成target文件夹
			classes文件夹存放字节码文件
			reports存放生成的测试报告
	mvn package 
		target中生成一个jar文件
2017年9月24日22点39分
maven的基础知识
	常用的构建命令
		mvn -v	查看maven版本
			compaile 编译
			test 测试
			package 打包
			clean 删除target中的字节码文件和测试报告
			install	安装jar到本地仓库
	构建过程：先编译，如果过程中程序有依赖就去pom.xml中查找是否引入坐标,会去本地仓库当中查找
		如果有就将jar加入到项目的classpath中如果没有就回去网上的maven中央仓库中下载
		放到仓库中供我们的项目使用
自动创建目录骨架
	archetype插件用于创建符合maven规范的目录骨架
		src java中主代码
	创建目录的2中方式：
	1.	mvn archetype:generate	按照提示进行选择
	2.	archetype:generate 	 组织名，公司网址的反写+项目名
							-DartifaceId=项目名-模块名
							-Dcersion-版本号
							-Dpackage=代码所存在的包名
	坐标
		构建
	仓库
		本地仓库和远程仓库
		lib/maven-model-builder的jar
			其中org/apache/maven/model中pom-4.0.0xml
			<repository>
			<id>central</id>
			<name>Central Repository</name>
			<url>https://repo.maven.apache.org/maven2</url>默认的中央仓库的地址
			<layout>default</layout>默认的布置
			<snapshots>
			<enabled>false</enabled>禁止下载快照的版本构建
			</snapshots>
			</repository>
		中央仓库地址http://search.maven.org/
	镜像仓库
		conf	settings
		mirror 
		<mirror>
			<id>maven.net.cn</id>
			<mirrorOf>central</mirrorOf> 可以使用* 
	对原仓库的访问都指向镜像仓库，原仓库不能直接访问
			<name>central mirror in China</name>
			<url>http://maven.net.cn/content/groups/public</url>
		</mirror>
	本地仓库
		修改settings
		<localRepository>一般不放c盘，防止重装系统丢失
		小技巧：将settings复制一份到repo中，可以在maven升级后
		不用重新设置maven
2017年10月3日15点07分
	在eclipse中安装插件以及创建
		在eclipse4.0以上不用另外安装maven插件
		maven插件将其复制到eclipse/dropins中重启eclipse
		1.在eclipse.ini中加入-vm java_home\bin\javaw.exe
		2.修改eclipse的jre 在java installed jres 中改成自己的jre
		（两者区别？？）
		3.更改maven配置 installations改成本机的maven
			user settings改成repo中 的settings
		选择quick-start
		创建了一个maven项目
			run as mavenbuilder->compile 出现错误
			在java的jre中添加-Dmaven.multiModuleProjectDirectory=$m2_home(maven的目录)
			run as mavenbuilder->compile编译成功
								package打包成功
2017年10月3日20点30分 
	完整的项目构建过程包括
		清理、编译、测试、打包、集成测试、验证、部署
	maven生命周期
		clean	清理项目
		default	构建项目
		site	生成项目站点
	clean、compile、test、package、install
	clean清理项目
		pre-clean 执行清理前的工作
		clean清理上一次构建生成的所有文件
		post-clean 执行清理后的文件
	default构建项目（最核心）
		compile test package install
	site生成项目站点
		pre-site	在生成项目站点前要完成的工作
		site		生成项目的站点文件
		post-site 	在生成项目站点后要完成的工作
		site-deploy	发布生成的站点到服务器上
	pom.xml中添加了
			<build>
				<plugins>
					<plugin>
						<groupId>org.apache.maven.plugins</groupId>
						<artifactId>maven-source-plugin</artifactId>
						<version>2.4</version>
						<executions>
							<execution>
								<phase>package</phase>
								<goals>
									<goal>jar-no-fork</goal>
								</goals>
							</execution>
						</executions>
					</plugin>
				</plugins>
			</build>
	然后run as clean package 项目就执行了编译 测试 打包 并且生成了source
	执行某个阶段时，其前面的阶段会依次执行
pom.xml
	详解见pom.xml文件
依赖范围
	scope	三种classpath 编译测试运行
		compile 默认的范围，编译测试运行都有效
		provided 在编译和测试时有效
		runtime	在测试和运行时有效
		test 	只在测试时有效
		system	与本机系统相关联，可移植性差
		import		导入的范围，它只使用在dependencyManagement中，
					表示从其他的pom中导入dependecy的配置
依赖冲突
	依赖不同版本的相同构建
	1.短路优先
		A->B->C->X(jar)
		A->D->X(jar)
	2.先声明先优先
		如果路径长度相同，则谁先声明，先解析谁
聚合和继承
	聚合：
	1.packaging设置为pom
	2.删除了junit依赖
	3.modules设置多个模块../模块名
	4.run as clean install
	继承：
	1.创建一个父类，packaging设置为pom，可以删除main和test目录
	2.	将公共依赖放入<dependencyManagement>中，
		可以提取版本号到properties中利用${}进行获取
	3.子类依赖只写groupid和artifactid就可以
一个完整的web项目：	
	1.jsp报错 添加servlet api 在www.mvnrepository.com查找servletapi选3.0版本
	并将junit版本设置为4.10,
	2.在build中添加插件jetty并将可以写executions	
			可设置打包成功后	使用jetty run运行jetty服务
			localhost：8080启动
	1.添加tomcat服务器在tomcat官网上maven插件
			localhost:8080/项目名
	myeclipse中的Deployment Assembly中将test去掉项目发布后不需要测试代码
	project facts中将dynamic web module勾选上
build path中source的各个目录都指向target/class
