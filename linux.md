##virtualnav和virtualenvwrapper
```shell
1.安装virtualenv（需要先安装pip3）：
	sudo pip3 install virtualenv
2、安装 virtualenvwrapper
	sudo pip3 install virtualenvwrapper
3、创建目录存放虚拟环境
	sudo mkdir ~/.virtualenvs
	whereis virtualenvwrapper.sh
4、在~/.bashrc中末尾添加
	sudo gedit ~/.bashrc
	export WORKON_HOME=~/.virtualenvs
	export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
	source /usr/local/bin/virtualenvwrapper.sh
	source ~/.bashrc

命令列表：
workon:列出虚拟环境列表
lsvirtualenv:同上
mkvirtualenv [envname]:新建虚拟环境  --python=/usr/bin/python3
workon [envname]:切换虚拟环境
rmvirtualenv  [envname]:删除虚拟环境
deactivate: 离开虚拟环境
cpvirtualenv [sorce] [dest]　　#复制虚拟环境
pip freeze > requirements.txt　　导出该环境下所有依赖到requirements.txt文件
```
## CentOS-->python3

```shell
1. 安装依赖环境
	# yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
2、下载Python3.6
新建个/usr/download 文件夹
	#mkdir /usr/download　
在download文件夹下下载安装包（/usr/download )
	wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
解压文件
	tar -xvf Python-3.6.4.tgz
3、安装Python3
创建安装文件的路径
	mkdir /usr/local/python3
4、编译（在解压的文件夹下 /usr/download/python3.6.4)
	./configure --prefix=/usr/local/python3
5、安装（在解压的文件夹下 /usr/download/python3.6.4)
	make
	make install
6、创建新版本的软链接
创建新的软连接　　
	ln -s /usr/local/python3/bin/python3 /usr/bin/python3
检查python的版本
	python -V
7、配置成功后，pip3用不了，需要进一步配置
将/usr/local/python3/bin加入PATH
	vim ~/.bash_profile
修改后如下：
复制代码
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin:/usr/local/python3/bin

export PATH
复制代码
保存退出后，执行以下命令，让上一步修改成功
	# source ~/.bash_profile
完成！
```



##PyCharm破解

```java
其他破解方法另找:http://idea.lanyus.com/
1.下载：地址：http://idea.lanyus.com/里面的.jar包
2.修改两个vmoptions文件加入
-javaagent:D:\PyCharm 2017.3.3\bin\JetbrainsCrack-2.7-release-str.jar
-javaagent:/opt/pycharm-2018.1.1/bin/JetbrainsCrack-2.7-release-str.jar
3.注册码有效期-2099年:
{"licenseId":"1454467925669","licenseeName":"ilanyu","assigneeName":"","assigneeEmail":"","licenseRestriction":"","checkConcurrentUse":false,"products":[{"code":"II","paidUpTo":"2099-12-31"},{"code":"DM","paidUpTo":"2099-12-31"},{"code":"AC","paidUpTo":"2099-12-31"},{"code":"RS0","paidUpTo":"2099-12-31"},{"code":"WS","paidUpTo":"2099-12-31"},{"code":"DPN","paidUpTo":"2099-12-31"},{"code":"RC","paidUpTo":"2099-12-31"},{"code":"PS","paidUpTo":"2099-12-31"},{"code":"DC","paidUpTo":"2099-12-31"},{"code":"RM","paidUpTo":"2099-12-31"},{"code":"CL","paidUpTo":"2099-12-31"},{"code":"PC","paidUpTo":"2099-12-31"}],"hash":"2911276/0","gracePeriodDays":7,"autoProlongated":false}
```

##ftp服务器
```shell
1.下载pure-ftpd的源码
2.把源码上传到linux的某一目录下，/opt--其名（附加软件）/usr/local/ (默认安装位置)
3.解压源码
	tar -xvjf /opt/pure-ftpd-1.0.47.tar.bz2
4.进入源码目录
	cd pure-ftpd-1.0.47
5.配置编译文件
	./configure --prefix=/usr/local/pure-ftpd/ --with-language=simplified-chinese --with-everything
6.编译并安装
	make && make check && make install
7.添加ftp用户组
	groupadd ftpgroup
8.增加ftp用户
	useradd -G ftpgroup ftpuser
9.创建一个用户的上传目录
	mkdir -p /home/ftpusers/joe
10.添加一个pure-ftpd用户
	/usr/local/pure-ftpd/bin/pure-pw useradd joe -u ftpuser -d /home/ftpusers/joe
11.生成数据库文件
	/usr/local/pure-ftpd/bin/pure-pw mkdb
12.启动pure-ftpd
	/usr/local/pure-ftpd/sbin/pure-ftpd -j -lpuredb:/usr/local/pure-ftpd/etc/pureftpd.pdb &
13.无法上传文件，是因为没有添加目录的用户权限，执行以下命令
	chown ftpuser /home/ftpusers/joe
这样你就可以顺利使用pure-ftp了，
ps:如果连不上，是这个关闭防火墙，或者添加一条端口配置
```
##nfs服务器
```shell
network file system
nfs:网网络文文件系统
搭建nfs至至少需要两台linux,linux1作为nfs客户端,linux2作为nfs服务器器
主机1(192.168.0.12,nfs服务器器) 主机2(192.168.0.13 nfs客户端)
nfs服务器器
1、yum install nfs-utils -y
2、编辑配置文件
vim /etc/exports
/test
*(insecure,rw,3sync,no_root_squash)
3、启动服务
systemctl restart nfs-server.service
systemctl enable nfs-server.service
nfs客户端
1、yum install -y nfs-utils
2、查看对应的ip地址上面面共享的目目录
showmount -e 192.168.0.12(nfs服务器器ip地址)
3、将共享的目目录挂载到本地
mount -t nfs 192.168.0.12:/test/ /root/bajie
4、设置开机挂载
vim /etc/fstab
192.168.0.108:/test/ /root/nfs nfs defaults 0 0
```
##nginx服务器
```shell
ubuntu :
方式1：
sudo apt-get install nginx
配置文件：/etc/nginx/... 运行文件/usr/local/...
方式2：
安装gcc g++的依赖库
sudo apt-get install build-essential
sudo apt-get install libtool
安装pcre依赖库（http://www.pcre.org/）
sudo apt-get update
sudo apt-get install libpcre3 libpcre3-dev
安装zlib依赖库（http://www.zlib.net）
sudo apt-get install zlib1g-dev
安装SSL依赖库（16.04默认已经安装了）

sudo apt-get install openssl
1、下载 Nginx，下载地址：http://nginx.org/download/
2、解压安装包
tar zxvf nginx-1.6.2.tar.gz
3、进入安装包目录
cd nginx-1.6.2
4、编译安装
./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35
make
make install
5、查看nginx版本
/usr/local/webserver/nginx/sbin/nginx -v
6.Nginx 配置
创建 Nginx 运行使用的用户 www：
/usr/sbin/groupadd www 
/usr/sbin/useradd -g www www
配置nginx.conf ，将/usr/local/webserver/nginx/conf/nginx.conf替换为以下内容

cat /usr/local/webserver/nginx/conf/nginx.conf

user www www;
worker_processes 2; #设置值和CPU核心数一致
error_log /usr/local/webserver/nginx/logs/nginx_error.log crit; #日志位置和日志级别
pid /usr/local/webserver/nginx/nginx.pid;
#Specifies the value for maximum file descriptors that can be opened by this process.
worker_rlimit_nofile 65535;
events
{
  use epoll;
  worker_connections 65535;
}
http
{
  include mime.types;
  default_type application/octet-stream;
  log_format main  '$remote_addr - $remote_user [$time_local] "$request" '
               '$status $body_bytes_sent "$http_referer" '
               '"$http_user_agent" $http_x_forwarded_for';
  
#charset gb2312;
     
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 8m;
     
  sendfile on;
  tcp_nopush on;
  keepalive_timeout 60;
  tcp_nodelay on;
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;
  gzip on; 
  gzip_min_length 1k;
  gzip_buffers 4 16k;
  gzip_http_version 1.0;
  gzip_comp_level 2;
  gzip_types text/plain application/x-javascript text/css application/xml;
  gzip_vary on;
 
  #limit_zone crawler $binary_remote_addr 10m;
 #下面是server虚拟主机的配置
 server
  {
    listen 80;#监听端口
    server_name localhost;#域名
    index index.html index.htm index.php;
    root /usr/local/webserver/nginx/html;#站点目录
      location ~ .*\.(php|php5)?$
    {
      #fastcgi_pass unix:/tmp/php-cgi.sock;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
    {
      expires 30d;
  # access_log off;
    }
    location ~ .*\.(js|css)?$
    {
      expires 15d;
   # access_log off;
    }
    access_log off;
  }

}

Nginx 其他命令
以下包含了 Nginx 常用的几个命令：
/usr/local/webserver/nginx/sbin/nginx    				   #启动
/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx

centos:
1.检查并安装所需的依赖软件
　1).gcc:nginx编译依赖gcc环境
　　　安装命令：yum install gcc-c++
　2).pcre:(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式.
　　　安装命令：yum install -y pcre pcre-devel
　3).zlib：该库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip。
　　　安装命令：yum install -y zlib zlib-devel
　4).openssl:一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。nginx不仅支持http协议，还支持https（即在ssl协议上传输http）.
　　　安装命令：yum install -y openssl openssl-devel
2.下载nginx源码包
　下载命令：wget http://nginx.org/download/nginx-1.12.0.tar.gz
3.解压缩源码包并进入
　1).解压缩：tar -zxvf nginx-1.12.0.tar.gz
　2).进入解压缩后文件夹：cd nginx-1.12.0
4.配置编译参数命令:(可以使用./configure --help查询详细参数)
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
注：安装之前需要手动创建上面指定的nginx文件夹，即/var/temp、/var/temp/nginx、/var/run/nginx/文件夹，否则启动时报错
5.编译并安装
　　命令：make && make install
　　可以进入/usr/local/nginx查看文件是否存在conf、sbin、html文件夹，若存在则安装成功
四、基本使用
1.启动nginx
　　　./usr/local/nginx/sbin/nginx
　3).若报错：[emerg] open() "/var/run/nginx/nginx.pid" failed (2: No such file or directory)
　　　需要查看下是不是在/var/run文件夹下不存在nginx文件夹，不存在则新建
　4).查看是否启动：ps -ef | grep nginx
　　　如果有master和worker两个进程证明启动成功
　　注意：执行./nginx启动nginx，这里可以-c指定加载的nginx配置文件，如下：
　　　./nginx -c /usr/local/nginx/conf/nginx.conf
　　　如果不指定-c，nginx在启动时默认加载conf/nginx.conf文件，此文件的地址也可以在编译安装nginx时指定./configure的参数(--conf-path= 指向配置文件（nginx.conf）)
2.停止
　1).暴利kill(不推荐使用)
　　　kill -9 processId
　2).快速停止
　　　cd /usr/local/nginx/sbin && ./nginx -s stop
　　　此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程
　3).完整停止(建议使用)
　　　cd /usr/local/nginx/sbin && ./nginx -s quit
　　　此方式停止步骤是待nginx进程处理任务完毕进行停止
3.重启及重新加载配置
　　1.先停止再启动（建议使用）
　　　　./nginx -s quit && ./nginx
　　2.重新加载配置文件
　　　　./nginx -s reload
4.测试
　　nginx安装成功，启动nginx,即可通过ip地址来访问nginx:
```
##mysql-->Ubuntu/CentOS
```shell
`Ubuntu:`
1.命令apt-get删除mysql
sudo apt-get autoremove --purge mysql-server-5.7
sudo apt-get remove mysql-server
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common //这个很重要
上面的其实有一些是多余的。
2.清理残留数据
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
3.重新安装　　
　　sudo apt-get install mysql-server mysql-client libmysqlclient-dev
　　设置mysql允许远程访问,首首先编辑文文件
			vim /etc/mysql/mysql.conf.d/mysqld.cnf
	在43行行行处,注释掉bind-8ddress = 127.0.0.1
	mysql -uroot -p123(密码以123为例例)
4.安装视图workbench软件
	sudo apt install mysql-workbench
	
`Centos:`
1、配置YUM源
在MySQL官网中下载YUM源rpm安装包：http://dev.mysql.com/downloads/repo/yum/ 
MySQL YUM源下载地址

# 下载mysql源安装包
shell> wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
# 安装mysql源
shell> yum localinstall mysql57-community-release-el7-8.noarch.rpm
检查mysql源是否安装成功

shell> yum repolist enabled | grep "mysql.*-community.*"
检查mysql源安装是否正确 
看到上图所示表示安装成功。 
可以修改vim /etc/yum.repos.d/mysql-community.repo源，改变默认安装的mysql版本。比如要安装5.6版本，将5.7源的enabled=1改成enabled=0。然后再将5.6源的enabled=0改成enabled=1即可。改完之后的效果如下所示： 
这里写图片描述

2、安装MySQL
shell> yum install mysql-community-server
3、启动MySQL服务
shell> systemctl start mysqld
查看MySQL的启动状态

shell> systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; disabled; vendor preset: disabled)
   Active: active (running) since 五 2016-06-24 04:37:37 CST; 35min ago
 Main PID: 2888 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─2888 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

6月 24 04:37:36 localhost.localdomain systemd[1]: Starting MySQL Server...
6月 24 04:37:37 localhost.localdomain systemd[1]: Started MySQL Server.
4、开机启动
shell> systemctl enable mysqld
shell> systemctl daemon-reload
5、修改root本地登录密码
mysql安装完成之后，在/var/log/mysqld.log文件中给root生成了一个默认密码。通过下面的方式找到root默认密码，然后登录mysql进行修改：

shell> grep 'temporary password' /var/log/mysqld.log
root默认密码

shell> mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!'; 
或者

mysql> set password for 'root'@'localhost'=password('MyNewPass4!'); 
注意：mysql5.7默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。否则会提示ERROR 1819 (HY000): Your password does not satisfy the current policy requirements错误，如下图所示： 
密码策略提示

通过msyql环境变量可以查看密码策略的相关信息：

mysql> show variables like '%password%';
mysql密码策略 
validate_password_policy：密码策略，默认为MEDIUM策略 
validate_password_dictionary_file：密码策略文件，策略为STRONG才需要 
validate_password_length：密码最少长度 
validate_password_mixed_case_count：大小写字符长度，至少1个 
validate_password_number_count ：数字至少1个 
validate_password_special_char_count：特殊字符至少1个 
上述参数是默认策略MEDIUM的密码检查规则。

共有以下几种密码策略：

策略	检查规则
0 or LOW	Length
1 or MEDIUM	Length; numeric, lowercase/uppercase, and special characters
2 or STRONG	Length; numeric, lowercase/uppercase, and special characters; dictionary file
MySQL官网密码策略详细说明：http://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy

修改密码策略
在/etc/my.cnf文件添加validate_password_policy配置，指定密码策略

# 选择0（LOW），1（MEDIUM），2（STRONG）其中一种，选择2需要提供密码字典文件
validate_password_policy=0
如果不需要密码策略，添加my.cnf文件中添加如下配置禁用即可：

validate_password = off
重新启动mysql服务使配置生效：

systemctl restart mysqld
6、添加远程登录用户
默认只允许root帐户在本地登录，如果要在其它机器上连接mysql，必须修改root允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我添加一个新的帐户：

mysql> GRANT ALL PRIVILEGES ON *.* TO 'yangxin'@'%' IDENTIFIED BY 'Yangxin0917!' WITH GRANT OPTION;
7、配置默认编码为utf8
修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：

[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
重新启动mysql服务，查看数据库默认编码如下所示：

mysql默认编码

默认配置文件路径： 
配置文件：/etc/my.cnf 
日志文件：/var/log//var/log/mysqld.log 
服务启动脚本：/usr/lib/systemd/system/mysqld.service 
socket文件：/var/run/mysqld/mysqld.pid
```
##Redis
```shell
1.安装redis服务    
	sudo apt install redis-server -y
2.安装Redis可视化工工具 RedisDesktopManager
	https://redisdesktop.com/download
```

##Mongodb
```shell

```
##VNC

```shell
RealVNC官方最新下载地址：http://www.realvnc.com/products/download.html
支持4.6以上直至2011年11月10号更新。
WHJRK-UXY7V-Q34M9-CZU8L-8KGFA 
S4J7A-XWXY5-KXAJW-54KRA-TP3QA 
48R4P-NFZ46-NBCWY-Q2ZJT-3H9RA 
NGNW9-7Q8BK-UQGY7-J3KAA-6G39 

```

##防火墙

```shell
1.安装firewalld firewall-config
# apt install firewalld firewall-config

启动：# systemctl start firewalld
查看状态：# systemctl status firewalld 或者 firewall-cmd –state
停止：# systemctl disable firewalld
禁用：# systemctl stop firewalld
重启：#systemctl restart firewalld

永久的开放需要的端口：
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent  
sudo firewall-cmd --reload //重载生效刚才的端口设置  
防火墙检查规则：
firewall-cmd --list-all //查看防火墙规则，可查到你当前开放的端口信息
常用命令介绍
firewall-cmd --state ##查看防火墙状态，是否是running
firewall-cmd --reload ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones ##列出支持的zone
firewall-cmd --get-services ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp ##查看ftp服务是否支持，返回yes或者no
firewall-cmd --add-service=ftp ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent ##永久移除ftp服务
firewall-cmd --add-port=80/tcp --permanent ##永久添加80端口
iptables -L -n ##查看规则，这个命令是和iptables的相同的
man firewall-cmd ##查看帮助
```
## linux快捷方式的创建

```shell
#方法2：如果你刚开始没有建立快捷方式自己建立一个快捷方式，方法如下终端输入：
sudo gedit /usr/share/applications/[...].desktop粘贴模板：
[Desktop Entry]
Type=Application
Name=...
GenericName=...
Comment=...
Exec=/opt/....      path （...部分是你存放的地址）
Icon=/opt/....      img（...部分-图片icon） 
Terminal=false
Categories=.....

```

## 网络端口内存cpu查看软件（16.04以下）

```shell
indicator-sysmonitor：https://launchpad.net/indicator-sysmonitor/+download
```

## Shadowsocks翻墙

```shell
安装Shadowsocks：
Debian / Ubuntu:
# apt-get install python3-pip
# pip3 install shadowsocks
# apt-get install shadowsocks
编辑文件/etc/shadowsocks.json，填写代理信息：
{
"server":"11.22.33.44",
"server_port":50003,
"local_port":1080,
"password":"123456",
"timeout":600,
"method":"aes-256-cfb"
}
其中local_address是本地绑定的IP。
method是加密码算法，这个必须跟shadowsocks的服务端的一致。
其它参数不解释，很容易理解。

接下来启动shadowsocks客户端。
后台运行：
alias startssl="sudo sslocal -c /opt/shadowsocks/shadowsocks.json -d start"
alias stopssl="sudo sslocal -c /opt/shadowsocks/shadowsocks.json -d stop"
Shadowsocks使用的socks5协议,而终端很多工具目前只支持http和https等协议，所以我们要用工具把socks5转成http协议。
在linux下可以使用privoxy来实现这个转换。

$ sudo apt-get install privoxy
编辑配置文件/etc/privoxy/config,加入下面两行内容。

forward-socks5t   /               127.0.0.1:1080 .
listen-address  localhost:8118
需要注意：

127.0.0.1：1080是shadowsocks的客户端的IP和端口，要与上面shadowsocks里的配置相符。
请不要忽略这行配置后面的.,这可不是句号的意思，这个必须有。
启动服务：

$ sudo service privoxy restart
二、设置代理

 2.1 设置当前用户的全局代理
 # 修改bashrc文件
vim ~/.bashrc
 # 添加下面的内容，wq保存 
export http_proxy=http://127.0.0.1:8118
export https_proxy=http://127.0.0.1:8118
export ftp_proxy=http://127.0.0.1:8118
 # 立刻生效 
source ~/.bashrc
 2.2 设置系统全局代理(可不设置)
# 修改profile文件
vim /etc/profile
# 添加下面的内容，wq保存。
复制代码
export http_proxy=http://127.0.0.1:8118
export https_proxy=http://127.0.0.1:8118
export ftp_proxy=http://127.0.0.1:8118
复制代码
# 立即生效
source /etc/profile

无法打开修改方法：
用vim打开文件：vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py (该路径请根据自己的系统情况自行修改，如果不知道该文件在哪里的话，可以使用find命令查找文件位置)
跳转到52行（shadowsocks2.8.2版本，其他版本搜索一下cleanup）
进入编辑模式
将第52行libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,) 
改为libcrypto.EVP_CIPHER_CTX_reset.argtypes = (c_void_p,)
再次搜索cleanup（全文件共2处，此处位于111行），将libcrypto.EVP_CIPHER_CTX_cleanup(self._ctx) 
改为libcrypto.EVP_CIPHER_CTX_reset(self._ctx)
保存并退出
启动shadowsocks服务：service shadowsocks start 或 sslocal -c ss配置文件目录
问题解决
```

## **systemctl命令**

```shell
systemctl list-units            ##列出当前系统服务的状态--type=service
systemctl list-unit-files       ##列出服务的开机状态--type=service
systemctl status sshd           ##查看指定服务的状态
systemctl stop sshd             ##关闭指定服务
systemctl start sshd            ##开启指定服务
systemctl restart sshd          ##从新启动服务
systemctl enable sshd           ##设定指定服务开机开启
systemctl disable sshd          ##设定指定服务开机关闭
systemctl reload sshd           ##使指定服务从新加载配置
systemctl list-dependencies sshd    ##查看指定服务的倚赖关系
systemctl mask  sshd            ##冻结指定服务
systemctl unmask sshd           ##启用服务
```

## 清理残留数据

```shell
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
sudo apt autoremove
sudo apt autoclean
```

## Gnome-Shell主题

```shell
sudo apt install gnome-tweak-tool
sudo apt install gnome-shell
sudo apt install chrome-gnome-shell
安装谷歌gnome shell插件
```

## MarkDown-Typora

```shell
网站:https://typora.io/#linux
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
sudo add-apt-repository 'deb https://typora.io linux/'
sudo apt-get update
sudo apt-get install typora
```

## Ubuntu按键冲突

Source : <http://askubuntu.com/questions/315625/how-to-disable-the-shortcut-ctrl-alt-arrow-in-gnome-3-8>

ubuntu 下执行命令

添加:

```
gsettings get org.gnome.desktop.wm.keybindings switch-to-workspace-down

['<Primary><Shift>Down', '<Control><Alt>Down']
```

 删除:

```
gsettings set org.gnome.desktop.wm.keybindings switch-to-workspace-down "['']"
gsettings set org.gnome.desktop.wm.keybindings switch-to-workspace-up "['']"
```

## Linux命令

```shell
入门装B命令:'sudo apt-get/yum install screenfetch' 执行:'screenfetch'
sudo mv path1 path2 --移动
sudo cp -r path1 path2 --拷贝
sudo scp -r  root@..:/home/ /home/
sudo rm -rf path1 --删除
sudo mkdir -p /dir1/dir2/dirn... --多级创建
sudo head -n 3 path --查看文件前几行
sudo teal -n 3 path --查看文件后几行
whereis filename --查看文件位置
sudo find / -name finaname --扫描/查找filename全部文件
touch file --创建一个文件
less file --查看文件
cat file --查看文件
sudo lsof -i:port --查看端口
sudo lsof -i|grep [servicename] --查看端口
sudo kill pid --杀pid进程
sudo killall servicename --杀指定全部服务 
top --查看运行时的进程
ps -aux --查看所有进程
ps -ef|grep [] --查看指定进程
sudo chmod 777/+/-.. dir/file --赋予权限
ls -a/-l/-lhs
ln -s file1 lnk1 创建一个指向文件或目录的软链接 
mount/umount path2 --挂载/载硬盘
tar -xzvf/xjvf/cxvf .. --解压、压缩、打包...
ifconfig / ip addr/add --ip地址查看
def -h --挂载所有磁盘信息
groupadd [groud] --创建组
useradd -g [group] [user] --创建用户
passwd --设置密码
gedit vim vi ....
```


```shell
系统信息 
arch 显示机器的处理器架构(1) 
uname -m 显示机器的处理器架构(2) 
uname -r 显示正在使用的内核版本 
dmidecode -q 显示硬件系统部件 - (SMBIOS / DMI) 
hdparm -i /dev/hda 罗列一个磁盘的架构特性 
hdparm -tT /dev/sda 在磁盘上执行测试性读取操作 
cat /proc/cpuinfo 显示CPU info的信息 
cat /proc/interrupts 显示中断 
cat /proc/meminfo 校验内存使用 
cat /proc/swaps 显示哪些swap被使用 
cat /proc/version 显示内核的版本 
cat /proc/net/dev 显示网络适配器及统计 
cat /proc/mounts 显示已加载的文件系统 
lspci -tv 罗列 PCI 设备 
lsusb -tv 显示 USB 设备 
date 显示系统日期 
cal 2007 显示2007年的日历表 
date 041217002007.00 设置日期和时间 - 月日时分年.秒 
clock -w 将时间修改保存到 BIOS 

关机 (系统的关机、重启以及登出 ) 
shutdown -h now 关闭系统(1) 
init 0 关闭系统(2) 
telinit 0 关闭系统(3) 
shutdown -h hours:minutes & 按预定时间关闭系统 
shutdown -c 取消按预定时间关闭系统 
shutdown -r now 重启(1) 
reboot 重启(2) 
logout 注销 

文件和目录 
cd /home 进入 '/ home' 目录' 
cd .. 返回上一级目录 
cd ../.. 返回上两级目录 
cd 进入个人的主目录 
cd ~user1 进入个人的主目录 
cd - 返回上次所在的目录 
pwd 显示工作路径 
ls 查看目录中的文件 
ls -F 查看目录中的文件 
ls -l 显示文件和目录的详细资料 
ls -a 显示隐藏文件 
ls *[0-9]* 显示包含数字的文件名和目录名 
tree 显示文件和目录由根目录开始的树形结构(1) 
lstree 显示文件和目录由根目录开始的树形结构(2) 
mkdir dir1 创建一个叫做 'dir1' 的目录' 
mkdir dir1 dir2 同时创建两个目录 
mkdir -p /tmp/dir1/dir2 创建一个目录树 
rm -f file1 删除一个叫做 'file1' 的文件' 
rmdir dir1 删除一个叫做 'dir1' 的目录' 
rm -rf dir1 删除一个叫做 'dir1' 的目录并同时删除其内容 
rm -rf dir1 dir2 同时删除两个目录及它们的内容 
mv dir1 new_dir 重命名/移动 一个目录 
cp file1 file2 复制一个文件 
cp dir/* . 复制一个目录下的所有文件到当前工作目录 
cp -a /tmp/dir1 . 复制一个目录到当前工作目录 
cp -a dir1 dir2 复制一个目录 
ln -s file1 lnk1 创建一个指向文件或目录的软链接 
ln file1 lnk1 创建一个指向文件或目录的物理链接 
touch -t 0712250000 file1 修改一个文件或目录的时间戳 - (YYMMDDhhmm) 
file file1 outputs the mime type of the file as text 
iconv -l 列出已知的编码 
iconv -f fromEncoding -t toEncoding inputFile > outputFile creates a new from the given input file by assuming it is encoded in fromEncoding and converting it to toEncoding. 
find . -maxdepth 1 -name *.jpg -print -exec convert "{}" -resize 80x60 "thumbs/{}" \; batch resize files in the current directory and send them to a thumbnails directory (requires convert from Imagemagick) 

文件搜索 
find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
find / -user user1 搜索属于用户 'user1' 的文件和目录 
find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
find / -name \*.rpm -exec chmod 755 '{}' \; 搜索以 '.rpm' 结尾的文件并定义其权限 
find / -xdev -name \*.rpm 搜索以 '.rpm' 结尾的文件，忽略光驱、捷盘等可移动设备 
locate \*.ps 寻找以 '.ps' 结尾的文件 - 先运行 'updatedb' 命令 
whereis halt 显示一个二进制文件、源码或man的位置 
which halt 显示一个二进制文件或可执行文件的完整路径 

挂载一个文件系统 
mount /dev/hda2 /mnt/hda2 挂载一个叫做hda2的盘 - 确定目录 '/ mnt/hda2' 已经存在 
umount /dev/hda2 卸载一个叫做hda2的盘 - 先从挂载点 '/ mnt/hda2' 退出 
fuser -km /mnt/hda2 当设备繁忙时强制卸载 
umount -n /mnt/hda2 运行卸载操作而不写入 /etc/mtab 文件- 当文件为只读或当磁盘写满时非常有用 
mount /dev/fd0 /mnt/floppy 挂载一个软盘 
mount /dev/cdrom /mnt/cdrom 挂载一个cdrom或dvdrom 
mount /dev/hdc /mnt/cdrecorder 挂载一个cdrw或dvdrom 
mount /dev/hdb /mnt/cdrecorder 挂载一个cdrw或dvdrom 
mount -o loop file.iso /mnt/cdrom 挂载一个文件或ISO镜像文件 
mount -t vfat /dev/hda5 /mnt/hda5 挂载一个Windows FAT32文件系统 
mount /dev/sda1 /mnt/usbdisk 挂载一个usb 捷盘或闪存设备 
mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share 挂载一个windows网络共享 

磁盘空间 
df -h 显示已经挂载的分区列表 
ls -lSr |more 以尺寸大小排列文件和目录 
du -sh dir1 估算目录 'dir1' 已经使用的磁盘空间' 
du -sk * | sort -rn 以容量大小为依据依次显示文件和目录的大小 
rpm -q -a --qf '%10{SIZE}t%{NAME}n' | sort -k1,1n 以大小为依据依次显示已安装的rpm包所使用的空间 (fedora, redhat类系统) 
dpkg-query -W -f='${Installed-Size;10}t${Package}n' | sort -k1,1n 以大小为依据显示已安装的deb包所使用的空间 (ubuntu, debian类系统) 

用户和群组 
groupadd group_name 创建一个新用户组 
groupdel group_name 删除一个用户组 
groupmod -n new_group_name old_group_name 重命名一个用户组 
useradd -c "Name Surname " -g admin -d /home/user1 -s /bin/bash user1 创建一个属于 "admin" 用户组的用户 
useradd user1 创建一个新用户 
userdel -r user1 删除一个用户 ( '-r' 排除主目录) 
usermod -c "User FTP" -g system -d /ftp/user1 -s /bin/nologin user1 修改用户属性 
passwd 修改口令 
passwd user1 修改一个用户的口令 (只允许root执行) 
chage -E 2005-12-31 user1 设置用户口令的失效期限 
pwck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的用户 
grpck 检查 '/etc/passwd' 的文件格式和语法修正以及存在的群组 
newgrp group_name 登陆进一个新的群组以改变新创建文件的预设群组 

文件的权限 - 使用 "+" 设置权限，使用 "-" 用于取消 
ls -lh 显示权限 
ls /tmp | pr -T5 -W$COLUMNS 将终端划分成5栏显示 
chmod ugo+rwx directory1 设置目录的所有人(u)、群组(g)以及其他人(o)以读（r ）、写(w)和执行(x)的权限 
chmod go-rwx directory1 删除群组(g)与其他人(o)对目录的读写执行权限 
chown user1 file1 改变一个文件的所有人属性 
chown -R user1 directory1 改变一个目录的所有人属性并同时改变改目录下所有文件的属性 
chgrp group1 file1 改变文件的群组 
chown user1:group1 file1 改变一个文件的所有人和群组属性 
find / -perm -u+s 罗列一个系统中所有使用了SUID控制的文件 
chmod u+s /bin/file1 设置一个二进制文件的 SUID 位 - 运行该文件的用户也被赋予和所有者同样的权限 
chmod u-s /bin/file1 禁用一个二进制文件的 SUID位 
chmod g+s /home/public 设置一个目录的SGID 位 - 类似SUID ，不过这是针对目录的 
chmod g-s /home/public 禁用一个目录的 SGID 位 
chmod o+t /home/public 设置一个文件的 STIKY 位 - 只允许合法所有人删除文件 
chmod o-t /home/public 禁用一个目录的 STIKY 位 

文件的特殊属性 - 使用 "+" 设置权限，使用 "-" 用于取消 
chattr +a file1 只允许以追加方式读写文件 
chattr +c file1 允许这个文件能被内核自动压缩/解压 
chattr +d file1 在进行文件系统备份时，dump程序将忽略这个文件 
chattr +i file1 设置成不可变的文件，不能被删除、修改、重命名或者链接 
chattr +s file1 允许一个文件被安全地删除 
chattr +S file1 一旦应用程序对这个文件执行了写操作，使系统立刻把修改的结果写到磁盘 
chattr +u file1 若文件被删除，系统会允许你在以后恢复这个被删除的文件 
lsattr 显示特殊的属性 

打包和压缩文件 
bunzip2 file1.bz2 解压一个叫做 'file1.bz2'的文件 
bzip2 file1 压缩一个叫做 'file1' 的文件 
gunzip file1.gz 解压一个叫做 'file1.gz'的文件 
gzip file1 压缩一个叫做 'file1'的文件 
gzip -9 file1 最大程度压缩 
rar a file1.rar test_file 创建一个叫做 'file1.rar' 的包 
rar a file1.rar file1 file2 dir1 同时压缩 'file1', 'file2' 以及目录 'dir1' 
rar x file1.rar 解压rar包 
unrar x file1.rar 解压rar包 
tar -cvf archive.tar file1 创建一个非压缩的 tarball 
tar -cvf archive.tar file1 file2 dir1 创建一个包含了 'file1', 'file2' 以及 'dir1'的档案文件 
tar -tf archive.tar 显示一个包中的内容 
tar -xvf archive.tar 释放一个包 
tar -xvf archive.tar -C /tmp 将压缩包释放到 /tmp目录下 
tar -cvfj archive.tar.bz2 dir1 创建一个bzip2格式的压缩包 
tar -xvfj archive.tar.bz2 解压一个bzip2格式的压缩包 
tar -cvfz archive.tar.gz dir1 创建一个gzip格式的压缩包 
tar -xvfz archive.tar.gz 解压一个gzip格式的压缩包 
zip file1.zip file1 创建一个zip格式的压缩包 
zip -r file1.zip file1 file2 dir1 将几个文件和目录同时压缩成一个zip格式的压缩包 
unzip file1.zip 解压一个zip格式压缩包 

RPM 包 - （Fedora, Redhat及类似系统） 
rpm -ivh package.rpm 安装一个rpm包 
rpm -ivh --nodeeps package.rpm 安装一个rpm包而忽略依赖关系警告 
rpm -U package.rpm 更新一个rpm包但不改变其配置文件 
rpm -F package.rpm 更新一个确定已经安装的rpm包 
rpm -e package_name.rpm 删除一个rpm包 
rpm -qa 显示系统中所有已经安装的rpm包 
rpm -qa | grep httpd 显示所有名称中包含 "httpd" 字样的rpm包 
rpm -qi package_name 获取一个已安装包的特殊信息 
rpm -qg "System Environment/Daemons" 显示一个组件的rpm包 
rpm -ql package_name 显示一个已经安装的rpm包提供的文件列表 
rpm -qc package_name 显示一个已经安装的rpm包提供的配置文件列表 
rpm -q package_name --whatrequires 显示与一个rpm包存在依赖关系的列表 
rpm -q package_name --whatprovides 显示一个rpm包所占的体积 
rpm -q package_name --scripts 显示在安装/删除期间所执行的脚本l 
rpm -q package_name --changelog 显示一个rpm包的修改历史 
rpm -qf /etc/httpd/conf/httpd.conf 确认所给的文件由哪个rpm包所提供 
rpm -qp package.rpm -l 显示由一个尚未安装的rpm包提供的文件列表 
rpm --import /media/cdrom/RPM-GPG-KEY 导入公钥数字证书 
rpm --checksig package.rpm 确认一个rpm包的完整性 
rpm -qa gpg-pubkey 确认已安装的所有rpm包的完整性 
rpm -V package_name 检查文件尺寸、 许可、类型、所有者、群组、MD5检查以及最后修改时间 
rpm -Va 检查系统中所有已安装的rpm包- 小心使用 
rpm -Vp package.rpm 确认一个rpm包还未安装 
rpm2cpio package.rpm | cpio --extract --make-directories *bin* 从一个rpm包运行可执行文件 
rpm -ivh /usr/src/redhat/RPMS/`arch`/package.rpm 从一个rpm源码安装一个构建好的包 
rpmbuild --rebuild package_name.src.rpm 从一个rpm源码构建一个 rpm 包 

YUM 软件包升级器 - （Fedora, RedHat及类似系统） 
yum install package_name 下载并安装一个rpm包 
yum localinstall package_name.rpm 将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系 
yum update package_name.rpm 更新当前系统中所有安装的rpm包 
yum update package_name 更新一个rpm包 
yum remove package_name 删除一个rpm包 
yum list 列出当前系统中安装的所有包 
yum search package_name 在rpm仓库中搜寻软件包 
yum clean packages 清理rpm缓存删除下载的包 
yum clean headers 删除所有头文件 
yum clean all 删除所有缓存的包和头文件 

DEB 包 (Debian, Ubuntu 以及类似系统) 
dpkg -i package.deb 安装/更新一个 deb 包 
dpkg -r package_name 从系统删除一个 deb 包 
dpkg -l 显示系统中所有已经安装的 deb 包 
dpkg -l | grep httpd 显示所有名称中包含 "httpd" 字样的deb包 
dpkg -s package_name 获得已经安装在系统中一个特殊包的信息 
dpkg -L package_name 显示系统中已经安装的一个deb包所提供的文件列表 
dpkg --contents package.deb 显示尚未安装的一个包所提供的文件列表 
dpkg -S /bin/ping 确认所给的文件由哪个deb包提供 

APT 软件工具 (Debian, Ubuntu 以及类似系统) 
apt-get install package_name 安装/更新一个 deb 包 
apt-cdrom install package_name 从光盘安装/更新一个 deb 包 
apt-get update 升级列表中的软件包 
apt-get upgrade 升级所有已安装的软件 
apt-get remove package_name 从系统删除一个deb包 
apt-get check 确认依赖的软件仓库正确 
apt-get clean 从下载的软件包中清理缓存 
apt-cache search searched-package 返回包含所要搜索字符串的软件包名称 

查看文件内容 
cat file1 从第一个字节开始正向查看文件的内容 
tac file1 从最后一行开始反向查看一个文件的内容 
more file1 查看一个长文件的内容 
less file1 类似于 'more' 命令，但是它允许在文件中和正向操作一样的反向操作 
head -2 file1 查看一个文件的前两行 
tail -2 file1 查看一个文件的最后两行 
tail -f /var/log/messages 实时查看被添加到一个文件中的内容 

文本处理 
cat file1 file2 ... | command <> file1_in.txt_or_file1_out.txt general syntax for text manipulation using PIPE, STDIN and STDOUT 
cat file1 | command( sed, grep, awk, grep, etc...) > result.txt 合并一个文件的详细说明文本，并将简介写入一个新文件中 
cat file1 | command( sed, grep, awk, grep, etc...) >> result.txt 合并一个文件的详细说明文本，并将简介写入一个已有的文件中 
grep Aug /var/log/messages 在文件 '/var/log/messages'中查找关键词"Aug" 
grep ^Aug /var/log/messages 在文件 '/var/log/messages'中查找以"Aug"开始的词汇 
grep [0-9] /var/log/messages 选择 '/var/log/messages' 文件中所有包含数字的行 
grep Aug -R /var/log/* 在目录 '/var/log' 及随后的目录中搜索字符串"Aug" 
sed 's/stringa1/stringa2/g' example.txt 将example.txt文件中的 "string1" 替换成 "string2" 
sed '/^$/d' example.txt 从example.txt文件中删除所有空白行 
sed '/ *#/d; /^$/d' example.txt 从example.txt文件中删除所有注释和空白行 
echo 'esempio' | tr '[:lower:]' '[:upper:]' 合并上下单元格内容 
sed -e '1d' result.txt 从文件example.txt 中排除第一行 
sed -n '/stringa1/p' 查看只包含词汇 "string1"的行 
sed -e 's/ *$//' example.txt 删除每一行最后的空白字符 
sed -e 's/stringa1//g' example.txt 从文档中只删除词汇 "string1" 并保留剩余全部 
sed -n '1,5p;5q' example.txt 查看从第一行到第5行内容 
sed -n '5p;5q' example.txt 查看第5行 
sed -e 's/00*/0/g' example.txt 用单个零替换多个零 
cat -n file1 标示文件的行数 
cat example.txt | awk 'NR%2==1' 删除example.txt文件中的所有偶数行 
echo a b c | awk '{print $1}' 查看一行第一栏 
echo a b c | awk '{print $1,$3}' 查看一行的第一和第三栏 
paste file1 file2 合并两个文件或两栏的内容 
paste -d '+' file1 file2 合并两个文件或两栏的内容，中间用"+"区分 
sort file1 file2 排序两个文件的内容 
sort file1 file2 | uniq 取出两个文件的并集(重复的行只保留一份) 
sort file1 file2 | uniq -u 删除交集，留下其他的行 
sort file1 file2 | uniq -d 取出两个文件的交集(只留下同时存在于两个文件中的文件) 
comm -1 file1 file2 比较两个文件的内容只删除 'file1' 所包含的内容 
comm -2 file1 file2 比较两个文件的内容只删除 'file2' 所包含的内容 
comm -3 file1 file2 比较两个文件的内容只删除两个文件共有的部分 

字符设置和文件格式转换 
dos2unix filedos.txt fileunix.txt 将一个文本文件的格式从MSDOS转换成UNIX 
unix2dos fileunix.txt filedos.txt 将一个文本文件的格式从UNIX转换成MSDOS 
recode ..HTML < page.txt > page.html 将一个文本文件转换成html 
recode -l | more 显示所有允许的转换格式 

文件系统分析 
badblocks -v /dev/hda1 检查磁盘hda1上的坏磁块 
fsck /dev/hda1 修复/检查hda1磁盘上linux文件系统的完整性 
fsck.ext2 /dev/hda1 修复/检查hda1磁盘上ext2文件系统的完整性 
e2fsck /dev/hda1 修复/检查hda1磁盘上ext2文件系统的完整性 
e2fsck -j /dev/hda1 修复/检查hda1磁盘上ext3文件系统的完整性 
fsck.ext3 /dev/hda1 修复/检查hda1磁盘上ext3文件系统的完整性 
fsck.vfat /dev/hda1 修复/检查hda1磁盘上fat文件系统的完整性 
fsck.msdos /dev/hda1 修复/检查hda1磁盘上dos文件系统的完整性 
dosfsck /dev/hda1 修复/检查hda1磁盘上dos文件系统的完整性 

初始化一个文件系统 
mkfs /dev/hda1 在hda1分区创建一个文件系统 
mke2fs /dev/hda1 在hda1分区创建一个linux ext2的文件系统 
mke2fs -j /dev/hda1 在hda1分区创建一个linux ext3(日志型)的文件系统 
mkfs -t vfat 32 -F /dev/hda1 创建一个 FAT32 文件系统 
fdformat -n /dev/fd0 格式化一个软盘 
mkswap /dev/hda3 创建一个swap文件系统 

SWAP文件系统 
mkswap /dev/hda3 创建一个swap文件系统 
swapon /dev/hda3 启用一个新的swap文件系统 
swapon /dev/hda2 /dev/hdb3 启用两个swap分区 

备份 
dump -0aj -f /tmp/home0.bak /home 制作一个 '/home' 目录的完整备份 
dump -1aj -f /tmp/home0.bak /home 制作一个 '/home' 目录的交互式备份 
restore -if /tmp/home0.bak 还原一个交互式备份 
rsync -rogpav --delete /home /tmp 同步两边的目录 
rsync -rogpav -e ssh --delete /home ip_address:/tmp 通过SSH通道rsync 
rsync -az -e ssh --delete ip_addr:/home/public /home/local 通过ssh和压缩将一个远程目录同步到本地目录 
rsync -az -e ssh --delete /home/local ip_addr:/home/public 通过ssh和压缩将本地目录同步到远程目录 
dd bs=1M if=/dev/hda | gzip | ssh user@ip_addr 'dd of=hda.gz' 通过ssh在远程主机上执行一次备份本地磁盘的操作 
dd if=/dev/sda of=/tmp/file1 备份磁盘内容到一个文件 
tar -Puf backup.tar /home/user 执行一次对 '/home/user' 目录的交互式备份操作 
( cd /tmp/local/ && tar c . ) | ssh -C user@ip_addr 'cd /home/share/ && tar x -p' 通过ssh在远程目录中复制一个目录内容 
( tar c /home ) | ssh -C user@ip_addr 'cd /home/backup-home && tar x -p' 通过ssh在远程目录中复制一个本地目录 
tar cf - . | (cd /tmp/backup ; tar xf - ) 本地将一个目录复制到另一个地方，保留原有权限及链接 
find /home/user1 -name '*.txt' | xargs cp -av --target-directory=/home/backup/ --parents 从一个目录查找并复制所有以 '.txt' 结尾的文件到另一个目录 
find /var/log -name '*.log' | tar cv --files-from=- | bzip2 > log.tar.bz2 查找所有以 '.log' 结尾的文件并做成一个bzip包 
dd if=/dev/hda of=/dev/fd0 bs=512 count=1 做一个将 MBR (Master Boot Record)内容复制到软盘的动作 
dd if=/dev/fd0 of=/dev/hda bs=512 count=1 从已经保存到软盘的备份中恢复MBR内容 

光盘 
cdrecord -v gracetime=2 dev=/dev/cdrom -eject blank=fast -force 清空一个可复写的光盘内容 
mkisofs /dev/cdrom > cd.iso 在磁盘上创建一个光盘的iso镜像文件 
mkisofs /dev/cdrom | gzip > cd_iso.gz 在磁盘上创建一个压缩了的光盘iso镜像文件 
mkisofs -J -allow-leading-dots -R -V "Label CD" -iso-level 4 -o ./cd.iso data_cd 创建一个目录的iso镜像文件 
cdrecord -v dev=/dev/cdrom cd.iso 刻录一个ISO镜像文件 
gzip -dc cd_iso.gz | cdrecord dev=/dev/cdrom - 刻录一个压缩了的ISO镜像文件 
mount -o loop cd.iso /mnt/iso 挂载一个ISO镜像文件 
cd-paranoia -B 从一个CD光盘转录音轨到 wav 文件中 
cd-paranoia -- "-3" 从一个CD光盘转录音轨到 wav 文件中（参数-3） 
cdrecord --scanbus 扫描总线以识别scsi通道 
dd if=/dev/hdc | md5sum 校验一个设备的md5sum编码，例如一张 CD 

网络 - （以太网和WIFI无线） 
ifconfig eth0 显示一个以太网卡的配置 
ifup eth0 启用一个 'eth0' 网络设备 
ifdown eth0 禁用一个 'eth0' 网络设备 
ifconfig eth0 192.168.1.1 netmask 255.255.255.0 控制IP地址 
ifconfig eth0 promisc 设置 'eth0' 成混杂模式以嗅探数据包 (sniffing) 
dhclient eth0 以dhcp模式启用 'eth0' 
route -n show routing table 
route add -net 0/0 gw IP_Gateway configura default gateway 
route add -net 192.168.0.0 netmask 255.255.0.0 gw 192.168.1.1 configure static route to reach network '192.168.0.0/16' 
route del 0/0 gw IP_gateway remove static route 
echo "1" > /proc/sys/net/ipv4/ip_forward activate ip routing 
hostname show hostname of system 
host www.example.com lookup hostname to resolve name to ip address and viceversa(1) 
nslookup www.example.com lookup hostname to resolve name to ip address and viceversa(2) 
ip link show show link status of all interfaces 
mii-tool eth0 show link status of 'eth0' 
ethtool eth0 show statistics of network card 'eth0' 
netstat -tup show all active network connections and their PID 
netstat -tupl show all network services listening on the system and their PID 
tcpdump tcp port 80 show all HTTP traffic 
iwlist scan show wireless networks 
iwconfig eth1 show configuration of a wireless network card 
hostname show hostname 
host www.example.com lookup hostname to resolve name to ip address and viceversa 
nslookup www.example.com lookup hostname to resolve name to ip address and viceversa 
whois www.example.com lookup on Whois database 

GO TOP INDEX ^ 
Microsoft Windows networks (SAMBA) 
nbtscan ip_addr netbios name resolution 
nmblookup -A ip_addr netbios name resolution 
smbclient -L ip_addr/hostname show remote shares of a windows host 
smbget -Rr smb://ip_addr/share like wget can download files from a host windows via smb 
mount -t smbfs -o username=user,password=pass //WinClient/share /mnt/share mount a windows network share
```

##  sftp发送文件：

```
sftp 用户名@ip地址 
ls : 查看连接用户目录
lls：查看本地用户目录
put + 文件名 ：发送文件
get + 文件名 ：得到文件
```

