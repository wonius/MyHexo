---
title: SpringBoot集成Swagger2
copyright: true
date: 2018-04-27 15:57:56
tags:
- SpringBoot
- Swagger2
categories: 
- Spring
- SpringBoot
keywords: 
- SpringBoot
- Swagger2
description: SpringBoot集成Swagger2
---

## 增加denpendency

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.8.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.8.0</version>
</dependency>
```

## 创建Swagger2配置类

```java
@Configuration
@EnableSwagger2
public class Swagger2Config {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.springboot.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("SpringBoot中使用Swagger2构建RESTful APIS")
                .description("更多SpringBoot相关文章请关注：http://wonius.top")
                .termsOfServiceUrl("http://wonius.top")
                .contact("Gavin's Notes")
                .version("1.0")
                .build();
    }
}
```

## 🌰

```java
@RestController
@RequestMapping(value = "/users")
@Api(tags = "用户管理类")
public class UserControllerWithSwagger2 {

    static Map<Long, User> users = Collections.synchronizedMap(new HashMap<Long, User>());

    @ApiOperation(value = "获取用户列表")
    @RequestMapping(value = "/getUsers")
    public List<User> getUsers() {
        return new ArrayList<User>(users.values());
    }

    @ApiOperation(value = "创建用户", notes = "根据User对象创建用户")
    @ApiImplicitParam(name = "user", value = "用户实体User", required = true, dataType = "User")
    @RequestMapping(value = "/postUser", method = RequestMethod.POST)
    public String postUser(@RequestBody User user) {
        users.put(user.getId(), user);
        return "success";
    }

    @ApiOperation(value = "获取用户信息", notes = "根据id获取用户信息")
    @ApiImplicitParam(name = "id", value = "用户id", required = true, dataType = "Long")
    @RequestMapping(value = "/getUser")
    public User getUser(@PathVariable Long id) {
        return users.get(id);
    }

    @ApiOperation(value = "更新用户详细信息", notes = "根据id更新对应user")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "用户id", required = true, dataType = "Long"),
            @ApiImplicitParam(name = "user", value = "用户实体user", required = true, dataType = "User")
    })
    @RequestMapping(value = "/putUser", method = RequestMethod.POST)
    public String putUser(@PathVariable Long id, @RequestBody User user) {
        User u = users.get(id);
        u.setHost(user.getHost());
        u.setName(user.getName());
        users.put(id, u);
        return "success";
    }

    @ApiOperation(value = "删除用户", notes = "根据id删除用户")
    @ApiImplicitParam(name = "id", value = "用户id", required = true, dataType = "Long")
    @RequestMapping(value = "/deleteUser", method = RequestMethod.DELETE)
    public String deleteUser(@PathVariable Long id) {
        users.remove(id);
        return "success";
    }
}
```

之后访问http://localhost:8080/swagger-ui.html加载。

**PS: method一定要写，不然swagger2会将该方法的所有method枚举一遍**

## 常用注解

### `@Api` *用在请求的类上，说明该类的作用*

tags="说明该类的作用"
value="该参数没什么意义，所以不需要配置"

### `@ApiOperation`*用在请求的方法上，说明方法的作用*

value="说明方法的作用"

notes="方法的备注说明"

### `@ApiImplicitParams/@ApiImplicitParam` *用在请求的方法上，请求参数说明*

@ApiImplicitParams：用在请求的方法上，包含一组参数说明
​    @ApiImplicitParam：用在 @ApiImplicitParams 注解中，指定一个请求参数的配置信息       
​        name：参数名
​        value：参数的汉字说明、解释
​        required：参数是否必须传
​        paramType：参数放在哪个地方
​            · header --> 请求参数的获取：@RequestHeader
​            · query --> 请求参数的获取：@RequestParam
​            · path（用于restful接口）--> 请求参数的获取：@PathVariable
​            · body（不常用）
​            · form（不常用）    
​        dataType：参数类型，默认String，其它值dataType="Integer"       
​        defaultValue：参数的默认值

### `@ApiResponses/@ApiResponse`*用在请求的方法上，响应参数说明*

@ApiResponses：用于请求的方法上，表示一组响应
​    @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
​        code：数字，例如400
​        message：信息，例如"请求参数没填好"
​        response：抛出异常的类

### `@ApiModel/@ApiModelProperty`*用在响应类上，表示响应数据的信息*

@ApiModel：用于响应类上，表示一个返回响应数据的信息
​            （这种一般用在post创建的时候，使用@RequestBody这样的场景，
​            请求参数无法使用@ApiImplicitParam注解进行描述的时候）
​    @ApiModelProperty：用在属性上，描述响应类的属性