---
layout: post
category: php
title: apache2中php的url重写
tags: [note, php, beginner, tutorial]
---
{% include JB/setup %}

<h4>apache2中php url重写</h4>

1.apache2加载mod_rewrite.so模块。

<pre>
cat /etc/apache2/mods-available/rewrite.load
LoadModule rewrite_module /usr/lib/apache2/modules/mod_rewrite.so

a2enmod rewrite
</pre>

2.项目目录下的.htaccess文件

<pre>
&ltIfModule mod_rewrite.c&gt
	RewriteEngine on
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
&lt/IfModule&gt
</pre>

3.apache2项目配置文件

<pre>
	AllowOverride All

	&ltFilesMatch "^\.ht"&gt
		Require all granted
	&lt/FilesMatch&gt
</pre>
