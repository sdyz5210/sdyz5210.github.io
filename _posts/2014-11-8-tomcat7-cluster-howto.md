---
layout: post
title: 怎么配置Tomcat7 Cluster  
category: program 
tags: [tomcat]  
---

#理论#

如果能够很好的学习和使用一个工具，最简单的方式便是从官方文档中学习。Tomcat7 Cluster配置如下：http://tomcat.apache.org/tomcat-7.0-doc/cluster-howto.html。对于国内的it人员来说，看英文是比较头疼的事情，对本人也是如此。

##Tomcat7实现方式##
Tomcat7自带的集群功能通过session复制完成，现有两种复制方式:  
1、DeltaManager:将session复制到所有tomcat节点中，不管是否有相应的应用。缺点是如果集群节点很多，此方式的系统消耗太大。  
2、BackupManager:将session复制到一个备份节点中，并且只复制到有相应应用的节点。缺点是这种方式没有像DeltaManager一样经过大规模的应用测试。

##步骤##
Tomcat7 中运行session复制，需要完成以下步骤。  
1、session中所有的属性（attributes）必须实现java序列表java.io.Serializable
2、把配置文件server.xml中cluster元素的注释
3、如果你定义了客户化的集群阀门(valves)，请确保在server.xml文件中的Cluster元素中定义ReplicationValve。
4、如果Tomcat节点运行在同一台服务器上，确保tcpListenPort属性值唯一。在大多数情况下，Tomcat可以自动检测4000-4100范围内的端口 
5、确保应用中web.xml文件有<distributable/>元素
6、如果使用mod_jk，确保设置jvmRoute属性<Engine name="Catalina" jvmRoute="node01">，并且这个属性要和workers.properties相同
7、确保所有节点都有相同的时间.
8、确保loadbalancer被设置为粘性session模式(sticky session)

#配置#

##操作环境##
OS:CentOS6.3_64
Web Server1：192.168.1.100
Web Server2：192.168.1.101
Tomcat：apache-tomcat-7.56
负载均衡实用的是nginx，版本为1.7.7,此处不会具体描述怎么配置和安装nginx，只给出配置文件内容，安装和说明稍后会进行总结:

```

 #user  nobody;
 worker_processes  1;

 #error_log  logs/error.log;
 #error_log  logs/error.log  notice;
 #error_log  logs/error.log  info;

 #pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

http {  
    include       mime.types;  
    default_type  application/octet-stream;  
    
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    upstream localhost{ 
    	server 192.168.1.100:8080 weight=1 ;
		server 192.168.1.101:8080 weight=1 ;
    }

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
	    proxy_pass http://localhost;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}  

```  

##Tomcat配置##

在tomcat中，我们需要对conf/server.xml进行修改配置，首先在server.xml的Engine节点修改成如下的内容，增加jvmRoute属性。

```
webserver1的配置
<Engine name="Catalina" defaultHost="localhost"  jvmRoute="tomcat1" >
webserver2的配置
<Engine name="Catalina" defaultHost="localhost"  jvmRoute="tomcat2" > 

```

接下来是对session复制的配置，下面是webserver1上的配置：

```java:n

<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
          channelSendOptions="8">
          <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>
          <Channel className="org.apache.catalina.tribes.group.GroupChannel">
              <Membership className="org.apache.catalina.tribes.membership.McastService"
                          address="228.0.0.4"
                          port="45564"
                          frequency="500"
                          dropTime="3000"/>
              <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                        address="192.168.1.100"
                        port="4000"
                        selectorTimeout="100"
                        maxThreads="6"/>
              <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
                  <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
              </Sender>
              <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
              <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/>
              <Interceptor className="org.apache.catalina.tribes.group.interceptors.ThroughputInterceptor"/>
          </Channel>

          <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                 filter=".*\.gif|.*\.js|.*\.jpeg|.*\.png|.*\.htm|.*\.html|.*\.css|.*\.txt"/>

          <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="true"/>

          <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
      </Cluster>
      
```

webserver2上的tomcat的配置和上面的配置唯一的区别在于需要修改ip地址，把上面的192.168.1.100修改成192.168.1.101即可。

<font color="red">注意：如果两个tomcat是配置在同一个物理机上，tomcat的配置还需要修改一下的内容：  
1、需要修改tomcat本身的端口号，8080,8009,8005等以防止端口冲突
2、需要修改上述配置Receiver中的4000端口号，防止冲突，修改范围为4000~4100
3、Receiver的address的值可以设置成auto，但是如果网络复杂，比如我配置的机器存在双网卡的情况，最好具体配置上ip。
</font>

接下来对上述的配置参数做一些简单的说明：
A、Cluster元素可以是<Engine>或者<Host>的子元素，如果使用farm war deploy功能，则必须是<Host>的子元素。SimpleTcpCluster是官方提供的唯一一个Cluster。channaelSendOptions是设置SimpleTcpCluster.send方法发送消息的方式，默认值是8。  
B、Manager默认的配置如上，但是每个节点上相同的webapp要配置相同的manager。  
C、Channel元素是Tribes，该元素封装所有沟通和成员逻辑。  
D、Membership通过组播方式建立成员，通过address和port设置一个组播，在相同组播下为同一个集群。Tribes也支持静态成员，通过使用StaticMembershipInterceptor。  
E、Receiver 在Tribes中，发送(sender)和接收(receiver)是分开的两个组件。每个节点的address和port是不同的(如果节点有不同的IP地址，可以使用"auto"，如果节点在同一个IP上，则port必须不同)。这个组件中有一个线程池，可以配置相关信息。  
F、TcpFailureDetector - 通过TCP验证成员是否正常。  
G、MessageDispatch15Interceptor - 调度消息到线程(线程池)，异步发送消息。  
H、ThroughputInterceptor - 打印出消息流量的简单统计  
I、Valve 过滤一些不会修改session的request。  
J、Deployer 默认的tomcat集群支持Farm部署，通过一个节点，可以部署和卸载应用到其他节点。集群中只设置一个主节点，将watchEnabled设为true，主节点将监听watchDir的变化，先更新本节点的deployDir，之后更新其他节点的deployDir。集群中其他节点的watchEnabled设为false。如果要执行此功能，Cluster必须是Host的子元素。  
K、ClusterListener 当使用DeltaManager时，通过ClusterSessionListener接收和传播信息。

#配置遇到的<font color="red">坑</font>#

1、问题：很多教程中都说设置Receiver的address的值可以设置成auto，但是本人现有的环境下面试了多次，但是还是有问题？

解答：因为目前操作系统层面涉及到了多个网卡的情况，所以如果单单设置成auto会造成无法识别或者无法获取组播信息的情况。

2、问题：在进行tomcat配置的时候，关闭了防火墙很快就配置好了，但是实际的生产环境下是需要开启防火墙的，一旦开启防火墙配置的session复制就失效了？

解答：一开始就以为就是端口的为题，所以就设置了一下的配置：

```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 4000 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8005 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8009 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8443 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 45564 -j ACCEPT
```

<font color="red">但是还是不行</font>，确实让我头疼了一会，最终发现组播使用的端口的协议不是TCP，所以设置开发端口45564的TCP协议是不可以，必须使用UDP协议，配置方式如下：

```
-A INPUT -p udp -m udp --dport 4000 -j ACCEPT
-A INPUT -p udp -m udp --dport 45564 -j ACCEPT
```

ok，完成。