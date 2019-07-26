---
layout: post
title: Springboot集成Swagger
tags: [springboot2，swagger]

---

### Pom导入依赖包

注意：此次我导入的是2.9.2版本。之前使用的是2.6.1版本，但是v2.6.1版本在Swagger-UI的方法名描述为中文时无法点击操作，存在一个bug。

```
    
 <!-- 引物swagger -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>   

```

### 配置Swagger

```

@Configuration
@EnableSwagger2
public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                //配置忽略某些固定非用户传递的参数
                .ignoredParameterTypes(Map.class, ModelAndView.class,Model.class, HttpSession.class)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.summer.web.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Spring Boot中使用Swagger2构架接口文档")
                .description("验证springboot集成Swagger")
                .version("1.0")
                .build();
    }
}


```

### 配置拦截器

在做的系统中没有拦截器时，可以不配置，只配置静态资源映射即可；如果系统中有拦截器，需要在拦截器中配置排除规则；

```

@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
            .excludePathPatterns("/static/**","/webjars/**","/swagger-ui.html","/swagger-resources/**","/v2/**");
}

@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/webjars/**")
            .addResourceLocations("classpath:/META-INF/resources/webjars/");
    registry.addResourceHandler("swagger-ui.html")
            .addResourceLocations("classpath:/META-INF/resources/");
    registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
}

```

配置完以上信息就可以在接口上使用了。

### 例子

```

@Api(tags = "用户登录类")  //此处的中文信息，如果使用Swagger2.6.1时，页面的点击操作会有影响，网友反馈这是2.6.1的一个bug
@Controller
public class LoginController {

    @ApiOperation(value = "用户登录",notes = "用户名必须是数字字母组合，长度至少6个字符，不能超过20个字符")
    @PostMapping("/user/login")
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                        Model model, HttpSession session){

    // 在接口描述中可以不显示model和session的描述，通过在Swagger配置文件中设置ignoredParameterTypes即可。
```