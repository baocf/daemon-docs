//check update package
apt update

//install had updated package 
apt upgrade

//install nginx
apt-get install nginx
service nginx start
service nginx status

//安装一个通用的仓库
apt install software-properties-common

//安装一个新的源
add-apt-repository ppa:pondrej/php
Enter/Enter

 apt update
 看到刚刚新增加的repo

 apt-cache search php7.4
//可以满足NGINX配置PHP的进程管理器
 apt install php7.4-fpm
 service php7.4-fpm start
 service php7.4-fpm status

apt install php7.4-mysql php7.4-curl php7.4-xml php7.4-json php7.4-gd php7.4-mbstring php7.4-zip php7.4-bcmath -y 
service php7.4-fpm restart

//install composer
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
mv composer.phar /usr/local/bin/composer

//配置国内镜像源
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

php.ini
cgi.fix_pathinfo=0 //辅助Laravel文件系统进行相关定位
zib.output_compression=On //配置压缩相关配置为开启
zib.output_compression_level=5
memory_limit=256M //跟进服务器配置调整
upload_max_filesize=8M// default 2M
service php7.4-fpm restart

//创建项目
composer create-project laravel/laravel tingting001


//下载MySQL仓库镜像源
wget https://dev.mysql.com/get/mysql-apt-config_0.8.15-1_all.deb
//解压MySQL包
dpkg -i mysql-apt-config_0.8.15-1_all.deb
apt update
  select Mysql8.0
apt install mysql-server
 input: password
 选择兼容MySQL5m Mode.
 service mysql status


//安装命令提示工具
apt install fish
fish  //进入代码提示工具里面

git clone 远程拉取Laravel repo时 ，访问出现500错误？
1. 原因是在git remote clone时  没有.env;
	需要cp env.example 
2. No Application encryption key has been specified.
    php artisan key generate