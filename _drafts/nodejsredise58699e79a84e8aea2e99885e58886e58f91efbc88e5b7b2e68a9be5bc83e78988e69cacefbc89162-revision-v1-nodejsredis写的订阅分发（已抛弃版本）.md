---
id: 252
title: nodejs+redis写的订阅分发（已抛弃版本）
date: 2018-10-10T14:13:40+08:00
author: 徐艺洲
layout: revision
guid: http://www.fireidea.com/archives/252
permalink: /archives/252
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  下面是我前段时间用nodejs监听redis的pub/sub写的订阅脚本<br />后来由于持久化及系统资源耗费严重（cpu 50% 内存500m）抛弃掉了，放这里给大家围观下<br />如果有好办法请告诉我，谢过</p> 
  
  <p>
    设计思路如下<br />1.当有人在redis下发布一个消息的同时发布个广播<br />2.分发中转程序收到广播后将消息复制成多份扔到对应订阅者的接受队列内，并对各个队列发送订阅广播<br />3.redis下的订阅端就会接收到事件，并把事件用http发送一个通知接口让对方来取
  </p>
  
  <p>
    中间分发用的开源的qdis后来想慢慢替换掉一直没来得及替换。
  </p>
  
  <p>
    var redis = require(&#8220;redis&#8221;);<br />var subscription_client = redis.createClient();<br />var regular_client = redis.createClient();<br />var http=require(&#8220;http&#8221;);<br />//var querystring=require(&#8220;querystring&#8221;);<br />var url=require(&#8220;url&#8221;);
  </p>
  
  <p>
    //current send process count<br />var currentSend=0;<br />var starttime = process.hrtime();<br />var configlistobj={<br /> &#8216;pub&#8217;:{<br /> &#8216;sub1&#8242;:&#8217;http://xxxx.com/test.php?p=123&#8217;,<br /> &#8216;sub2&#8242;:&#8217;http://xxxx.com/test.php?p=456&#8217;<br /> }<br />};<br />//time report<br />setInterval(function () {<br />var diff = process.hrtime(starttime);<br />console.log(&#8216;benchmark took %d seconds and %d nanoseconds&#8217;,diff[0],diff[1]);}, 1000);
  </p>
  
  <p>
    //send http request<br />function httprequest(urlpath,dataString)<br />{<br /> currentSend++;<br /> //parse theurl<br /> urlpath =url.parse(urlpath);<br /> //defaultport is 80<br /> if(urlpath[&#8220;port&#8221;]==&#8221;&#8221;)<br /> {<br /> urlpath[&#8220;port&#8221;]=80;<br /> }
  </p>
  
  <p>
    var headers= {<br /> &#8216;content-type&#8217;: &#8216;application/x-www-form-urlencoded&#8217;,<br /> &#8216;Content-Length&#8217;: dataString.length<br /> };
  </p>
  
  <p>
    var options= {<br /> host: urlpath[&#8220;hostname&#8221;],<br /> port: urlpath[&#8220;port&#8221;],<br /> path: urlpath[&#8220;path&#8221;],<br /> method: &#8216;POST&#8217;,<br /> headers: headers<br /> };
  </p>
  
  <p>
    //console.log(JSON.stringify(options));
  </p>
  
  <p>
    var req =http.request(options, function(res) {<br /> //console.log(&#8216;STATUS: &#8216; + res.statusCode);<br /> //console.log(&#8216;HEADERS: &#8216; + JSON.stringify(res.headers));<br /> if(res.statusCode!=200)<br /> {<br /> console.log(&#8220;fuck,get error&#8221;);<br /> return;<br /> }<br /> res.setEncoding(&#8216;utf8&#8217;);<br /> res.on(&#8216;data&#8217;, function (chunk) {<br /> console.log(&#8216;BODY: &#8216; + chunk);<br /> currentSend&#8211;;<br /> console.log(&#8216;Children:&#8217;+currentSend);
  </p>
  
  <p>
    });<br /> });
  </p>
  
  <p>
    // errorhandle<br /> req.on(&#8216;error&#8217;,function(e){<br /> console.log(&#8216;error&#8217;);<br /> });<br /> ;<br /> req.setTimeout(1,function(socket){});<br /> req.write(dataString);<br /> //console.log(dataString);<br /> req.end();<br />}
  </p>
  
  <p>
    function processOfflineData(channel)<br />{<br /> regular_client.rpop( channel, function(err,res){<br /> if(res != null)<br /> {<br /> httprequest(configlistobj[&#8220;pub&#8221;][&#8220;sub1&#8221;],res);<br /> processOfflineData(channel);<br /> }<br /> });<br />}
  </p>
  
  <p>
    subscription_client.on( &#8216;message&#8217;, function( channel, message ){
  </p>
  
  <p>
    //thechannel name is the same name as the list<br /> regular_client.rpop( channel, function(err,res){<br /> httprequest(configlistobj[&#8220;pub&#8221;][&#8220;sub1&#8221;],res);<br /> console.log(currentSend);<br /> });
  </p>
  
  <p>
    } );
  </p>
  
  <p>
    console.log(&#8220;sending the Offline info&#8221;);<br />processOfflineData(&#8220;sub&#8221;);<br />//start process<br />subscription_client.subscribe(&#8216;sub&#8217;);
  </p>