---
layout: post
category: ruby
title: 搭建nginx+unicorn+rails环境
tags: [note, ruby, beginner, tutorial]
---
{% include JB/setup %}
<h4>搭建nginx+unicorn+rails环境</h4>

ubuntu 12.04 nginx 1.1.19 rvm 1.20.12 ruby 1.9.3 rails 3.2.16

1.安装nginx

<pre>
sudo apt-get install nginx
</pre>

2.安装rvm

<pre>
curl -sSL https://get.rvm.io | bash -s stable
</pre>

3.安装ruby 

<pre>
rvm install 1.9.3
</pre>

4.安装rails

<pre>
gem install rails -v 3.2.16
</pre>

5.新建rails项目

<pre>
rails new project_name
</pre>

6.安装unicorn

<pre>
# Gemfile
gem 'unicorn'

# terminal
bundle
</pre>

7.nginx配置

<pre>
# project_path/config/nginx.conf

upstream unicorn {
    server unix:/tmp/unicorn.todo.socket fail_timeout=0;
}
 
server {
  listen                80;
  server_name           server_name.com;
  root                  /project_path/public;
 
  try_files $uri/index.html $uri @unicorn;
 
  error_page 500 502 503 504 /500.html;
 
  location @unicorn {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://unicorn;
  }
 
}

# terminal

sudo ln -s /project_path/config/nginx.conf /etc/nginx/sites-enabled/project_name

</pre>

8.unicorn配置

<pre>
# project_path/config/unicorn.rb


# -*- encoding : utf-8 -*-
if ENV["RAILS_ENV"] != 'production'
# config/unicorn.rb # development
  working_directory "/project_path"
  pid "/project_path/tmp/pids/unicorn.pid"
  stderr_path "/project_path/unicorn/err.log"
  stdout_path "/project_path/unicorn/out.log"
   
  listen "/tmp/unicorn.todo.socket"
   
  worker_processes 2
  timeout 30

else
# config/unicorn.rb
  worker_processes Integer(ENV["WEB_CONCURRENCY"] || 3)
  timeout 15
  preload_app true
  
  before_fork do |server, worker|
    Signal.trap 'TERM' do
      puts 'Unicorn master intercepting TERM and sending myself QUIT instead'
      Process.kill 'QUIT', Process.pid
    end
  
    defined?(ActiveRecord::Base) and
      ActiveRecord::Base.connection.disconnect!
  end
  
  after_fork do |server, worker|
    Signal.trap 'TERM' do
      puts 'Unicorn worker intercepting TERM and doing nothing. Wait for master to send QUIT'
    end
  
    defined?(ActiveRecord::Base) and
      ActiveRecord::Base.establish_connection
  end
end

# terminal

mkdir unicorn
touch unicorn/err.log
touch unicorn/out.log

</pre>

9.启动服务

<pre>
touch /project_path/start.sh
    bundle exec unicorn -c config/unicorn.rb -E development -D -p 8080

touch /project_path/stop.sh
    kill -9 `cat ~/rails_projects/insoshi/tmp/pids/unicorn.pid`

chmod +x /project_path/start.sh /project_path/stop.sh

sudo service nginx start
./start.sh # 启动unicorn
</pre>
