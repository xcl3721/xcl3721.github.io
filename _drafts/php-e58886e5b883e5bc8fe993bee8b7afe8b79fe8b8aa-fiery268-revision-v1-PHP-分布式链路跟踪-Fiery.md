---
id: 269
title: PHP 分布式链路跟踪 Fiery
date: 2018-10-10T14:20:16+08:00
author: 徐艺洲
layout: revision
guid: http://www.fireidea.com/archives/269
permalink: /archives/269
---
Fiery 是一款为PHP提供服务的性能跟踪监控系统，能够提高线上业务排查故障效率，帮助开发人员改善系统性能及完善系统。方便的查看线上多依赖服务接口的调用关系，响应性能，回放请求过程，参数，系统异常、性能统计，部署简单方便，开箱即用。

## 模块及功能<figure class="wp-block-image">

![](https://wx4.sinaimg.cn/large/54ef3989ly1ffyjeni9p7j20c30gugmb.jpg) </figure> 

​

  * 埋点库: RagnarSDK提供PHP侵入式性能埋点库，集成到 项目入口、Curl类及Mysql基础类即可
  * 日志收集: LogPusher服务负责监控收集埋点库产生的日志更新，并推送到服务端
  * 统计存储服务: Server接收日志，并对日志进行整理、存储、汇总、索引、统计分析功能

## 最低配置要求

  * PHP 5.3 or later with bcmath
  * 目前仅支持64位 UTF8编码PHP项目
  * Linux, OS X 、Windows
  * 内存: 2G+
  * Java 8 Runtime

## 服务端安装步骤

  1. 下载并安装 <a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" target="_blank" rel="noreferrer noopener">Java 8 Runtime</a>
  2. 下载Fiery最新的 Fiery <a target="_blank" rel="noreferrer noopener">Release page</a> jar包
  3. 在jar所在目录创建文件夹 mkdir logs index db
  4. 通过以下命令启动主服务:

nohup java -XX:-MaxFDLimit -Xms3750m -Xmx3750m -XX:ReservedCodeCacheSize=240m -XX:+UseCompressedOops -jar ragnarserver-0.5.1-SNAPSHOT.jar -type server &#8211;server.port=9090 &

  1. 服务启动后 浏览器访问地址： <a href="http://127.0.0.1:9090/ragnar/" target="_blank" rel="noreferrer noopener">http://127.0.0.1:9090/ragnar/</a> 即可

## PHP项目埋点库埋点介绍

  * <a href="https://github.com/weiboad/fiery/blob/master/README_CN.md" target="_blank" rel="noreferrer noopener">埋点库相关介绍</a>

## LogPusher 日志收集及推送服务

日志推送服务，可以监控一个目录下所有日志是否有更新，并将内容推送到主服务

nohup java -XX:-MaxFDLimit -Xms128m -Xmx450m -XX:ReservedCodeCacheSize=240m -XX:+UseCompressedOops -jar ragnarserver-0.5.1-SNAPSHOT.jar -type logpush -path [要监控的日志目录] -host 服务器ip及端口[ip:port] -outtime 7 &

<hr class="wp-block-separator" />

## 功能界面介绍

## 调用回放<figure class="wp-block-image">

![](https://wx2.sinaimg.cn/large/54ef3989ly1ffyjf3xjcij219u0qtn32.jpg) </figure> 

​

<blockquote class="wp-block-quote">
</blockquote>

## 最近请求<figure class="wp-block-image">

![](https://wx4.sinaimg.cn/large/54ef3989ly1ffyjfacih6j21050ob434.jpg) </figure> 

​

<blockquote class="wp-block-quote">
</blockquote>

## 性能排行<figure class="wp-block-image">

![](https://wx2.sinaimg.cn/large/54ef3989ly1ffyjfh4bh8j21010oite8.jpg) </figure> 

​

<blockquote class="wp-block-quote">
</blockquote>

## 依赖服务排行

<blockquote class="wp-block-quote">
</blockquote>

## SQL性能统计

<blockquote class="wp-block-quote">
</blockquote>

## 线上故障去重

<blockquote class="wp-block-quote">
</blockquote>

​项目地址：https://github.com/weiboad/fiery/wiki​​​​