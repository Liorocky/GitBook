# CentOS7 安装JDK、Tomcat、MySQL

## CentOS7 安装JDK、Tomcat、MySQL

### 一、安装JDK

#### 1.卸载旧版本或者系统自带的JDK

（1）列出所有已安装的JDK

```text
rpm -qa | grep jdk
```

（2）卸载不需要的JDK

```text
yum -y remove 安装包名称
```

#### 2.下载并解压JDK

（1）下载安装包 进入到/usr/local目录下新建java目录

```text
mkdir java
```

在Java目录下使用wget指令下载安装包，如

```text
wget --no-cookies --no-check-certificate --header "Cookie:gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
```

或者使用shell工具在本地下好上传至linux。 （2）解压安装包 下载完成后使用命令解压

```text
tar -zxvf 压缩包名称
```

#### 3.配置环境变量

进入/etc/ 文件夹下使用 vim profile 命令编辑器编辑profile文件（全局环境变量配置）。如果没有profile文件，则去/root下配置 .bash\_profile文件（当前用户下的环境变量配置）在文件最后添上如下配置：（担心修改出错可使用ps命令将文件进行备份）

```text
JAVA_HOME=/usr/jdk
CLASSPATH=$JAVA_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH
```

最后不要忘记执行命令使配置文件生效

```text
source /etc/profile
```

输入 java -version 查看JDK配置是否成功。出现版本信息则JDK安装配置完成。

### 二、安装tomcat

#### 1.下载并解压tomcat

（1）下载安装包 进入到/usr/local目录下新建mywork目录

```text
mkdir mywork
```

在mywork目录下使用wget指令下载安装包，如 [https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.58/bin/](https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.58/bin/)

```text
wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.58/bin/apache-tomcat-8.5.58.tar.gz
```

或者使用shell工具在本地下好上传至linux。 （2）解压安装包 下载完成后使用命令解压

```text
tar -zxvf 压缩包名称
```

#### 2.启动tomcat

进入到tomcat主目录，启动tomcat，使用命令

```text
bin/startup.sh
```

查看tomcat是否启动成功（进程是否存在），使用命令

```text
ps -ef | grep tomcat
```

#### 3.查看tomcat是否安装成功

（1）查看防火墙状态

```text
systemctl status firewalld
```

上面命令无效时使用命令

```text
service iptables status
```

（2）关闭linux防火墙

```text
systemctl stop firewalld
```

上面命令无效时使用命令

```text
service iptables stop
```

（3）查看linux的ip地址信息

```text
ifconfig
```

（4）访问tomcat 浏览器输入地址，[http://ip地址：8080](http://ip地址：8080)

### 三、安装mysql

#### 1.卸载系统自带的数据库mariadb

（1）查看系统是否安装了mariadb

```text
yum list installed | grep mariadb
```

（2）卸载mariadb

```text
yum -y remove 应用名称
```

#### 2.下载并解压mysql

（1）下载安装包 进入到/usr/local目录下，使用wget指令下载安装包，如

```text
wget "http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.17-linux-glibc2.5-x86_64.tar.gz"
```

或者使用shell工具在本地下好上传至linux。 （2）解压安装包 下载完成后使用命令解压

```text
tar -zxvf 压缩包名称
```

解压完成后更改文件名称

```text
mv 解压文件名 mysql
```

#### 3.创建数据仓库目录

此目录存放数据库数据

```text
mkdir /usr/local/mysql/data
```

#### 4.创建mysql用户及用户组

（1）创建用户组

```text
groupadd mysql
```

（2）将mysql用户添加至组中并为用户指定mysql目录

```text
useradd -r -s /sbin/nologin -g mysql mysql -d /usr/local/mysql
```

#### 5.指定目录的所有者

（1）进入到mysql根目录

```text
cd /usr/local/mysql
```

（2）改变目录所有者，不要忘记后面的.

```text
chown -R mysql .
```

```text
chgrp -R mysql .
```

```text
chown -R mysql /usr/local/mysql/data
```

#### 6.初始化mysql配置参数

（1）在mysql根目录下执行

```text
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```

 **注意：命令执行后在末尾处会生成初始密码，将其复制到记事本中用于后面首次登录。** （2）设置数据加密

```text
bin/mysql_ssl_rsa_setup --datadir=/usr/local/mysql/data
```

#### 7.修改系统配置文件

（1）将mysql配置文件添加到系统配置文件中 进入目录

```text
cd /usr/local/mysql/support-files
```

复制

```text
cp my-default.cnf /etc/my.cnf
```

```text
cp mysql.server /etc/init.d/mysql
```

（2）编辑mysql配置文件，指定基础目录和数据目录

```text
vi /etc/init.d/mysql
```

修改如下属性：

```text
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
```

#### 8.修改密码

（1）启动mysql

```text
/etc/init.d/mysql start
```

5.0版本是

```text
mysqld start
```

（2）登录

```text
mysql -h localhost -u root -p
```

输入第6步拿到的密码。如果出现：-bash :mysql :commond not found 就执行：

```text
ln -s /usr/local/mysql/bin/mysql /usr/bin
```

创建命令软连接。 （3）修改密码

```text
set password=password('你要设置的密码')
```

#### 9.修改远程主机对root用户的操作权限

（1）赋予所有主机所有权限

```text
grant all privileges on *.* to 'root'@'%' identified by 'root';
```

（2）使权限生效

```text
flush privileges;
```

（3）查看用户表权限

```text
use mysql;
select * from user;
```

#### 10.添加系统环境变量

```text
vi /etc/profile
```

在末尾添加：

```text
export PATH=/usr/local/mysql/bin:$PATH
```

使配置文件生效

```text
source /etc/profile
```

#### 11.远程连接测试

可以使用mysql客户端工具远程连接，若连接失败可关闭防火墙重试。 补充： 查看mysql运行状态， service mysql status --5.0版本是service mysqld status 停止mysql， service mysql stop --5.0版本是service mysqld stop 启动mysql service mysql start --5.0版本是service mysqld start 重启mysql service mysql restart --5.0版本是service mysqld restart mysql可通过修改/etc/my.cnf进行详细配置。

### 四、修改字符集 修改 /etc/my.cnf文件

show variables like 'character\_set%';

## /etc/my.cnf

\[client\] default-character-set=utf8

\[mysqld\] datadir=/var/lib/mysql socket=/var/lib/mysql/mysql.sock user=mysql

## Disabling symbolic-links is recommended to prevent assorted security risks

symbolic-links=0

## default-character-set=utf8

character-set-server=utf8 init\_connect='SET NAMES utf8'

### 五、开机自动关闭防火墙

**查看当前状态** **\[root@xuegod63 ~\]\# systemctl status firewalld.service \#查看firewalld状态** **\[root@xuegod63 ~\]\# systemctl stop firewalld \#关闭** **\[root@xuegod63 ~\]\# systemctl start firewalld \#开启** **\[root@xuegod63 ~\]\# systemctl disable firewalld \#开机自动关闭 //RHLE7** **\[root@xuegod63 ~\]\# chkconfig --list\|grep network \#查看开机是否启动 //RHLE6** **\[root@xuegod63 ~\]\# systemctl enable firewalld \#开机自动启动**

