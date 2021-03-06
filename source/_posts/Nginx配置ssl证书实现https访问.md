---
title: Nginx配置ssl证书实现https访问
date: 2020-07-02 22:33:44
categories: notes
tags: Nginx
---

> - 进入nginx安装目录，修改配置文件信息

~~~
server {    

   listen 443;#监听443端口（https默认端口）

   server_name www.xxx.com; #填写绑定证书的域名

   ssl on;

   ssl_certificate xxx.crt;     
   
   #填写你的证书所在的位置(将证书上传到服务器文件夹，此处配置绝对路径,如/etc/nginx/xxx.crt)

   ssl_certificate_key xxx.key; 
   
   #填写你的key所在的位置(将证书上传到服务器文件夹，此处配置绝对路径,如/etc/nginx/xxx.key)

   #如遇到证书上传失败,请查看错误信息是否提示权限不足，只要在服务端临时更改一下权限即可，不展开说明。

   ssl_session_timeout 5m;

   ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置

   ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;  #按照这个套件配置

   ssl_prefer_server_ciphers on;

   location / {

     root  xxx ; #填写你的你的站点目录

   }

}
 server {

    listen 80;

    server_name  www.xxx.com;   #填写绑定证书的域名

    rewrite ^(.*)$ https://${server_name}$1 permanent;  #将http转到https
    
    location / {

     root  xxx ; #填写你的你的站点目录

   }

 }
 ~~~
<!-- more -->

>- 配置完成后，在nginx安装目录下
~~~
nginx -t
nginx -s reload
~~~

> 总结一下遇到的问题：
- 阿里云服务器没有开放443端口，需要到实例的安全组中添加。
- 添加之后，仍然无法使用https访问，发现访问时没有返回任何信息，怀疑仍然是端口没放开。
- 由于服务器是新的，还未安装和配置防火墙，于是尝试安装，并在防火墙策略中开放443端口。

~~~
sudo apt-get install firewalld
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --reload
~~~

> 至此可以用https协议访问，下面配置重定向访问，将http请求定向为https。

- 在80端口的server块增加重定向配置行

~~~
rewrite ^(.*)$ https://${server_name}$1 permanent;
~~~

- 使用http协议访问域名，发现没有返回任何信息，仍然怀疑是端口没放开

~~~
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
~~~

阿里云服务器在没有安装内置防火墙时，安全组80端口默认开放。
系统安装了防火墙后，若没有指定80端口开放，那么即使阿里云安全组80端口开放了，仍然无效。
导致此配置下http无法访问，进而无法重定向。
这里经验不足，以为配置有误，方向走偏。

> - <a href = "https://cloud.tencent.com/product/ssl?fromSource=gwzcw.2603076.2603076.2603076&utm_medium=cpc&utm_id=gwzcw.2603076.2603076.2603076">ssl证书: 腾讯云免费申请  </a>
> - <a href = "https://www.aliyun.com/?utm_content=se_1000301881">服务器: 阿里云服务器 </a>