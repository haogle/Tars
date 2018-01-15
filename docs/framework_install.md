# 4. <a id="chapter-4"></a>Tars框架运行环境搭建

## 4.1. 框架基础服务打包

框架服务的安装分两种：

一种是核心基础服务(必须的)，必须手工部署的，

一种是普通基础服务(可选的)，可以通过管理平台发布的(和普通服务一样）。

```
手工部署的核心基础服务：tarsAdminRegistry, tarsregistry, tarsnode, tarsconfig, tarspatch
 
通过管理平台部署的普通基础服务：tarsstat, tarsproperty,tarsnotify, tarslog，tarsquerystat，tarsqueryproperty
```

首先准备第一种服务的安装包，在cpp/build/目录下输入：
```
make framework-tar
```
会在当前目录生成framework.tgz 包
这个包包含了 tarsAdminRegistry, tarsregistry, tarsnode, tarsconfig, tarspatch 部署相关的文件

第二种服务安装包可以单独准备：
```
make tarsstat-tar
make tarsnotify-tar
make tarsproperty-tar
make tarslog-tar
make tarsquerystat-tar
make tarsqueryproperty-tar
```
生成的发布包，在管理平台部署发布完成后，进行部署发布，具体参见4.4章节。

**注意在管理平台进行部署时，选择正确的服务模板即可（默认是有的，若没有相应的模版，可以在管理平台上创建，具体服务的模版内容可以tars_template.md）!**

## 4.2. 安装框架核心基础服务

## 4.2.1. 安装核心基础服务

切换至root用户，创建基础服务的部署目录，如下：
```  shell
cd /usr/local/app
mkdir tars
chown ${普通用户}:${普通用户} ./tars/
```

将已打好的框架服务包复制到/usr/local/app/tars/，然后解压，如下：
``` shell
cp build/framework.tgz /usr/local/app/tars/
cd /usr/local/app/tars
tar xzfv framework.tgz
```

修改各个服务对应conf目录下配置文件，注意将配置文件中的ip地址修改为本机ip地址，如下：
``` shell
cd /usr/local/app/tars
sed -i "s/192.168.2.131/${your_machine_ip}/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/${your_machine_ip}/g" `grep db.tars.com -rl ./*`
sed -i "s/registry.tars.com/${your_machine_ip}/g" `grep registry.tars.com -rl ./*`
sed -i "s/web.tars.com/${your_machine_ip}/g" `grep web.tars.com -rl ./*`
```
**注意，192.168.2.131这个ip是tars开发团队当时部署服务测试的ip信息，替换成本机的部署地址即可，不要是127.0.0.1**

**注意，db.tars.com是tars框架数据库部署的地址信息，替换成自己数据库的部署地址即可**

**注意，registry.tars.com是tars框架主控tarsregistry服务部署的地址信息，替换成自己主控tarsregistry符的部署地址即可**

**注意，web.tars.com是rsync使用的地址信息，替换成自己的部署机器地址即可**

然后在/usr/local/app/tars/目录下，执行脚本，启动tars框架服务
```
chmod u+x tars_install.sh
./tars_install.sh
```
**注意如果几个服务不是部署在同一台服务器上，需要自己手工copy以及处理tars_install.sh脚本**

部署管理平台并启动web管理平台(管理平台必须和tarspatch部署在同一台服务器上)部署tarspatch，切换至root用户，并执行
```
tarspatch/util/init.sh
```
**注意，上面脚本执行后，看看rsync进程是否起来了，若没有看看rsync使用的配置中的ip是否正确（即把web.tars.com替换成本机ip）

在管理平台上面配置tarspatch，注意需要配置服务的可执行目录(/usr/local/app/tars/tarspatch/bin/tarspatch)

在管理平台上面配置tarsconfig，注意需要配置服务的可执行目录(/usr/local/app/tars/tarsconfig/bin/tarsconfig)

tarsnode需要配置监控，避免不小心挂了以后会启动，需要在crontab里面配置
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

## 4.2.2. 服务扩容前安装tarsnode

核心基础服务的安装成功后，如果需要在其他机器也能部署基于tars框架的服务，那么在管理平台扩容部署前，需要安装tarsnode服务。

如果只是在一台机器部署服务进行测试，这一步可以先忽略，等到需要扩容时再执行。

具体步骤跟上一节很像，如下：

切换至root用户，创建基础服务的部署目录，如下：
```  shell
cd /usr/local/app
mkdir tars
chown ${普通用户}:${普通用户} ./tars/
```

将已打好的框架服务包复制到/usr/local/app/tars/，然后解压，如下：
``` shell
cp build/framework.tgz /usr/local/app/tars/
cd /usr/local/app/tars
tar xzfv framework.tgz
```

修改各个服务对应conf目录下配置文件，注意将配置文件中的ip地址修改为本机ip地址，如下：
``` shell
cd /usr/local/app/tars
sed -i "s/192.168.2.131/${your_machine_ip}/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/${your_machine_ip}/g" `grep db.tars.com -rl ./*`
sed -i "s/registry.tars.com/${your_machine_ip}/g" `grep registry.tars.com -rl ./*`
sed -i "s/web.tars.com/${your_machine_ip}/g" `grep web.tars.com -rl ./*`
```
**注意，192.168.2.131这个ip是tars开发团队当时测试的ip信息，替换成自己扩容机器的ip地址即可，不要是127.0.0.1**

**注意，db.tars.com是tars框架数据库部署的地址信息，替换成数据库的部署ip地址即可**

**注意，registry.tars.com是tars框架主控tarsregistry服务部署的地址信息，替换成自己主控tarsregistry的部署地址即可**

**注意，web.tars.com是rsync使用的地址信息，替换成web的部署机器地址即可**

然后在/usr/local/app/tars/目录下，执行脚本，启动tars框架服务
```
chmod u+x tarsnode_install.sh
./tarsnode_install.sh
```

配置监控，避免不小心挂了以后会启动，需要在crontab里面配置
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

## 4.3. 安装web管理系统

>**执行之前先安装tars java语言框架到本地仓库，参见2.2章节**
>管理系统源代码目录名称为**web**

修改配置文件，文件存放的路径在web/src/main/resources/目录下。
- app.config.properties  
  修改为实际的数据库配置

```ini
# 数据库(db_tars)
tarsweb.datasource.tars.addr={$your_db_ip}:3306
tarsweb.datasource.tars.user=tars
tarsweb.datasource.tars.pswd=tars2015

# 发布包存储路径
upload.tgz.path=/usr/local/app/patchs/tars.upload/
```

- tars.conf  
  替换registry1.tars.com，registry2.tars.com为实际IP。可以只配置一个地址，多个地址用冒号“:”连接

```xml
<tars>
    <application>
        #proxy需要的配置
        <client>
            #地址
            locator = tars.tarsregistry.QueryObj@tcp -h registry1.tars.com -p 17890:tars.tarsregistry.QueryObj@tcp -h registry2.tars.com -p 17890
            sync-invoke-timeout = 30000
            #最大超时时间(毫秒)
            max-invoke-timeout = 30000
            #刷新端口时间间隔(毫秒)
            refresh-endpoint-interval = 60000
            #模块间调用[可选]
            stat = tars.tarsstat.StatObj
            #网络异步回调线程个数
            asyncthread = 3
            modulename = tars.system
        </client>
    </application>
</tars>
```

打包，在web目录下执行命令，会在web/target目录下生成tars.war
```
mvn clean package
```

web发布
将tars.war放置到/usr/local/resin/webapps/中
```
cp ./target/tars.war /usr/local/resin/webapps/
```

创建日志目录
```
mkdir -p /data/log/tars
```

修改Resin安装目录下的conf/resin.xml配置文件
将默认的配置
```xml
<host id="" root-directory=".">
    <web-app id="/" root-directory="webapps/ROOT"/>
</host>
```
修改为
```xml
<host id="" root-directory=".">
    <web-app id="/" document-directory="webapps/tars"/>
</host>
```

启动resin
/usr/local/resin/bin/resin.sh start

访问站点
浏览器输入${your machine ip}:8080，即可看到，如下：

![tars](docs/images/tars_web_system_index.png)

## 4.4. 安装框架普通基础服务
**平台部署的端口号仅供参考，保证端口无冲突即可**
### 4.4.1 tarsnotify部署发布

默认tarsnotify在安装核心基础服务时，部署信息已初始化了，安装完管理平台后，就可以看到，如下：

![tars](docs/images/tars_tarsnotify_bushu.png)

上传发布包后，发布如下：

![tars](docs/images/tars_tarsnotify_patch.png)

### 4.4.2 tarsstat部署发布

部署信息如下：

![tars](docs/images/tars_tarsstat_bushu.png)

上传发布包后，发布如下：

![tars](docs/images/tars_tarsstat_patch.png)

### 4.4.3 tarsproperty部署发布

部署信息如下：

![tars](docs/images/tars_tarsproperty_bushu.png)

上传发布包后，发布如下：

![tars](docs/images/tars_tarsproperty_patch.png)

### 4.4.4 tarslog部署发布

部署信息如下：

![tars](docs/images/tars_tarslog_bushu.png)

上传发布包后，发布如下：

![tars](docs/images/tars_tarslog_patch.png)

### 4.4.5 tarsquerystat部署发布

部署信息如下：

![tars](docs/images/tars_tarsquerystat_bushu.png)

**注意在服务部署时，选择非Tars协议，因为Web是以json协议来获取服务监控的数据的**

上传发布包后，发布如下：

![tars](docs/images/tars_tarsquerystat_patch.png)

### 4.4.5 tarsqueryproperty部署发布

部署信息如下：

![tars](docs/images/tars_tarsqueryproperty_bushu.png)

**注意在服务部署时，选择非Tars协议，因为Web是以json协议来获取属性监控的数据的**

上传发布包后，发布如下：

![tars](docs/images/tars_tarsqueryproperty_patch.png)
