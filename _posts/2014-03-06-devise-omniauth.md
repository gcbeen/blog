---
layout: post
category: ruby
title: 第三方平台登录-google github weibo 腾讯
tags: [note, rails, beginner, tutorial]
---
{% include JB/setup %}
<h4>第三方平台登录 google github weibo 腾讯</h4>

ruby 1.9.3 rails 3.2.16 devise 3.2.2 omniauth 1.1.4

1.google auth2

install gem 
<pre>
gem 'omniauth-google-oauth2'
bundle
</pre>

设置devise

<pre>
# config/initializers/devise.rb
config.omniauth :google_oauth2, ENV['GOOGLE_OAUTH2_KEY'], ENV['GOOGLE_OAUTH2_SECRET']
</pre>

取得第三方平台的key secret
在`console.developers.google.com`创建项目，并在项目下的`API&auth-Credentials`创建 client id 设置网站地址和回调路径`网站地址/users/auth/google_oauth2/callback`

第三方平台的key secret设置

<pre>
# config/social_keys.yml
development:
  google_oauth2_key: 'your_key'
  google_oauth2_secret: 'your_secret'
test:
  google_oauth2_key: 'your_key'
  google_oauth2_secret: 'your_secret'
production:
  google_oauth2_key: 'your_key'
  google_oauth2_secret: 'your_secret'

#config/application.rb
SOCIAL_KEY = YMAL.load_file("#{Rails.root}/config/social_keys.yml")[Rails.env]
SOCIAL_KEY.each do |k, v|
  ENV[k.upcase] ||= v
end
</pre>

如google openID
设置model

<pre>devise :omniauthable, :omniauth_providers => [:google_oauth2] </pre>
配置路由

添加controller的action

<pre>
def google_oauth2
  omniauth_callback('Google Oauth2')
end
</pre>

2.github

install gem

<pre>
gem 'omniauth-github'
bundle
</pre>

设置devise

<pre>
config.omniauth :github, ENV['GITHUB_KEY'], ENV['GITHUB_SECRET']
</pre>

取得第三方平台的key secret
登录`github.com`在`setting-applications`中创建项目，设置网站地址和回调路径`网站地址/users/auth/github/callback`取得 key, secret

key secret设置

<pre>
development:
  github_key: 'your_key'
  github_secret: 'your_secret'
test:
  github_key: 'your_key'
  github_secret: 'your_secret'
production:
  github_key: 'your_key'
  github_secret: 'your_secret'
</pre>

设置model

<pre>devise :omniauthable, :omniauth_providers => [:github] </pre>
配置路由
omniauth_callbacks_controller.rb添加action
<pre>
def github
  omniauth_callback('Github')
end
</pre>

3.weibo

inatall gem

<pre>
gem 'omniauth-weibo-oauth2'
bundle
</pre>

设置devise

<pre>
config.omniauth :weibo, ENV['WEIBO_KEY'], ENV['WEIBO_SECRET']
</pre>

取得第三方平台的key secret

登录`open.weibo.com/webmaster` 添加网站，填写网站名称，域名(填写一个外网可以访问的)，并将验证方式添加到网站(外网可以访问)的head标签中。创建完成之后在网站信息下取得key secret,并在测试帐号中添加测试帐号，在接口管理授权机制中设置网站地址和回调路径`网站地址/users/auth/weibo/callback`。

key secret设置

<pre>
development:
  weibo_key: 'your_key'
  weibo_secret: 'your_secret'
test:
  weibo_key: 'your_key'
  weibo_secret: 'your_secret'
production:
  weibo_key: 'your_key'
  weibo_secret: 'your_secret'
</pre>

设置model

<pre>devise :omniauthable, :omniauth_providers => [:weibo] </pre>
配置路由
omniauth_callbacks_controller.rb添加action
<pre>
def weibo
  omniauth_callback('Weibo')
end
</pre>

4.腾讯微薄 & QQ

install gem

<pre>
gem 'omniauth-qq'
bundle
</pre>

设置devise

<pre>
config.omniauth :tqq, ENV['TQQ_KEY'], ENV['TQQ_SECRET']
config.omniauth :qq_connect, ENV['QQ_CONNECT_KEY'], ENV['QQ_CONNECT_SECRET']
</pre>

取得第三方平台的key secret

腾讯微薄开放平台
登录`dev.t.qq.com` 进入管理中心添加网页应用，填写应用名称，应用地址(填写一个外网可以访问的)，创建完在应用汇总取得key, secret。

腾讯QQ
登录`connect.qq.com`在管理中心创建应用，填写网站名称，网站地址(外网可访问)，回调地址`网站地址`，并将验证方式添加到网站(外网可以访问)的head标签中。创建完成之后在网站信息下取得key secret,并在协作者测试帐号中添加测试帐号。

key secret设置

<pre>
development:
  tqq_key: 'your_key'
  tqq_secret: 'your_secret'
  qq_connect_key: 'your_key'
  qq_connect_secret: 'your_secret'
test:
  tqq_key: 'your_key'
  tqq_secret: 'your_secret'
  qq_connect_key: 'your_key'
  qq_connect_secret: 'your_secret'
production:
  tqq_key: 'your_key'
  tqq_secret: 'your_secret'
  qq_connect_key: 'your_key'
  qq_connect_secret: 'your_secret'
</pre>

设置model

<pre>devise :omniauthable, :omniauth_providers => [:tqq, :qq_connect] </pre>
配置路由
omniauth_callbacks_controller.rb添加action
<pre>
def tqq
  omniauth_callback('TQQ')
end

def qq_connect
  omniauth_callback('QQ')
end
</pre>
