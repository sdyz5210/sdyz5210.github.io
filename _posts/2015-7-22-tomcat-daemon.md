---
layout: post  
title: Tomcat Daemon模式下乱码问题  
category: program  
tags: [tomcat]  

---


在linux普通用户下面，使用daemon进行设置tomcat在普通用户下开机启动，但是之前tomcat的编码是正常的，但是使用了该插件后，日志出现了乱码，解决方案如下：

在daemon.sh中加入一下代码：

```
###CHANGE BY PHPDRAGON###
##Repair of Tomcat container Garbled problem##
CATALINA_OPTS="$CATALINA_OPTS -Dfile.encoding=UTF-8"
##Tomcat optimization##
JAVA_OPTS="$JAVA_OPTS -server -Xms2560m -Xmx2560m -Xss1280k -XX:PermSize=320M -XX:MaxNewSize=1280m -XX:MaxPermSize=1280m -Djava.awt.headless=true"
###CHANGE BY PHPDRAGON###
```

添加位置在 下面代码的下面即可

```
# Increase the maximum file descriptors if we can
if [ "$cygwin" = "false" ]; then
    MAX_FD_LIMIT=`ulimit -H -n`
    if [ "$?" -eq 0 ]; then
        # Darwin does not allow RLIMIT_INFINITY on file soft limit
        if [ "$darwin" = "true" -a "$MAX_FD_LIMIT" = "unlimited" ]; then
            MAX_FD_LIMIT=`/usr/sbin/sysctl -n kern.maxfilesperproc`
        fi
        test ".$MAX_FD" = ".maximum" && MAX_FD="$MAX_FD_LIMIT"
        ulimit -n $MAX_FD
        if [ "$?" -ne 0 ]; then
            echo "$PROGRAM: Could not set maximum file descriptor limit: $MAX_FD"
        fi
    else
        echo "$PROGRAM: Could not query system maximum file descriptor limit: $MAX_FD_LIMIT"
    fi
fi


```