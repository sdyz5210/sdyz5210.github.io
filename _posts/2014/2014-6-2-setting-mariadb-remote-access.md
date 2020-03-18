---
layout: post
title: 设置MariaDB远程访问
category: program 
tags: [mariadb]
---

1、首先配置允许访问的用户，采用授权的方式给用户权限.

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'IDENTIFIED BY 'password' WITH GRANT OPTION;
```

说明：root是登陆数据库的用户，password是登陆数据库的密码，*就是意味着任何来源任何主机反正就是权限很大的样子。
2、最后配置好权限之后不应该忘记刷新使之生效。

```
flush privileges;
```

网络上很多资源都说经过上面的操作就可以了，但是我在ubuntu下面还是禁止访问。我们可以在执行上面的操作的基础上，修改一下mariadb的配置文件即可。
3、在/etc/mysql下面找到my.cnf配置文件，找到下面一行配置信息：

```
bind-address          = 127.0.0.1
```

把这行配置信息注释掉即可。然后重启数据库服务，重启命令可以参考上一篇的博文。
