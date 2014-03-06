---
layout: post
category: ruby
title: 第三方平台登录-GoogleOpenID
tags: [note, rails, beginner, tutorial]
---
{% include JB/setup %}
<h4>第三方平台登录GoogleOpenID</h4>

ruby 1.9.3 rails 3.2.16 devise 3.2.2 omniauth 1.1.4

1.install omniauth openid

<pre>
# Gemfile
gem 'omniauth-openid'

bundle
</pre>

2.在devise中设置GoogleOpenID

<pre>
# config/initializers/devise.rb
require "openid/store/filesystem"
require 'omniauth-openid'
Devise.setup do |config|
  config.omniauth :open_id, :store => OpenID::Store::Filesystem.new('/tmp'),
    	:name => 'google', :identifier => 'https://www.google.com/accounts/o8/id', :require => 'omniauth-openid'
end
</pre>

3.给users表添加字段

<pre>
rails g migration add_provider_and_uid_on_users
# db/******_add_provider_and_uid_on_users.rb
def up
  add_column :users, :provider, :string
  add_column :users, :uid,      :string
end

def down
  remove_column :users, :provider
  remove_column :users, :uid
end
</pre>

4.在model user中设置 omniauthable并添加from_omniauth方法

<pre>
# app/models/user.rb
devise :omniauthable, :omniauth_providers => [:google]
attr_accessible :provider, uid

def self.from_omniauth(auth)
  if auth.info.email && User.find_by_email(auth.info.email)
    user.provider = auth.provider
    user.uid = auth.uid
	user
  else
    where(provider: auth.provider, uid: auth.uid.to_s).first_or_create do |user|
	  user.provider = auth.provider
	  user.uid = auth.uid
	  user.nickname = auth.info.nickname
	  user.email = auth.info.email
	  user.avatar = auth.info.image
	end
  end
end
</pre>

5.设置路由添加回调的controller

<pre>
config/routes.rb
devise_for :users, controllers: { omniauth_callbacks: 'omniauth_callbacks' }
</pre>

6.编写controller相应方法action

<pre>
# app/controllers/omniauth_callbacks_controller.rb
def google
  omniauth_callback('Google')
end

def omniauth_callback(kind)
  user = User.from_omniauth(env['omniauth.auth'])
  if user.persisted?
    flash[:notice] = I18n.t "devise.omniauth_callbacks.success", :kind => kind
	sign_in_and_redirect user
  else
    session['devise.user_attributes'] = user.attributes
	flash.notice = "认证失败"
	redirect_to new_user_registration_url
  end
end
</pre>

7.编写view页面

<pre>
# app/views/layouts/shared/_omniauth_link.html.erb

&lt%- if devise_mapping.omniauthable? %&gt
  &ltdiv class='box'&gt
  &lth4&gt 授权认证登录&lt/h4&gt
  &lt%- resource_class.omniauth_providers.each do |provider| %&gt
	  &lt%= omniauth_link(resource_name, provider) %&gt
  &lt% end -%&gt
  &lt/div&gt
&lt% end -%&gt
</pre>

8.提供相应的helper

<pre>
# app/helpers/links_helper.rb
module LinksHelper
  def omniauth_link(resource_name, provider)
	str = <<-DOC
	&lta href='#{ omniauth_authorize_path(resource_name, provider) }' class='btn btn-social btn-block btn-#{ t ".#{provider}" }' &gt
	  &lti class='fa fa-#{t ".#{provider}" }'&gt&lt/i&gt#{ t 'omniauth_text', provider_name: t(".#{provider}_text") }
	&lt/a&gt
	DOC
	str.html_safe
  end
end

</pre>
