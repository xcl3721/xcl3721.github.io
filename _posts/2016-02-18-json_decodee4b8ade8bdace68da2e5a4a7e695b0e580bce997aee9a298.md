---
id: 195
title: json_decode中转换大数值问题
date: 2016-02-18T16:47:50+08:00
author: 徐艺洲
layout: post
guid: http://www.fireidea.com/archives/195
permalink: /archives/195
categories:
  - PHP
tags:
  - decode
  - php
  - 大数值
---
<div id="sina_keyword_ad_area2" class="articalContent   newfont_family">
  <div>
    当json内数值如18446744073709551615 这个数值这么大的时候
  </div>
  
  <div>
    json解析后会返回float(1.844674407371E+19)
  </div>
  
  <div>
    这不是我们期望的，好在php5.4+带了一个选项在decode的时候，加上<span STYLE="line-height: 21px;">JSON_BIGINT_AS_STRING</span>
  </div>
  
  <div>
    <span STYLE="line-height: 21px;">大数值会转成string类型<br /></span>json_decode($output,true , 512 , JSON_BIGINT_AS_STRING);
  </div>