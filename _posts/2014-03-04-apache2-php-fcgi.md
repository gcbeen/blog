---
layout: post
category: ruby
title: 搭建apache2+laravel(php)环境
tags: [note, php, beginner, tutorial]
---
{% include JB/setup %}
<h4>搭建apache2+php环境</h4>

ubuntu 12.04 apache2 2.4.7 laravel4.2(php)

1.安装lavavel

<pre>
composer create-project laravel/laravel project_name

ln -s /project_path /var/www/project_name
sudo chmod -R 0777 app/storage
</pre>

2. enable rewrite module

<pre>
#/etc/apache2/
a2enmod rewrite
</pre>

3.apache2配置

<pre>

&ltVirtualHost *:83&gt

    # The location of our projects public directory.
    DocumentRoot    /var/www/project_name/public

    # Useful logs for debug.
    CustomLog       /var/www/project_name/apache2/out.log common
    ErrorLog        /var/www/project_name/apache2/err.log

    # Rewrites for pretty URLs, better not to rely on .htaccess.
    &ltDirectory /var/www/happy-php/public&gt
        &ltIfModule mod_rewrite.c&gt
			Options Indexes FollowSymLinks
            RewriteEngine On
            RewriteCond %{REQUEST_FILENAME} !-f
			RewriteRule ^(.*)$ index.php/$1 [L]
        &lt/IfModule&gt
    &lt/Directory&gt

&lt/VirtualHost&gt

# terminal
mkdir /project_path/apache2/
touch /project_path/apache2/out.log /project_path/apache2/err.log

</pre>
