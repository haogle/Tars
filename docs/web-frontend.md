# 2. <a id="chapter-2"></a>Tars开发环境安装介绍
## 2.1. web管理系统开发环境安装
以linux环境为例：

下载java jdk，解压安装

配置环境环境
```
vim /etc/profile
```
添加如下内容：
```
export JAVA_HOME=${jdk source dir}
CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME CLASSPATH
```
执行命令
```
source /etc/profile
```
测试
```
java -version
```

maven安装
下载maven，解压安装
配置环境变量
```
vim /etc/profile
```
添加如下内容：
```
export MAVEN_HOME=${maven source dir}
export PATH=$PATH:$MAVEN_HOME/bin
```
执行命令
```
source /etc/profile
```
测试
```
mvn -v
```
**注意，如有需要，修改本地仓库的位置和maven的源**

## 2.2. java语言框架开发环境安装

java jdk和maven环境配置与上面web管理系统的类似

**注意，java框架安装前，需要保证机器能联网，同时修改maven安装目录下的conf目录的setting.xml，尽量使用国内的maven镜像，具体操作可以网上查看**

下载tars源码，进入java源码目录，并安装在本地仓库
```
mvn clean install 
mvn clean install -f core/client.pom.xml 
mvn clean install -f core/server.pom.xml

```
构建web工程项目
通过IDE或者maven创建一个maven web项目，
这里以eclipse为例，File -> New -> Project -> Maven Project -> maven-archetype-webapp，再输入groupId、artifactId，生成完成之后可以通过eclipse进行导入，目录结构如下

![tars](docs/images/tars_java_evn1.png)


增加Maven依赖配置
使用Tars框架时，需要依赖框架提供的jar包依赖，以及工具插件。在工程项目pom.xml文件中增加如下依赖配置。
框架依赖配置

![tars](docs/images/tars_java_evn2.png)

插件依赖配置

![tars](docs/images/tars_java_evn3.png)

## 2.3. c++ 开发环境安装
下载tars源码，首先进入cpp/thirdparty目录，执行thirdparty.sh脚本，下载依赖的rapidjson

然后进入cpp/build源码目录
```
cd {$source_folder}/cpp/build
chmod u+x build.sh
./build.sh all
```

**编译时默认使用的mysql开发库路径：include的路径为/usr/local/mysql/include，lib的路径为/usr/local/mysql/lib/，若mysql开发库的安装路径不在默认路径，则需要修改build目录下CMakeLists.txt文件中的mysql相关的路径，再编译**

如果需要重新编译
```
./build.sh cleanall
./build.sh all
```

切换至root用户，创建安装目录
```
cd /usr/local
mkdir tars
chown ${普通用户}:${普通用户} ./tars/
```

安装
```
cd {$source_folder}/cpp/build
./build.sh install或者make install
```
**默认的安装路径为/usr/local/tars/cpp。**

**如要修改安装路径：**
```
**需要修改build目录下CMakeLists.txt文件中的安装路径。**
**需要修改servant/makefile/makefile.tars文件中的TARS_PATH的路径**
**需要修改servant/script/create_tars_server.sh文件中的DEMO_PATH的路径**
```
