---
id: 170
title: android dpi模式与px dip dp sp关系
date: 2013-12-18T13:32:08+08:00
author: 徐艺洲
layout: post
guid: http://www.fireidea.com/archives/170
permalink: /archives/170
categories:
  - Android
  - 经典技巧
tags:
  - android
  - dip
  - dpi模式
  - it
  - px
  - 素材尺寸
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  一般来说android是有多种dpi模式，分别为：<span STYLE="color: rgb(34, 34, 34); font-family: 'Helvetica neue', Helvetica, Arial, sans-serif; font-size: 13px; line-height: 22px;">ldpimdpi hdpi xhdpi</span></p> 
  
  <div>
    <font COLOR="#222222" FACE="Helvetica Neue, Helvetica, Arial, sans-serif" SIZE="2"><span STYLE="line-height: 22px;">在我们制作android的时候res目录下会对这些资源分了以上具体目录</span></font>
  </div>
  
  <div>
    <font COLOR="#222222" FACE="Helvetica Neue, Helvetica, Arial, sans-serif" SIZE="2"><span STYLE="line-height: 22px;"><br /></span></font>
  </div>
  
  <div>
    <div>
      在我们制作android应用的图片资源的时候要注意只有在mdpi模式下1px=1dip ，其他模式对应px的资源大小是不同的如
    </div>
    
    <div>
      <span STYLE="color: rgb(34, 34, 34); font-size: 13px;">mdpi与hdpi是2：3的关系 即2px=3dip在hdpi模式下</span>
    </div>
    
    <p>
      <span STYLE="color: rgb(34, 34, 34); font-family: 'Helvetica neue', Helvetica, Arial, sans-serif; font-size: 13px; line-height: 22px;">mdpi与xhdpi是1：2的关系 即2px=4dip在xhdpi模式下</span><br STYLE="color: rgb(34, 34, 34); font-family: 'Helvetica neue', Helvetica, Arial, sans-serif; font-size: 13px; line-height: 22px;" /><span STYLE="color: rgb(34, 34, 34); font-family: 'Helvetica neue', Helvetica, Arial, sans-serif; font-size: 13px; line-height: 22px;">ldpi与mdpi是3：4的关系 即3px=4dip在ldpi模式下</span></div> 
      
      <div>
      </div>
      
      <div>
        这样就是说，我们做一个图片是10像素宽，在mdpi文件夹下放10像素的，在xhdpi放20像素图片，在hdpi下放30像素，才能保证图片质量
      </div>
      
      <div>
      </div>
      
      <div>
        另外，这个dpi模式是android ROM内固定的，不同的手机分辨率及屏幕制材dpi模式是不同的
      </div>
      
      <div>
      </div>
      
      <div>
        题外话，如果android是mdpi的距离高清还是有距离的……因为实际绘制到屏幕上是缩放过的视频……
      </div>