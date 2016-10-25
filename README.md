# PHP_questions

解决编译apache出现的问题：configure: error: APR not found . Please read the documentation
2012-04-17 11:45:27
标签：编译 ./configure apache
原创作品，允许转载，转载时请务必以超链接形式标明文章 原始出处 、作者信息和本声明。否则将追究法律责任。http://xtony.blog.51cto.com/3964396/836508
今日编译apache时出错：
#./configure --prefix……检查编辑环境时出现：
checking for APR... no
configure: error: APR not found .  Please read the documentation
解决办法：
1.下载所需软件包：
wget http://archive.apache.org/dist/apr/apr-1.4.5.tar.gz  
wget http://archive.apache.org/dist/apr/apr-util-1.3.12.tar.gz  
wget http://jaist.dl.sourceforge.net/project/pcre/pcre/8.10/pcre-8.10.zip   
2.编译安装：
yum remove apr-util-devel apr apr-util-mysql apr-docs apr-devel apr-util apr-util-docs 
具体步骤如下:
  a:解决apr not found问题>>>>>>
[root@xt test]# tar -zxf apr-1.4.5.tar.gz  
[root@xt test]# cd  apr-1.4.5  
[root@xt apr-1.4.5]# ./configure --prefix=/usr/local/apr  
[root@xt apr-1.4.5]# make && make install  
 
  b:解决APR-util not found问题>>>>
[root@xt test]# tar -zxf apr-util-1.3.12.tar.gz  
[root@xt test]# cd apr-util-1.3.12  
[root@xt apr-util-1.3.12]# ./configure --prefix=/usr/local/apr-util -with- apr=/usr/local/apr/bin/apr-1-config  
[root@xt apr-util-1.3.12]# make && make install 

  c:解决pcre问题>>>>>>>>>
[root@xt test]#unzip -o pcre-8.10.zip  
[root@xt test]#cd pcre-8.10  
[root@xt pcre-8.10]#./configure --prefix=/usr/local/pcre  
[root@xt pcre-8.10]#make && make install 
4.最后编译Apache时加上：
--with-apr=/usr/local/apr \
--with-apr-util=/usr/local/apr-util/ \
 --with-pcre=/usr/local/pcre
成功编译完成~


安装libxml2
tar zxvf libxml2-2.6.32.tar.gz 
cd libxml2-2.6.32
./configure --prefix=/usr/local/libxml2 
make
make install

安装php5
./configure --prefix=/usr/local/php --with-mysql=/usr/local/mysql/mysql-5.7.13-linux-glibc2.5-x86_64 --with-apxs2=/usr/local/apache2/bin/apxs --with-libxml-dir=/usr/local/libxml2


配置php的时候出现以下问题解决方案

checking for MySQL support... yes
checking for specified location of the MySQL UNIX socket...no
checking for MySQL UNIX socket location... no
configure: error: Cannot find libmysqlclient_r under/usr/local/mysql. Note that the MySQL client library is not bundledanymore!
其实这跟PHP没有关系，那是因为在编译APACHE的时候，使用--with-mpm模块，所以就必须在编译MYSQL的时候加上--enable-thread-safe-client.参数
编译之前，先处理一下mysql的库，默认查找libmysqlclient_r.so，可是mysql默认为libmysqlclient.so，内容完全一样，做个链接即可
# cd /usr/local/mysql/lib/mysql/ 
# ln -s libmysqlclient.so.15.0.0 libmysqlclient_r.so


make
make install


去/usr/local/apache2/conf/httpd.conf中看看是否有
LoadModule php5_module        modules/libphp5.so


# sudo /usr/local/apache2/bin/apachectl start
# service apache2 restart

AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 0.0.1.174. Set the 'ServerName' directive globally to suppress this message
(98)Address already in use: AH00072: make_sock: could not bind to address [::]:80
(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:80
no listening sockets available, shutting down
AH00015: Unable to open logs

# sudo netstat -tulpn | grep :80
# sudo kill PID
# 重复start

写一个php测试页hello.php，放到apache2/htdocs中。
网页打开 http://localhost/hello.php


sudo gedit /usr/local/apache2/conf/httpd.conf
添加	AddType application/x-httpd-php .php .phtml
    	AddType application/x-httpd-php-source .phps

	
	ServerName localhost


Fatal error: Class 'mysqli' not found in /media/user01/2381911d-3176-4cb0-bb69-2d9da346752c/PHP/workspace/qwer/mysql_connect.php on line 7
在linux中解决办法稍加复杂一点，因为都是命令模式了
 代码如下 	复制代码


cd php-5.2.8 //在这下面有个ext文件夹里有mysqli
cd ext/mysqli

linux下将源码文件编译应该都有configure吧，可这里没有，在我们已经安装php后，php5这个文件夹里的命令目录bin里有一个文件叫phpize，用它就可以生成configure文件啦，继续
 代码如下 	复制代码

/usr/local/php5/bin/phpize //这里回车运行，执行完后就出了configure文件，
./configure –prefix=/usr/local/mysqli –with-php-config=/usr/local/php5/bin/php-config –with-mysqli=/usr/local/mysql/bin/mysql_config

直接回车，不出问题就继续。。
 代码如下 	复制代码

make
make test
make install //(不出错就会显示Installing shared extensions: /usr/local/php5/lib/php/extensions/no-debug-non-zts-20041030/)

现在mysqli.so文件就出来啦。。

直接在php.ini里把这个文件加载就OK啦。。
 代码如下 	复制代码

extension_dir=”/usr/local/php5/ext”
extension=mysqli.so

把上面第一行找到修改成这个,然后再把第二行加入。。wq保存退出。

把mysqli.so这个文件cp到/usr/local/php5/ext下

Configuring for:
PHP Api Version:         20131106
Zend Module Api No:      20131226
Zend Extension Api No:   220131226
Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.

sudo apt-get install autoconf

./configure --prefix=/usr/local/mysqli --with-php-config=/usr/local/php/bin/php-config --with-mysqli=/usr/local/mysql/mysql-5.7.13-linux-glibc2.5-x86_64/bin/mysql_config

我的解决方法：
直接修改源码，把找不到的那个头文件的路径修改为绝对路径，编译通过了，也能正常使用
复制内容到剪贴板
代码:
# pwd
/home/neo/tools/php-5.5.28/ext/mysqli
# vim mysqli_api.c
把第36行的
#include "ext/mysqlnd/mysql_float_to_double.h"
修改为
#include "/home/neo/tools/php-5.5.28/ext/mysqlnd/mysql_float_to_double.h"


