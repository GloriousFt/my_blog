---
layout: post
title: CentOS的LAMP开发环境配置说明
date: 2016-02-16 07:56:17.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- Apache
- MySQL
- PHP
meta:
  _edit_last: '1'
---
<p><span style="color: #e33737;">OS：CentOS 6.7</span><b><br />
</b></p>
<p><b style="color: #e33737;">Apache 2.2.15 </b></p>
<p>CentOS 自带了apache2服务，它的httpd service就是apache服务。可以用yum来查看httpd安装情况。</p>
<pre style="color: #4c4c4c;"><code class="bash hljs" style="color: #4d4d4c;">yum list installed | grep httpd</code></pre>
<p>安装后在 "/etc/httpd/conf"文件夹复制httpd.conf做备份，然后编辑httpd.conf。</p>
<pre style="color: #4c4c4c;"><code class="bash hljs" style="color: #4d4d4c;"><span class="hljs-built_in" style="color: #f5871f;">cd</span> /etc/httpd/conf
cp httpd.conf httpd.conf.backup
sudo vim httpd.conf</code></pre>
<p>这个是服务器的配置文件，要更改一些配置。</p>
<p>先更改DocumentRoot，这个定位你的Web文件夹，也就是localhost指向哪里。</p>
<!--more-->
<pre style="color: #4c4c4c;"><code class="bash hljs" style="color: #4d4d4c;">DocumentRoot <span class="hljs-string" style="color: #718c00;">"/home/.."</span> //Web目录位置
&lt;Directory <span class="hljs-string" style="color: #718c00;">"/home/.."</span>&gt;</code></pre>
<pre style="color: #4c4c4c;"><code>    RewriteEngine on

    # If a directory or a file exists, use the request directly
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d

    # Otherwise forward the request to index.php
    RewriteRule . index.php</code></pre>
<p>上面的要加到&lt;Directory&gt;标签下，是开启重定向引擎。AllowOverride 也要改成All。</p>
<pre style="color: #4c4c4c;"><code>LoadModule rewrite_module modules/mod_rewrite.so
</code></pre>
<p>找到这个，确保这个rewrite module在配置文件中。后面美化URL会用到。</p>
<p>还有一些其它的module，可以按需求添加。后面用到的php模块也是在这里添加的。</p>
<pre style="color: #4c4c4c;"><code># Load config files from the config directory "/etc/httpd/conf.d".
Include conf.d/*.conf
</code></pre>
<p>这个是导入配置文件的，一些模块化的配置文件可以这样导入，比如php配置文件。打开conf.d文件夹,如果安装了php这里就会有一个php.conf。同样先做好备份。</p>
<p>还有修改这一条。</p>
<pre style="color: #4c4c4c;"><code class="bash hljs" style="color: #4d4d4c;">DirectoryIndex index.php index.html index.html.var
</code></pre>
<p>如果要修改ServerName。</p>
<pre style="color: #4c4c4c;"><code>ServerName www.domainname.com:80 //改成自己服务器域名</code></pre>
<p>有时候还要隐藏一些信息。</p>
<pre style="color: #4c4c4c;"><code>ServerTokens Prod // Prod表明实际的产品环境中错误页不显示OS信息
ServerSignature Off // Off表明错误页不显示服务器签名</code></pre>
<p>最后添加一个默认字符集（如果配置中没有的话）。</p>
<pre style="color: #4c4c4c;"><code>AddDefaultCharset UTF-8
</code></pre>
<p>保存文件，退出后重启httpd就好。</p>
<pre style="color: #4c4c4c;"><code>sudo service httpd restart</code></pre>
<p>要想设置为开机启动的话，运行下面的指令。</p>
<pre style="color: #4c4c4c;"><code>chkconfig httpd on</code></pre>
<p>&nbsp;</p>
<p><b style="color: #e33737;">php 5.5.31</b></p>
<p>php的安装可以选择用yum安装，不过默认的好像是版本较低的，要想装指定版本可以这样。</p>
<p>先查看是否已安装php。</p>
<pre style="color: #4c4c4c;"><code>yum list installed | grep php</code></pre>
<p>如果不是自己想要的版本就remove掉相关项。</p>
<pre style="color: #4c4c4c;"><code>yum remove php.x86_64 ...</code></pre>
<p>然后运行这个。</p>
<pre style="color: #4c4c4c;"><code>rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
</code></pre>
<p>这个是centos6版本执行的指令，如果是5版本则把el6改成el5。</p>
<p>然后再用yum安装就可以了。</p>
<pre style="color: #4c4c4c;"><code>yum install php55w.x86_64 php55w-cli.x86_64 php55w-common.x86_64 php55w-gd.x86_64 php55w-ldap.x86_64 php55w-mbstring.x86_64 php55w-mcrypt.x86_64 php55w-mysql.x86_64 php55w-pdo.x86_64
</code></pre>
<p>这是php5.5版本的相关php软件，若是别的版本则改相应的版本号即可，如php56。</p>
<p>安装成功后，进入/etc，备份php.ini，修改php配置文件。</p>
<p>修改一些需要的配置即可，如expose_php改成Off，以免暴露php相关信息。</p>
<p>此时用浏览器打开配置的web目录（localhost），在目录里面新建一个php文件，随便写个phpinfo();应该能够看到了。记得每次配置后载入配置要重启服务。Apache对php的配置都在/etc/httpd/conf.d中。</p>
<p><b style="color: #e33737;">mysql 5.5.48</b></p>
<p>mysql同样可以用yum安装，但是依旧版本可能过低，可能连utf8mb4都不认识。可以用安装php的方法。</p>
<pre style="color: #4c4c4c;"><code>yum list installed | grep mysql</code></pre>
<p>如果有旧的版本就先删除。</p>
<pre style="color: #4c4c4c;"><code>yum remove mysql mysql-*
</code></pre>
<p>然后运行</p>
<pre style="color: #4c4c4c;"><code>rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm

rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm</code></pre>
<pre style="color: #4c4c4c;"><code><span style="color: black;">yum </span><span style="color: gray;">--enablerepo=remi,remi-test install mysql mysql-server</span></code></pre>
<p>这样就安装了新的版本的mysql。</p>
<p>然后启动并配置开机启动项。</p>
<pre style="color: #4c4c4c;"><code>/etc/init.d/mysqld start
chkconfig --level 235 mysqld on</code></pre>
<p>之后就该配置mysql了，先运行mysql -u root -p，然后输入root的密码进行第一次的配置。若是要新建用户，则可以新建用户。</p>
