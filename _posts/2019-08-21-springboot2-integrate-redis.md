---
layout: post  
title: springboot2配置redis
date: 2019-08-11
tags: [springboot,redis]

--- 

### Redis简介

Redis是一个开源的使用 ANSI C语言编写，支持网络，可基于内存也可持久化的日志型，Key-Value数据库，并提供了多种语言的 API ,相比 Memcached 它支持存储的类型相对更多 (字符，哈希，集合，有序集合，列表等)，同时Redis是线程安全的。

客户端连接Redis使用的是TCP协议，直连的方式每次需要建立TCP连接，而连接池的方式是可以预先初始化好客户端连接，所以每次只需要从连接池借用即可，而借用和归还操作是本地进行的，只有少量的并发同步开销，远远小于新建TCP连接的开销。另外，直连的方式无法限制redis客户端对象的个数，在极端情况下可能造成连接泄露，而连接池的形式可以有效的保护和控制资源的使用。

Jedis和Lettuce都是连接Redis Server的客户端程序，Jedis在实现上直连Redis Server，多线程环境下非线程安全，除非使用连接池，为每个Jedis实例增加物理连接。Lettuce基于Netty的连接实例（StatefulRedisConnection）,可以在多个线程并发访问，并且线程安全，满足多线程环境下的并发访问，同时它是可以伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。

### 集成环境

spingboot的版本为2.x版本，本案例使用的是2.1.7版本。

在springboot1.5.x版本的默认Redis客户端是Jedis实现的，Springboot2.x版本中默认客户端是lettuce实现。

### Lettuce方式

1. 在`pom.xml`中配置`spring-boot-starter-data-redis`的依赖。

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

```

2. 在`application.properties`文件中配置如下内容

```

#配置redis
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    # 如果使用的jedis 则将lettuce改成jedis即可
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0

```

3. 自定义Template：默认情况下模板只支持RedisTemplate<String,Strinig>，也就是只能存入字符串，这在开发中是不友好的，所以自定义模板很有必要。当自定义了模板又想使用String存储时可以继续使用`StringRedisTemplate`的方式，它们并不冲突。


```

package com.xxx.web.config;

import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.io.Serializable;

@Configuration
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Serializable> redisCacheTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Serializable> template = new RedisTemplate<>();
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}

```

### 测试

编写单元测试进行验证,单元测试中使用到了一个`User`对象，就不在这里展示了。
具体单元测试代码如下：

```

@RunWith(SpringRunner.class)
@SpringBootTest
public class RedisTemplateTest {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void redisTest(){
        //redis存储数据
        String key = "token";
        redisTemplate.opsForValue().set(key,"123456");
        //获取数据
        String value = (String)redisTemplate.opsForValue().get(key);
        System.out.println("获取缓存中key为"+key+"的值为："+value);

        User user = new User();
        user.setUserName("Tom");
        user.setUserId("123");
        user.setEmail("Tom@126.com");
        user.setPhone("13800138000");
        redisTemplate.opsForValue().set("user",user);

        User u = (User) redisTemplate.opsForValue().get("user");
        System.out.println(u.toString());
    }
}

```

### Jedis方式

Jedis的配置可以很轻松的在Lettuce上修改。

1. 修改`pom.xml`：

```

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <!-- 这里需要单独排除默认包的引入 -->
    <exclusions>
        <exclusion>
            <groupId>io.lettuce</groupId>
            <artifactId>lettuce-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- 需要额外增加jedis的配置 -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

```

2. 修改`application.yum`中的redis配置，把lettuce修改成jedis即可。