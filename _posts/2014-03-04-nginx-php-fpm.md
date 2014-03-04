---
layout: post
category: ruby
title: 搭建nginx+laravel(php)+fpm环境
tags: [note, php, beginner, tutorial]
---
{% include JB/setup %}
<h4>搭建nginx+php+fpm环境</h4>

ubuntu 12.04 nginx 1.1.19 php 5.5.9

1.升级php

<pre>
  sudo add-apt-repository ppa:ondrej/php5 
  sudo apt-get update
  sudo apt-get install php5
  sudo apt-get install php5-fpm
</pre>

2.安装laravel

<pre>
# install composer
php -r "readfile('https://getcomposer.org/installer');" | php
sudo mv composer.phar /usr/bin/composer

composer create-project laravel/laravel project_name
</pre>

3.nginx配置

<pre>
# /project_path/app/config/nginx.conf

server {
        listen   80;

        root /home/been/happy-php/public/;
        index index.php index.html index.htm;

        location / {
             try_files $uri $uri/ /index.php$is_args$args;
        }

        # pass the PHP scripts to FastCGI server listening on /var/run/php5-fpm.sock
        location ~ \.php$ {
                try_files $uri /index.php =404;
                fastcgi_pass unix:/var/run/php5-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}

</pre>

4.php配置

<pre>
# /etc/php5/fpm/php.ini
cgi.fix_pathinfo=0

# /etc/php5/fpm/pool.d/www.conf
listen = /var/run/php5-fpm.sock
</pre>

5.启动 nginx php5-fpm

<pre>
sudo service php5-fpm restart
sudo service nginx restart
</pre>
