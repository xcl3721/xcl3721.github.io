---
id: 172
title: mysql 主从库更新延迟，通过orm解决办法
date: 2014-03-05T14:35:33+08:00
author: 徐艺洲
layout: post
guid: http://www.fireidea.com/archives/172
permalink: /archives/172
categories:
  - 经典技巧
tags:
  - it
  - mysql
  - mysql主从延迟
  - php
  - 架构
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  最近由于库比较不稳定还在集体改版，我碰到一个问题<br />主库更新后，从库两秒才能拿到更新后的数据，比如赞操作点后自动在页面html+1，但是由于读取列表是在从库，如果刷新的快，会看到赞还是没+1之前的数值。</p> 
  
  <p>
    今天跟新青和李伟他俩讨论，新青提供的方式很棒，特此记录。<br />我只需在他基础上集成了一下列表输出操作就解决了这个问题。
  </p>
  
  <p>
    首先更新的时候调用orm的edit更新操作，函数内部使用edit函数分析参数及修改值 并更新单条数据到memcache（以数据库内数据的主键组为key）这时所有的最新数据都会记录到memcache内。
  </p>
  
  <p>
    调用获取单条操作的时候直接回自动从memcache走，如果没有再查数据库。
  </p>
  
  <p>
    如果做列表查询时候使用selectpkid（只输出表主键），然后根据id再调用获取单条数据的接口从memcache内拿数据。虽然浪费点php和内存，但是从某个角度来说这个速度很可观。也解决了主从同步延迟问题。
  </p>