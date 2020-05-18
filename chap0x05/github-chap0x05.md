## Exp5 WEB服务器搭建

### 1.实验环境
* Ubuntu 18.04.4 Server 64bit
  
   Host-Only:192.168.56.101
* nginx
* verynginx
* wordpress
* DVWA


### 3.实验过程
#### step1.安装并配置nginx
* `sudo apt-get update`
* `sudo apt-get install nginx`
* `sudo nginx -t`
* `sudo vim /etc/nginx/sites-enabled/default`
修改监听端口为80  并保存退出
* `sudo systemctl start nginx`
  
运行nginx,可通过ip:80访问nginx;

  **[PROS]**

执行'sudo nginx -t'报错'command not found'

**[SLOS]**

在root权限下：vim /etc/profile 在页末添加：PATH=$PATH:/home/nginx/nginxssl/sbin
 和export PATH

#### step2.安装并配置verynginx
* `git clone https://github.com/alexazhou/VeryNginx.git`
  
* `cd VeryNginx`
  
*  `sudo python3 install.py install`
  
*  `sudo vim /opt/verynginx/openresty/nginx/conf/nginx.conf`修改user www-data listen ：8080
  
*   `sudo verynginx`
  
运行verynginx,在windows主机 host文件中添加一条对应的DNS解析192.168.0.103 vn.sec.cuc.edu.cn，即可使用域名访问verynginx。(sam文件改起来真真麻烦~)

#### step3.安装与配置MYSQL\PHP
* `sudo apt install mysql-server`安装mysql;
  
* `sudo add-apt-repository universe sudo apt install php-fpm php-mysql`安装PHP；
  
* `sudo vim /etc/nginx/sites-enabled/default`
   
    `location ~ \.php$ {
         include snippets/fastcgi-php.conf;
          fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
     }`
修改nginx配置PHP-FPM进程的反向代理配置在nginx服务器上 保存退出

* `sudo systemctl restart nginx1`重新启动nginx使配置生效

#### step4.安装与配置WordPress
* `PUT C:\Users\Administrator\Desktop\wordpress.zip`利用psftp将本机下载的wordpress安装包传到虚拟机内；(虚拟机下得太慢惹~~从同学那里得到启发~)
  
* `unzip wordpress.zip;`
  
* `sudo mkdir /var/www/html/wp.sec.cuc.edu.cn;`
 
* `sudo cp wordpress /var/www/html/wp.sec.cuc.edu.cn`将解压后的wordpress移到指定路径
  
* 新建数据库以便wordpress使用
`sudo apt install mysql-server`

`CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci`

`GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';`

`FLUSH PRIVILEGES;`

`EXIT;`
* 安装php扩展
  
`sudo apt update`

`sudo apt install php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip`

`sudo systemctl restart php7.2-fpm`
* 配置nginx

`sudo vim /etc/nginx/sites-enabled/default`

将网站根站点修改为wordpress的安装目录 并保存退出
`root /var/www/html/wp.sec.cuc.edu.cn;`

在windows主机 host文件中添加一条对应的DNS解析169.254.134.150 wp.sec.cuc.edu.cn 使用url wp.sec.cuc.edu.cn/wordpress/wp-admin/ 访问wordpress进行配置,选择语言，然后连接数据库,设置完wordpress用户名密码即可访问wordpress网站。

#### step5.安装与配置DVWA
- 安装DVWA

  - 安装步骤

    - 首先下载DVWA，并移动到指定文件夹中,执行完毕后  `/var/www/html`文件夹下会有一个DVWA的子文件夹。

      ```bash
      sudo git clone https://github.com/ethicalhack3r/DVWA /tmp/DVWA
      sudo mv /tmp/DVWA /var/www/html
      ```

    - 重命名DVWA中的 **/config/config.inc.php.dist** **/config/config.inc.php**

      ```bash
      sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
      ```

    - 在mysql为DVWA新建一个用户名, 修改DVWA中的配置,用于连接mysql数据库

      ```bash
       CREATE DATABASE dvwa DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
      
       GRANT ALL ON dvwa.* TO 'dvwauser'@'localhost' IDENTIFIED BY 'p@ssw0rd';
       FLUSH PRIVILEGES;
       EXIT;
      sudo systemctl restart mysql
       
       sudo vim /var/www/html/DVWA/config/config.inc.php
       
       $_DVWA[ 'db_server' ]   = '127.0.0.1';
       $_DVWA[ 'db_database' ] = 'dvwa';
       $_DVWA[ 'db_user' ]     = 'dvwauser';
       $_DVWA[ 'db_password' ] = 'p@ssw0rd';
      ```
      ![过程展示](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x05/images/3.png)

    - 修改php的配置

      ```bash

      sudo vim /etc/php/7.2/fpm/php.ini
      
      allow_url_include = on
      allow_url_fopen = on
      safe_mode = off
      magic_quotes_gpc = off
      display_errors = off
      

      sudo systemctl restart php7.2-fpm
      ```
        (这里要用到vim的字符串查找)
    - 设置DVWA文件夹访问权限

      ```bash
      sudo chown -R www-data.www-data /var/www/html/DVWA
      ```

    - 配置nginx 5566端口监听DVWA的访问

      ```bash
      sudo vim /etc/nginx/sites-enabled/default
      
      server {
              listen 5566；
              root /var/www/html/DVWA;
              index index.html setup.php index.htm index.php index.nginx-debian.html;
               location / {
                      try_files $uri $uri/ =404;
                }
              location ~ \.php$ {
                      include snippets/fastcgi-php.conf;
                      fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
              }
          }
      sudo systemctl restart nginx
      ```

    - 此时访问dvwa，在主机hosts文件中增加一条对应的dns解析 `169.254.134.150 dvwa.sec.cuc.edu.cn`,并点击`setup.php`页面下方的**Create/Reset Database**生成需要使用的数据库。如果数据库连接成功，页面会直接重定向到登录页面。
    - ![主机hosts文件](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x05/images/2.png)

#### 实验要求


* 在一台主机（虚拟机）上同时配置Nginx和VeryNginx  


![Nginx与verynginx配置](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x05/images/4.png)  

* 使用Wordpress搭建的站点对外提供访问的地址为： http://wp.sec.cuc.edu.cn  

* 使用DVWA搭建的站点对外提供访问的地址为： http://dvwa.sec.cuc.edu.cn  

#### 关于作业的说明
对这次实验部分理解不够透彻以及时间安排问题，实验没能完全做完（也有虚拟机网卡配置的问题），是我个人的失误。由于作业提交期限将至，只能交一个半半成品，接受老师的指正与批评，并且我会抓紧时间补交一份完整的实验报告，希望老师谅解!
#### 实验参考
-https://github.com/CUCCS/linux-2019-jackcily/pull/5/commits/

-https://github.com/CUCCS/linux-2019-jackcily/pull/5/commits/

-https://blog.csdn.net/m1913843179/article/details/103846332