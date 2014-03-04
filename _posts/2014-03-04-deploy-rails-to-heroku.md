---
layout: post
category: ruby
title: 将rails部署到heroku
tags: [note, ruby, beginner, tutorial]
---
{% include JB/setup %}
<h4>将rails部署到heroku</h4>

ubuntu 12.04 rvm 1.20.12 ruby 1.9.3 rails 3.2.16 unicorn 4.7.0

1.安装heroku toolbelt

<pre>
wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
</pre>

2.登录添加公钥

<pre>
heroku login
heroku keys:add ~/.ssh/id_rsa.pub
</pre>

3.使用pg数据库
<pre>
# install pg
sudo apt-get install postgresql postgresql-contrib
# phpPgAdmin
download phpPgAdmin
加压到/var/www/

# Gemfile
gem 'pg'

bundle
</pre>

设置database
<pre>
# config/database.yml

development: &default
  adapter: postgresql
  encoding: unicode
  host: localhost
  database: app_name_development
  username: username
  password: 'password'
  pool: 5
  timeout: 5000

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.

test:
  <<: *default
  database: app_name_test


production:
  <<: *default
  database: app_name_production
</pre>

4.数据库迁移

<pre>
rake db:create
rake db:migrate
</pre>

5. precompile assets

<pre>
RAILS_ENV=production rake db:create
RAILS_ENV=production bundle exec rake assets:precompile
</pre>

6.使用unicorn

<pre>
# Gemfile
gem 'unicorn'
bundle

# Procfile
web: bundle exec unicorn -p $PORT -E $RACK_ENV -c ./config/unicorn.rb

# config/unicorn.rb

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

</pre>

7.提交到heroku
<pre>
heroku create
git remote -v
git remote add [name] [url]
git push [name] master
</pre>

8.数据库迁移

<pre>
heroku run rake db:crate
heroku run rake db:migrate
</pre>
