---
id: 245
title: Gearman Manager安装及碰到的问题
date: 2018-10-10T14:13:27+08:00
author: 徐艺洲
layout: revision
guid: http://www.fireidea.com/archives/245
permalink: /archives/245
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  gearman manager安装的时候由于官方文档不太全，转了几大圈才搞定<br />现在记录下来</p> 
  
  <p>
    安装前需要php安装pecntl<br /> php -m|grep pcntl 命令可查看<br />如果没有任何输出就是没安装
  </p>
  
  <p>
    首先去官方https://github.com/brianlmoon/GearmanManager<br />把他下载下来，解压
  </p>
  
  <p>
    进入目录install执行install.sh<br />会列出一个菜单让你选择那种方式管理worker<br />我选的pecl<br />系统会自动把文件拷贝到几个地方：
  </p>
  
  <p>
    <b>启动脚本保存路径：</b> /usr/local/bin/gearman-manager<br /><b>pid 文件保存路径：</b> /var/run/gearman<br /><b>log日志文件：</b> /var/log/gearman-manager.log<br /><b>配置：</b>/etc/gearman-manager<br /><b>worker文件放置目录</b> 在/etc/gearman-manager/workers/<br /><b>具体的php系统文件在</b> /usr/local/share/gearman-manager<br /><b>服务启动关闭文件在</b> /etc/init.d/gearman-manager
  </p>
  
  <p>
    列出这几个地方是有原因的，因为一会儿修整让他能工作这些目录都要知道……
  </p>
  
  <p>
    安装完毕
  </p>
  
  <p>
    <b>启动gearman manager命令如下</b><br />/etc/init.d/gearman-manager start
  </p>
  
  <p>
    <b>关闭用</b><br />/etc/init.d/gearman-manager stop
  </p>
  
  <p>
    如果启动提示/usr/bin/env:php 找不到指定文件<br />这个时候需要我们去path环境变量内指定php执行文件所在，并且<br />/usr/bin/php<br />保证上面这个目录有php可执行文件，可以拷贝过去或者ln过去
  </p>
  
  <p>
    搞定上面问题后，在worker代码放置目录下放一两个官方指定格式的worker文件进去，启动服务后<br />执行ps -aux|grep gearman-manager<br />如果发现一个都没有，那么很有可能碰到了以下问题
  </p>
  
  <p>
    故障排除解决方法如下<br />&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-<br /><b>故障现象<br />执行 start后会在第一行输出gearmand user not found</b>类似提示<br />那么修改<br />vim /etc/init.d/gearman-manager<br />GEARMANUSER=&#8221;系统内已经有的用户名&#8221;<br />这个是因为服务启动的时候要指定一个用户身份，但是系统没有脚本默认的gearmand这个用户，所以会报错
  </p>
  
  <p>
    如果碰到启动后什么都没有，那么用一下方式解决下<br />&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-<br /><b>故障现象</b>：<br />当停止服务后，用php /usr/local/share/gearman-manager/pecl-manager.php -vvv-c config文件所在路径<br />大量输出<br />php: libgearman/universal.cc:486: gearman_return_tconnection_loop(gearman_universal_st&, constgearman_packet_st&, Check&):Assertion `packet_ptr ==&con->_packet&#8217; failed.<br />这个错误
  </p>
  
  <p>
    <b>解决方法</b><br />vim /usr/local/share/gearman-manager/pecl-manager.php
  </p>
  
  <p>
    注释$thisWorker->addOptions(GEARMAN_WORKER_NON_BLOCKING);这句命令即可<br />这个是因为某bug导致他挂了……暂时这么改……其中php扩展的1.1.1版本已经解决这个问题<br />&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-<br />最后是启动命令，扔这里了<br />/etc/init.d/gearman-manager start -c/etc/gearman-manager/config.ini -h ip:端口,ip2:端口
  </p>