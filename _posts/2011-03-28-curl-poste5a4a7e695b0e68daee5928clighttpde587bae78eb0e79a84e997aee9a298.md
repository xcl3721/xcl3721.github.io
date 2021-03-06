---
id: 124
title: CURL POST大数据和lighttpd出现的问题
date: 2011-03-28T15:39:01+08:00
author: 徐艺洲
layout: post
guid: http://www.fireidea.com/archives/124
permalink: /archives/124
categories:
  - PHP
tags:
  - 杂谈
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  <div>
    <ul>
      <li>
        作者: laruence(<a HREF="http://www.laruence.com/" TITLE="风雪之隅" TARGET="_blank">http://www.laruence.com</a>)
      </li>
      <li>
        本文地址: <a HREF="http://www.laruence.com/2011/01/20/1840.html" TITLE="Permanet Link to Expect:100-continue">http://www.laruence.com/2011/01/20/1840.html</a>
      </li>
      <li>
        转载请注明出处
      </li>
    </ul>
  </div>
  
  <div>
  </div>
  
  <p>
    在使用curl做POST的时候, 当要POST的数据大于1024字节的时候, curl并不会直接就发起POST请求,而是会分为俩步,
  </p>
  
  <pre NAME="code"></pre>
  
  <ol>
    <li>
      1. 发送一个请求,包含一个Expect:100-continue, 询问Server使用愿意接受数据
    </li>
    <li>
      2.接收到Server返回的100-continue应答以后, 才把数据POST给Server
    </li>
    <li>
    </li>
  </ol>
  
  <p>
    这是libcurl的行为.
  </p>
  
  <p>
    具体的RFC相关描述: <a HREF="http://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html#sec8.2.3">http://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html#sec8.2.3</a>
  </p>
  
  <p>
    于是,这样就有了一个问题, 并不是所有的Server都会正确应答100-continue, 比如lighttpd,就会返回417 “Expectation Failed”, 则会造成逻辑出错,,
  </p>
  
  <p>
    要解决的办法也挺容易:
  </p>
  
  <pre NAME="code"></pre>
  
  <ol>
    <li>
      <span>curl_setopt</span><span>(</span><span>$ch</span><span>,</span>CURLOPT_HTTPHEADER<span>,</span> <span>array</span><span>(</span><span>&#8216;Expect:&#8217;</span><span>));</span>
    </li>
    <li>
      <span>//Disable Expect: header (lighttpd does not support it)</span>
    </li>
  </ol>