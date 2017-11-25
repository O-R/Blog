
` vim  /etc/postfix/main.cf`
（Postfix主要配置文件，再其末尾添加以下配置）

```
#指定默认的邮件发送服务器
relayhost = [smtp.163.com]:25
#激活sasl认证
smtp_sasl_auth_enable = yes
#指定sasl密码配置文件
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
#非匿名登录
smtp_sasl_security_options = noanonymous
#指定认证类型 （提示：需要yum安装cyrus-sasl-*组件，否则发邮件时会报错：no mechanism available）
smtp_sasl_type = cyrus
#linux用户与发件人的对应关系配置文件
sender_canonical_maps = hash:/etc/postfix/sender_canonical 
```
 
`vim /etc/postfix/sasl_passwd`
（邮箱账号和密码文件，每行一个。 创建好后需要使用postmap命令使配置文件生效）
[smtp.163.com]:25   发件邮箱:邮箱密码
`postmap /etc/postfix/sasl_passwd`
 
`vim /etc/postfix/sender_canonical`
（linux用户和发件人对应关系，每行一个）
root   发件邮箱
`postmap /etc/postfix/sender_canonical`
 
重启Postfix：
`service postfix restart`
 
尝试发送邮件：
`echo "hello world"  |mail -s  test hung_z_h@qq.com`
(可以用 mailq 命令查看发送队列，清空mailq队列 ： postsuper -d ALL  )
