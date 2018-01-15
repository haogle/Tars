# 3. <a id="chapter-3"></a>Tars数据库环境初始化
## 3.1. 添加用户
```sql
grant all on *.* to 'tars'@'%' identified by 'tars2015' with grant option;
grant all on *.* to 'tars'@'localhost' identified by 'tars2015' with grant option;
grant all on *.* to 'tars'@'${主机名}' identified by 'tars2015' with grant option;
flush privileges;
```
**注意${主机名}需要修改成自身机器的名称，可以通过查看/etc/hosts

## 3.2. 创建数据库
sql脚本在cpp/framework/sql目录下，修改部署的ip信息
```
sed -i "s/192.168.2.131/${your machine ip}/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/${your machine ip}/g" `grep db.tars.com -rl ./*`
```
**注意，192.168.2.131这个ip是tars开发团队当时部署服务测试的ip信息，替换成自己数据库的部署地址即可,不要是127.0.0.1**

**注意，db.tars.com是tars框架数据库部署的地址信息，替换成自己数据库的部署地址即可**

执行.
```
chmod u+x exec-sql.sh
./exec-sql.sh
```
**注意将${your machine ip}改为部署机器的IP**

**如果exec-sql.sh脚本执行出错，需要脚本里修改数据库用户名root对应的密码**

脚本执行后，会创建3个数据库，分别是db_tars、tars_stat、tars_property。

其中db_tars是框架运行依赖的核心数据库，里面包括了服务部署信息、服务模版信息、服务配置信息等等；

tars_stat是服务监控数据存储的数据库；

tars_property是服务属性监控数据存储的数据库；
