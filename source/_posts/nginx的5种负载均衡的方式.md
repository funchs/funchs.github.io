---
title: nginx的5种负载均衡的方式
author: funchs
tags: [工具]
categories: [reprint]
date: 2017-01-13 12:00:00
---

### nginx的5种负载均衡的方式

#### 1.roundrobin
轮询方式(weight=1),依次将请求分配到各个后台服务器中，默认的负载均衡方式。 
适用于后台机器性能一致的情况。 
挂掉的机器可以自动从服务列表中剔除。

配置文件如下：

```shell
upstream test {
	server 192.168.109.5:81;
	servse 192.168.109.3:80;
}
```

测试结果：

```shell
[root@xxw conf]# for i in $(seq 10); do curl http://192.168.109.4  ;done
192.168.109.5  It is work!
192.168.109.3 It is work!
192.168.109.5  It is work!
192.168.109.3 It is work!
192.168.109.5  It is work!
192.168.109.3 It is work!
192.168.109.5  It is work!
192.168.109.3 It is work!
192.168.109.5  It is work!
192.168.109.3 It is work!
```
测试结果一直是访问这个1次，再访问那个5次，反复循环。

#### 2.weight
根据权重来分发请求到不同的机器中，适用于后台机器性能不一样的情况。

如果后端服务器down掉，能自动剔除。 
比如下面配置，则1.11服务器的访问量为1.10服务器的两倍。

配置文件如下:

```shell
upstream bakend {
	server 192.168.1.10 weight=1;
	server 192.168.1.11 weight=2;
}
```

#### 3.ip_hash
根据请求者ip的hash值将请求发送到后台服务器中，可以保证来自同一ip的请求被打到固定的机器上，可以解决session问题。
如果后端服务器down掉，要手工down掉。

配置文件如下:

```shell
upstream resinserver{
	ip_hash;
	server 192.168.1.10:8080;
	server 192.168.1.11:8080;
}
```

**ip_hash是容易理解的，但是因为仅仅能用ip这个因子来分配后端，因此ip_hash是有缺陷的，不能在一些情况下使用：**
1. nginx不是最前端的服务器。ip_hash要求nginx一定是最前端的服务器，否则nginx得不到正确ip，就不能根据ip作hash。譬如使用的是squid为最前端，那么nginx取ip时只能得到squid的服务器ip地址，用这个地址来作分流是肯定错乱的。
2. nginx的后端还有其它方式的负载均衡。假如nginx后端又有其它负载均衡,，将请求又通过另外的方式分流了，那么某个客户端的请求肯定不能定位到同一台session应用服务器上。这么算起来，nginx后端只能直接指向应用服务器，或者再搭一个squid，然后指向应用服务器。最好的办法是用location作一次分流，将需要session的部分请求通过ip_hash分流，剩下的走其它后端去。

#### 4.url_hash（第三方插件）
根据请求的url的hash值将请求分到不同的机器中，当后台服务器为缓存的时候效率高。
在upstream中加入hash语句，hash_method是使用的hash算法

配置文件如下:

```shell
upstream resinserver{
	server 192.168.1.10:8080;
	server 192.168.1.11:8080;
	hash $request_uri;
	hash_method crc32;
}
```

#### 5.fair（第三方插件）

根据后台响应时间来分发请求，响应时间短的分发的请求多。

配置文件如下:

```shell
upstream resinserver{
	server 192.168.1.10:8080;
	server 192.168.1.11:8080;
	fair;
}
```

#### tips:  

```shell
#定义负载均衡设备的Ip及设备状态 
upstream bakend{ 
	ip_hash;  
	server 127.0.0.1:9090 down;  
	server 127.0.0.1:8080 weight=2;  
	server 127.0.0.1:6060;  
	server 127.0.0.1:7070 backup;  
}

#在需要使用负载均衡的server中增加  
proxy_pass http://bakend/; 
```

#### 设备的状态
1. down 表示单前的server暂时不参与负载 
2. weight 权重,默认为1。 weight越大，负载的权重就越大。 
3. max_fails 允许请求失败的次数默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误 
4. fail_timeout max_fails次失败后，暂停的时间。 
5. backup 备用服务器, 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。


#### 其他
nginx支持同时设置多组的负载均衡，用来给不用的server来使用。  
client_body_in_file_only 设置为On 可以讲client post过来的数据记录到文件中用来做debug  
client_body_temp_path 设置记录文件的目录 可以设置最多3层目录  

location 对URL进行匹配.可以进行重定向或者进行新的代理 负载均衡

```shell
location / {
	proxy_pass	http://test /;   #将请求传到负载服务器池
}
root 指令：指定目录
proxy_pass  proxy_store  proxy_cache ：实现缓存代理
expires  指定缓存时间
```