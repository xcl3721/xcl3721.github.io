---
id: 246
title: mysql集成的key-value引擎-个人翻译
date: 2018-10-10T14:13:27+08:00
author: 徐艺洲
layout: revision
guid: http://www.fireidea.com/archives/246
permalink: /archives/246
---
<div id="sina_keyword_ad_area2" class="articalContent   ">
  <i>As Calvin mentioned in “<a HREF="http://blogs.innodb.com/wp/2011/04/nosql-to-innodb-with-memcached/">NoSQLto InnoDB with Memcached</a>“, we just released a “technologypreview” of the feature that makes memcached a MySQL Daemon Plugin.And this “technology preview” release demonstrates how user can goaround SQL Optimizer and Query Processing and directly interactwith InnoDB Storage Engine through InnoDB APIs. Here, we would liketo walk with you step by step to see how to get the memcachedDaemon Plugin set up and get it running.</i></p> 
  
  <div>
    <p>
      正如上文提到的，我们只是释放出了一个技术预览版。此功能目前只是mysql的一个插件，使用memcached协议。
    </p>
    
    <p>
    </p>
    
    <p>
      <i>If you would just like to get a brief introduction on thesetup steps, there is a “README-innodb_memcached” in themysql-5.6.2-labs-innodb-memcached package. This is a moreelaborated description on these steps.</i>
    </p>
    
    <p>
      如果你想获取到更多关于安装的介绍步骤，请查看README-innodb_memcached文件，这个文件在mysql-5.6.2-labs-innodb-memcached内。里面有更详细的介绍
    </p>
    
    <p>
      <strong>1) Prerequisite:服务需求</strong>
    </p>
    
    <p>
      <i>Currently, the Memcached Daemon Plugin prototype is onlysupported on Linux platform. And as a prerequisite, you must havelibevent installed, since it is required by memcached.</i>
    </p>
    
    <p>
      目前为止memcached守护进程插件只支持linux平台，并且你需要安装libevent，因为这个是memcached所需的
    </p>
    
    <p>
      <i>If you have the source code release, then there is a libevent1.4.3 included in the package (plugin/innodb_memcached/libevent).You can go to the directory and do autoconf, ./configure, make andmake install to make the libevent installed.</i>
    </p>
    
    <p>
      如果需要你可以去plugin/innodb_memcached/libevent来安装libevent1.4.3，只需配置./configure 然后make &makeinstall
    </p>
    
    <p>
    </p>
    
    <p>
      <span></span>
    </p>
    
    <p>
      <strong>2) Build the server编译服务</strong>
    </p>
    
    <p>
      <i>Assuming you would like to build the server yourself(otherwise, you can just skip this section), once libevent isinstalled, you can just build the MySQL server as usual.</i>
    </p>
    
    <p>
      考虑到你可以更喜欢自己编译源代码来构建服务器，当然，如果不是可以跳过此章节，只要安装了libevent，你可以按之前方式编译mysql
    </p>
    
    <p>
      <i>Our source code is in the “plugin/innodb_memcached”directory. As part of server build, it will generate two sharedlibraries:</i>
    </p>
    
    <p>
      <i>1) libmemcached.so: this is the memcached daemon plugin toMySQL</i>
    </p>
    
    <p>
      <i>2) innodb_engine.so: this is an InnoDB API plugin tomemcached</i>
    </p>
    
    <p>
      在plugin/innodb_memcached目录下的文件编译后会生成两个公共库
    </p>
    
    <p>
      1） libmemcached.so 这个是mysql内的memcached插件
    </p>
    
    <p>
      2）innodb_engine.so 这个是innodb调用memcached 的api插件
    </p>
    
    <p>
      <i>Make sure above two shared libraries are put in the MySQLplugin directory. You can find MySQL plugin directory by doing“select @@plugin_dir”:</i>
    </p>
    
    <p>
      确认将这两个文件拷贝到mysql的插件目录，你可以在mysql命令行下执行以下语句查找mysql插件保存地址
    </p>
    
    <p>
      mysql> select @@plugin_dir;<br />+——————————————————————————-+<br />|@@plugin_dir |<br />+——————————————————————————-+<br />| /home/jy/work2/mysql-5.6-memcached-labs/lib/plugin |<br />+——————————————————————————-+<br />1 row in set (0.00 sec)
    </p>
    
    <p>
    </p>
    
    <p>
      <strong>3) Install configuration tables:安装创建表</strong>
    </p>
    
    <p>
      <i>Next, the memcached plugin needs a bit configuration to knowhow to interact with InnoDB table. We have a configuration scriptin “scripts/innodb_memcached_config.sql”. You can just install thenecessary configure tables by running “mysql< scripts/innodb_memcached_config.sql”. If you donot like to know the detail of these configuration tables, you canjust skip this section.</i>
    </p>
    
    <p>
      下一步，memcached插件需要一些配置。我们有一个配置脚本在scripts/innodb_memcached_config.sql，你可以直接用mysql执行他即可完成所有的配置。下面我们会介绍他都添加了哪些表，如果你对此没有兴趣可以跳过此章节
    </p>
    
    <p>
      <i>This configure script installs 3 tables needed by the InnoDBMemcached. These tables are created in a dedicated database“innodb_memcache”. We will go over these three tablesin a bit more detail:</i>
    </p>
    
    <p>
      这个脚本会添加三个表，这三个表是memcached插件所必须的，下面我们会详细介绍下这三个表的用途
    </p>
    
    <p>
      <i>1) “containers” &ndash; This table is the most important table for“Memcached &ndash; InnoDB mapping”. It describes the table used to storethe memcached values. Currently, we can only map memcached to onetable at a time. So essentially, there will be only one row in thetable. In the future, we would consider making this configurationmore flexible and dynamic, or user can map memcached operations tomultiple tables.</i>
    </p>
    
    <p>
      1）container 数据存储表，这个表用于memcached -innodb映射，这里将保存所有memcached的映射相关值，目前这个表只有一个，且数据只有一行。未来我们会考虑将这个配置的更灵活，比如用户可以根据自定规则将值存储到多个指定表。
    </p>
    
    <p>
      <i>The mapping is done through specifying corresponding columnvalues in the table:</i>
    </p>
    
    <ul>
      <li>
        <i>“db_schema” and “db_table” columns describe the database andtable name for storing the memcached value.</i>
      </li>
    </ul>
    
    <ul>
      <li>
        <i>“key_columns” describes the column (single column) name forthe column being used as “key” for the memcached operation</i>
      </li>
    </ul>
    
    <ul>
      <li>
        <i>“value_columns” describes the columns (can be multiple) usedas “values” for the memcached operation. User can specify multiplecolumns by separating them by comma (such as “col1, col2&Prime;etc.)</i>
      </li>
    </ul>
    
    <ul>
      <li>
        <i>“unique_idx_name_on_key” is the name of the index on the“key” column. It must be a unique index. It can be primary orsecondary.</i> <p>
          这个映射配置内有很多设置对应如下：</li> 
          
          <li>
            db_schema anddb_table行用来说明存储memcached的值，table是指保存数据的表，而schema是存储数据库名
          </li>
          <li>
            key_column 表内哪列用于key标识
          </li>
          <li>
            value_columns 表内哪列用于保存key对应的值
          </li>
          <li>
            unique_idx_name_on_key这个是key_column指定的数据表内的key的索引名，必须是唯一索引，当然可以使用主键或二级索引
          </li></ul> 
          
          <p>
            <i>Above 5 column values (table name, key column, value columnand index) must be supplied. Otherwise, the setup willfail.</i>
          </p>
          
          <p>
            <i>Following are optional values, however, to fully comply withmemcached protocol, you will need these column values suppliedtoo.</i>
          </p>
          
          <ul>
            <li>
              “flags” describes the columns used as “flag” for memcached. Italso used as “column specifier” for some operations (such as incr,prepend) if memcached “value” is mapped to multiple columns. So theoperation would be done on specified column. For example, if youhave mapped value to 3 columns, and only want the “increment”operation performed on one of these columns, you can use flags tospecify which column will be used for these operations.
            </li>
          </ul>
          
          <ul>
            <li>
              “cas_column” and “exp_column” are used specifically to storethe “cas” and “exp” value of memcached. <p>
                上面的5个设置必须提供，否则会失败，下面的几个选项可以选填</li> 
                
                <li>
                  flag表示类似memcached的flag，他的用途用来表示某“列”比方说当memcached映射的值包含多列。比如我们有三列，我们只想incr操作其中的一列，你可以通过flags指定哪行进行此操作
                </li>
                <li>
                  case_column以及exp_column是用来保存cas以及exp设置（这俩你知道的……查下cas以及超时） <p>
                    </l I></ul> 
                    
                    <p>
                      <i>2. Table “cache_policies” specifies whether we’ll use InnoDBas the data store of memcached (innodb_only) or use memcached’s“default engine” as the backstore (cache-only) or both (caching).In the last case, only if the default engine operation fails, theoperation will be forwarded to InnoDB (for example, we cannot finda key in the memory, then it will search InnoDB).</i>
                    </p>
                    
                    <p>
                      2.表“cache_policies”制定了我们是否使用innodb保存数据，我们可以指定使用那种方式进行数据保存，如：使用innodb(innodb_only),使用memcached默认引擎(defaultengine)并且不保存在innodb，或者使用memcached保存并使用innodb保存(caching)。在最后一个选项内，当默认引擎查询失败，查询将会在innodb内查询（如内存内没有这个key的数据，他将会自动查询innodb）
                    </p>
                    
                    <p>
                      <i>3) Table “config_options”, currently, we only support oneconfig option through this table. It is the “separator” used toseparate values of a long string into smaller values for multiplecolumns values. For example, if you defined “col1, col2&Prime; as valuecolumns. And you define “|” as separate, you could issue followingcommand in memcached to insert values into col1 and col2respectively:</i>
                    </p>
                    
                    <p>
                      <i>set keyx 10 0 19</i>
                    </p>
                    
                    <p>
                      <i>valuecolx|valuecoly</i>
                    </p>
                    
                    <p>
                      <i>So “valuecol1x” will send to col1 and valuecoly will send tocol2.</i>
                    </p>
                    
                    <p>
                      3.表&#8221;<i>config_options</i>&#8220;目前我们只在这提供了一个配置选项，此选项用于拆分&#8221;<i>separator</i>&#8220;，当我们有一个很长的字符串，我们可以通过此功能把他拆分成多列内保存。比如定义“列1,列2”为value_COLUMNS。&#8221;|&#8221;符号为分隔符的时候，我们可以使用下面命令把列1和列2内容分别插入
                    </p>
                    
                    <p>
                      set keyx 10 0 19
                    </p>
                    
                    <p>
                      列1|列2
                    </p>
                    
                    <p>
                      这时列1值和列2值将会分别存在两个对应顺序的字段内
                    </p>
                    
                    <p>
                    </p>
                    
                    <p>
                      <i>4) Example tables</i>
                    </p>
                    
                    <p>
                      <i>Finally, as part of the configuration script, we created a“demo_test” in the “test” database as an example. It also allowsthe Daemon Memcached to work out of box, and no need to for anyadditional configurations.</i>
                    </p>
                    
                    <p>
                      <i>As you would notice, this “demo_test” table has more columnsthan needed, so it would need the entries in the “container” tableto tell which column is used for what purpose as describedabove.</i>
                    </p>
                    
                    <p>
                      4）示例表
                    </p>
                    
                    <p>
                      最后，在我们的配置脚本内，他会自动建立一个 demo_test在test数据库内。
                    </p>
                    
                    <p>
                      当然这个可以直接使用，无需其他特殊配置
                    </p>
                    
                    <p>
                      可能你会发现，这个表内有很多列超过了我们的需要。我们可以再container内自行更改下配置只保留我们用的
                    </p>
                    
                    <p>
                    </p>
                    
                    <p>
                      <strong>4) Install the Daemon Plugin（安装守护插件）</strong>
                    </p>
                    
                    <p>
                      <i>The final step would be installing the daemon plugin. It isthe same as installing any other MySQL plugin:</i>
                    </p>
                    
                    <p>
                      最后一步就是安装守护插件，这个步骤和安装其他mysql插件一样执行以下命令
                    </p>
                    
                    <p>
                      mysql> install plugin daemon_memcached soname“libmemcached.so”;
                    </p>
                    
                    <p>
                      <i>If you have any memcached specific configure parameters,although it takes effect when the plugin is installed, you wouldneed to specify it during MySQL server boot timeor enter them in the MySQL configure files.</i>
                    </p>
                    
                    <p>
                      如果你有任何附加的memcached插件配置参数，你可以把他放在mysql的配置文件。重启mysql即可生效
                    </p>
                    
                    <p>
                      <i>For example, if you would like the memcached to listen onport “11222&Prime; instead of the default port “11211&Prime;, then you wouldneed to add “-p11222&Prime; to MySQL system configure variable“daemon_memcached_option”:</i>
                    </p>
                    
                    <p>
                      比如你想让memcached监听11222端口而非默认的11211端口，你需要指定-p111222参数，到my.cnf内的daemon_memcached_option字段内写法如下
                    </p>
                    
                    <p>
                      mysqld …. &ndash;loose-daemon_memcached_option=”-p11222&Prime;.
                    </p>
                    
                    <p>
                      <i>Of course, you can add other memcached command line optionsto “daemon_memcached_option” string.</i>
                    </p>
                    
                    <p>
                      <i>The other configure options are:</i>
                    </p>
                    
                    <p>
                      <i>1) daemon_memcached_engine_lib_name (default“innodb_engine.so”)</i>指定默认存储引擎so文件
                    </p>
                    
                    <p>
                      <i>2) daemon_memcached_engine_lib_path (default NULL, the plugindirectory).</i>指定memcached插件lib地址
                    </p>
                    
                    <p>
                      当然，你可以增加其他命令行参数到配置内如上
                    </p>
                    
                    <p>
                      B<i>y default, you do not need to set/change anything with thesetwo configure option. We have above two configure options becausethey allow you to load any other storage engine for memcached (suchas NDB memcached engine). This opens door for more interestingexploration.</i>
                    </p>
                    
                    <p>
                      总的来说你不需要改变上面两个参数。我们提供者两个参数是为了提供其他存储引擎给memcached，如NDB存储引擎，这样可以提供更多有趣的体验。
                    </p>
                    
                    <p>
                      <i>3) daemon_memcached_r_batch_size, batch commit size for readoperation (get operations. It specifies after how many ops we willdo a commit. By default, this is set as a very large number,1048576.</i>
                    </p>
                    
                    <p>
                      守护进程的读取操作批量更新（每多少次操作后将会做一次数据提交到存储引擎，默认会设置很大如1048576）
                    </p>
                    
                    <p>
                      <i>4) daemon_memcached_w_batch_size, batch commit for any writeoperations (set, replace, append, prepend, incr, decr etc.) Bydefault, this is set as 32.</i>
                    </p>
                    
                    <p>
                      多少次写操作后才会更新存储引擎，默认32
                    </p>
                    
                    <p>
                      <i>Again, please note that you will have these configurationparameter in your MySQL configure file or MySQL boot command line.And they will take effect when you load the memcachedplugin.</i>
                    </p>
                    
                    <p>
                      另外，注意，当你在命令行启动mysql服务或者在配置文件内指定，都会改变memcached插件的配置
                    </p>
                    
                    <p>
                    </p>
                    
                    <p>
                      <strong>5) Start to play:</strong>
                    </p>
                    
                    <p>
                      <i>Now you have everything set up, you can start toplay:</i>
                    </p>
                    
                    <p>
                      现在我们可以玩一下拉~
                    </p>
                    
                    <p>
                      telnet 127.0.0.1 11211
                    </p>
                    
                    <p>
                      使用telnet连11211端口（memcached协议都可以直接这么玩）
                    </p>
                    
                    <p>
                      set a11 10 0 9<br />123456789<br />STORED<br />get a11<br />VALUE a11 0 9<br />123456789<br />END
                    </p>
                    
                    <p>
                      <i>You can access the InnoDB table (“demo_test”) through thestandard SQL interfaces. However, there are some catches:</i>
                    </p>
                    
                    <p>
                    </p>
                    
                    <p>
                      <i>1) If you would like to take a look at what’s in the“demo_test” table, please remember we had batched the commits (32ops by default) by default. So you will need to do “readuncommitted” select to find the just inserted rows:</i>
                    </p>
                    
                    <p>
                      这时你可以访问innodb表“demo_test”去看下里面的内容，另外要注意，我们上面设置了写操作执行了32次后才会保存同步一次数据到存储引擎，所以你必须使用读取未提交数据来列出
                    </p>
                    
                    <p>
                      mysql> set session TRANSACTION ISOLATIONLEVEL<br />-> read uncommitted;<br />Query OK, 0 rows affected (0.00 sec)
                    </p>
                    
                    <p>
                      mysql> select * from demo_test;<br />+——+——+——+——+———&ndash;+——+——+——+——+——+——+<br />| cx |cy |c1 |cz |c2 | ca |CB |c3 |cu |c4 |C5 |<br />+——+——+——+——+———&ndash;+——+——+——+——+——+——+<br />| NULL | NULL | a11 | NULL | 123456789 | NULL |NULL | 10 | NULL| 3 | NULL|<br />+——+——+——+——+———&ndash;+——+——+——+——+——+——+<br />1 row in set (0.00 sec)
                    </p>
                    
                    <p>
                      <i>2) The InnoDB table would be IS (shared intention) or IX(exclusive intentional) locked for all operations in a transaction.So unless you change “daemon_memcached_r_batch_size” and“daemon_memcached_w_batch_size” to small number (like 1), the tableis most likely intentionally locked between each operations. So youcannot do any DDL on the table.</i>
                    </p>
                    
                    <p>
                      当innodb在提交同步事务中，他将会使用锁锁定表，所以当你把“daemon_memcached_r_batch_size”and“daemon_memcached_w_batch_size”设置的值十分低的时候（如1）将会导致innodb频繁的锁表，所以你无法做任何DDL操作到表上
                    </p>
                    
                    <p>
                      <strong>Summary</strong>
                    </p>
                    
                    <p>
                      Now<br /> you have everything setup. And you can directly interactwith InnoDB storage engine through Memcached interfaces. Inaddition, as you might notice while going through this extended“README”, we still have a lot interesting options open forexploration and enhancement. This is the beginningof opening InnoDB to the outside world, and NoSQLis a perfect place for it to play.
                    </p>
                    
                    <p>
                      总结
                    </p>
                    
                    <p>
                      没啥了……
                    </p></div> 
                    
                    <p>
                      个人水平有限，翻译了以下mysql5.6.2内的innodb实现keyvalue方式存储的介绍文章<br />考虑到可能会有纰漏，把原文和翻译对照一起敲下
                    </p>