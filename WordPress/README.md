# 腾讯云CentOS 7上搭建WordPress

## 1.安装Apache Web服务器

使用yum工具安装：

 

```c
sudo yum install httpd
```

sudo命令获得了root用户的执行权限，因此需要验证用户口令。
 安装完成之后，启动Apache Web服务器：

 

```
sudo systemctl start httpd.service
```

测试Apache服务器是否成功运行，找到腾讯云实例的公有IP地址(your_cvm_ip)，在你本地主机的浏览器上输入：

 

```
http://your_cvm_ip/
```

![15](../image/15.png)

## 2.安装MySQL

CentOS 7.2的yum源中并末包含MySQL，需要其他方式手动安装。因此，我们采用MySQL数据库的开源分支MariaDB作为替代。
 安装MariaDB：

 

```
sudo yum install mariadb-server mariadb
```

安装好之后，启动mariadb：

 

```
sudo systemctl start mariadb
```

随后，运行简单的安全脚本以移除潜在的安全风险，启动交互脚本：

 

```
sudo mysql_secure_installation
```

设置相应的root访问密码以及相关的设置(都选择Y)。
 最后设置开机启动MariaDB：

 

```
sudo systemctl enable mariadb.service
```

## 3.安装PHP

PHP是一种网页开发语言，能够运行脚本，连接MySQL数据库，并显示动态网页内容。
默认的PHP版本太低（PHP 5.4.16），无法支持最新的WordPress（笔者写作时为5.2.2），因此需要手动安装PHP较新的版本(PHP 7.2)。
PHP 7.x包在许多仓库中都包含，这里我们使用Remi仓库，而Remi仓库依赖于EPEL仓库，因此首先启用这两个仓库
sudo yum install epel-release yum-utils
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm



接着启用PHP 7.2 Remi仓库：

 

```
sudo yum-config-manager --enable remi-php72
```

安装PHP以及php-mysql

 

```
sudo yum install php php-mysql
```

查看安装的php版本：

 

```
php -v
```

安装之后，重启Apache服务器以支持PHP：

 

```
sudo systemctl restart httpd.service
```

## 4.安装PHP模块

为了更好的运行PHP，需要启动PHP附加模块，使用如下命令可以查看可用模块：

 

```
yum search php-
```

![16](../image/16.png)

这里先行安装php-fpm(PHP FastCGI Process Manager)和php-gd(A module for PHP applications for using the gd graphics library)，WordPress使用php-gd进行图片的缩放。

 

```
sudo yum install php-fpm php-gd
```

![17](../image/17.png)

重启Apache服务：

 

```
sudo service httpd restart
```

![18](../image/18.png)

至此，LAMP环境已经安装成功，接下来测试PHP。

## 5.测试PHP

这里我们利用一个简单的信息显示页面（info.php）测试PHP。创建info.php并将其置于Web服务的根目录（/var/www/html/）：

 

```
sudo vim /var/www/html/info.php
```

该命令使用vim在/var/www/html/处创建一个空白文件info.php，我们添加如下内容：

 

```
<?php phpinfo(); ?>
```

![19](../image/19.png)

完成之后，使用刚才获取的cvm的IP地址，在你的本地主机的浏览器中输入:

 

```
http://your_cvm_ip/info.php
```

![20](../image/20.png)

## 6.安装WordPress以及完成相关配置

### (1)为WordPress创建一个MySQL数据库

首先以root用户登录MySQL数据库：

 

```
mysql -u root -p
```

![21](../image/21.png)

首先为WordPress创建一个新的数据库：

 

```
CREATE DATABASE wordpress;
```

![22](../image/22.png)

注意：MySQL的语句都以分号结尾。

接着为WordPress创建一个独立的MySQL用户：

 

```
CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';
```

![23](../image/23.png)

"wordpressuser”和“password”使用你自定义的用户名和密码。

授权给wordpressuser用户访问数据库的权限：

 

```
GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';
```

![24](../image/24.png)

随后刷新MySQL的权限：

 

```
FLUSH PRIVILEGES;
```

![25](../image/25.png)