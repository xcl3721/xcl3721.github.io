---
id: 221
title: 'BDB0126 mmap: Invalid argument BerkeleyDb报错原因及解决'
date: 2018-10-10T14:06:57+08:00
author: 徐艺洲
layout: revision
guid: http://www.fireidea.com/archives/221
permalink: /archives/221
---
<div id="sina_keyword_ad_area2" class="articalContent   newfont_family">
  用虚拟机下弄Bdb结果报错了。。。。无语。。后来原因竟然是因为我用vagrant挂载了mac上的一个目录，然后这个目录下开发。。。虚拟机的底层对这个挂载的服务器不支持mmap导致失败。。拷贝进去就好了。。。怨念。。。</p> 
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
    相关问题：
  </div>
  
  <div>
    http://stackoverflow.com/questions/18420473/invalid-argument-for-read-write-mmap
  </div>
  
  <div>
  </div>
  
  <div>
    https://community.oracle.com/message/12689724#12689724
  </div>