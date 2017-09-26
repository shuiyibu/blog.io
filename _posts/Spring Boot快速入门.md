# Spring Boot快速入门

# 简介

在您第1次接触和学习Spring框架的时候，是否因为其繁杂的配置而退却了？在你第n次使用Spring框架的时候，是否觉得一堆反复黏贴的配置有一些厌烦？那么您就不妨来试试使用Spring Boot来让你更易上手，更简单快捷地构建Spring应用！

Spring Boot让我们的Spring应用变的更轻量化。比如：你可以仅仅依靠一个Java类来运行一个Spring引用。你也可以打包你的应用为jar并通过使用java -jar来运行你的Spring Web应用。

Spring Boot的主要优点：

- 为所有Spring开发者更快的入门
- 开箱即用，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

------

# 快速入门

本章主要目标完成Spring Boot基础项目的构建，并且实现一个简单的Http请求处理，通过这个例子对Spring Boot有一个初步的了解，并体验其结构简单、开发快速的特性。

## 系统要求：

- Java 7及以上
- Spring Framework 4.1.5及以上

**本文采用Java 1.8.0_73、Spring Boot 1.3.2调试通过。**

## 使用Maven构建项目

1. 通过

   ```
   SPRING INITIALIZR
   ```

   工具产生基础项目

   1. 访问：`http://start.spring.io/`
   2. 选择构建工具`Maven Project`、Spring Boot版本`1.3.2`以及一些工程基本信息，可参考下图所示[![SPRING INITIALIZR](http://blog.didispace.com/content/images/2016/02/chapter1-1.png)](http://blog.didispace.com/content/images/2016/02/chapter1-1.png)SPRING INITIALIZR
   3. 点击`Generate Project`下载项目压缩包

2. 解压项目包，并用IDE以

   ```
   Maven
   ```

   项目导入，以

   ```
   IntelliJ IDEA 14
   ```

   为例：

   1. 菜单中选择`File`–>`New`–>`Project from Existing Sources...`
   2. 选择解压后的项目文件夹，点击`OK`
   3. 点击`Import project from external model`并选择`Maven`，点击`Next`到底为止。
   4. 若你的环境有多个版本的JDK，注意到选择`Java SDK`的时候请选择`Java 7`以上的版本

## 项目结构解析

[![项目结构](http://blog.didispace.com/content/images/2016/02/chapter1-2.png)](http://blog.didispace.com/content/images/2016/02/chapter1-2.png)项目结构

通过上面步骤完成了基础项目的创建，如上图所示，Spring Boot的基础结构共三个文件（具体路径根据用户生成项目时填写的Group所有差异）：

- `src/main/java`下的程序入口：`Chapter1Application`
- `src/main/resources`下的配置文件：`application.properties`
- `src/test/`下的测试入口：`Chapter1ApplicationTests`

生成的`Chapter1Application`和`Chapter1ApplicationTests`类都可以直接运行来启动当前创建的项目，由于目前该项目未配合任何数据访问或Web模块，程序会在加载完Spring之后结束运行。

## 引入Web模块

当前的`pom.xml`内容如下，仅引入了两个模块：

- `spring-boot-starter`：核心模块，包括自动配置支持、日志和YAML
- `spring-boot-starter-test`：测试模块，包括JUnit、Hamcrest、Mockito

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter</artifactId>
	</dependency>

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
```

引入Web模块，需添加`spring-boot-starter-web`模块：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 编写HelloWorld服务

- 创建`package`命名为`com.didispace.web`（根据实际情况修改）
- 创建`HelloController`类，内容如下

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }

}
```

- 启动主程序，打开浏览器访问`http://localhost:8080/hello`，可以看到页面输出`Hello World`

## 编写单元测试用例

打开的`src/test/`下的测试入口`Chapter1ApplicationTests`类。下面编写一个简单的单元测试来模拟http请求，具体如下：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = MockServletContext.class)
@WebAppConfiguration
public class Chapter1ApplicationTests {

	private MockMvc mvc;

	@Before
	public void setUp() throws Exception {
		mvc = MockMvcBuilders.standaloneSetup(new HelloController()).build();
	}

	@Test
	public void getHello() throws Exception {
		mvc.perform(MockMvcRequestBuilders.get("/hello").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk())
				.andExpect(content().string(equalTo("Hello World")));
	}

}
```

使用`MockServletContext`来构建一个空的`WebApplicationContext`，这样我们创建的`HelloController`就可以在`@Before`函数中创建并传递到`MockMvcBuilders.standaloneSetup（）`函数中。

- 注意引入下面内容，让`status`、`content`、`equalTo`函数可用

```java
import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
```

至此已完成目标，通过Maven构建了一个空白Spring Boot项目，再通过引入web模块实现了一个简单的请求处理。



# 使用Intellij中的Spring Initializr来快速构建Spring Boot/Cloud工程

在之前的所有Spring Boot和Spring Cloud相关博文中，都会涉及Spring Boot工程的创建。而创建的方式多种多样，我们可以通过Maven来手工构建或是通过脚手架等方式快速搭建，也可以通过[《Spring Boot快速入门》](http://blog.didispace.com/spring-boot-learning-1/)一文中提到的`SPRING INITIALIZR`页面工具来创建，相信每位读者都有自己最喜欢和最为熟练的创建方式。

本文我们将介绍嵌入的Intellij中的Spring Initializr工具，它同Web提供的创建功能一样，可以帮助我们快速的构建出一个基础的Spring Boot/Cloud工程。

- 菜单栏中选择`File`=>`New`=>`Project..`，我们可以看到如下图所示的创建功能窗口。其中`Initial Service Url`指向的地址就是Spring官方提供的Spring Initializr工具地址，所以这里创建的工程实际上也是基于它的Web工具来实现的。

  [![img](http://blog.didispace.com/assets/spring-initializr-in-intellij-1.png)](http://blog.didispace.com/assets/spring-initializr-in-intellij-1.png)

- 点击`Next`，等待片刻后，我们可以看到如下图所示的工程信息窗口，在这里我们可以编辑我们想要创建的工程信息。其中，`Type`可以改变我们要构建的工程类型，比如：Maven、Gradle；`Language`可以选择：Java、Groovy、Kotlin。

  [![img](http://blog.didispace.com/assets/spring-initializr-in-intellij-2.png)](http://blog.didispace.com/assets/spring-initializr-in-intellij-2.png)

- 点击`Next`，进入选择Spring Boot版本和依赖管理的窗口。在这里值的我们关注的是，它不仅包含了Spring Boot Starter POMs中的各个依赖，还包含了Spring Cloud的各种依赖。

  [![img](http://blog.didispace.com/assets/spring-initializr-in-intellij-3.png)](http://blog.didispace.com/assets/spring-initializr-in-intellij-3.png)

- 点击`Next`，进入最后关于工程物理存储的一些细节。最后，点击`Finish`就能完成工程的构建了。

  [![img](http://blog.didispace.com/assets/spring-initializr-in-intellij-4.png)](http://blog.didispace.com/assets/spring-initializr-in-intellij-4.png)

Intellij中的`Spring Initializr`虽然还是基于官方Web实现，但是通过工具来进行调用并直接将结果构建到我们的本地文件系统中，让整个构建流程变得更加顺畅，还没有体验过此功能的Spring Boot/Cloud爱好者们不妨可以尝试一下这种不同的构建方式。

# 3 Spring Boot属性配置文件详解

相信很多人选择Spring Boot主要是考虑到它既能兼顾Spring的强大功能，还能实现快速开发的便捷。我们在Spring Boot使用过程中，最直观的感受就是没有了原来自己整合Spring应用时繁多的XML配置内容，替代它的是在`pom.xml`中引入模块化的`Starter POMs`，其中各个模块都有自己的默认配置，所以如果不是特殊应用场景，就只需要在`application.properties`中完成一些属性配置就能开启各模块的应用。

在之前的各篇文章中都有提及关于`application.properties`的使用，主要用来配置数据库连接、日志相关配置等。除了这些配置内容之外，本文将具体介绍一些在`application.properties`配置中的其他特性和使用方法。

## 自定义属性与加载

我们在使用Spring Boot的时候，通常也需要定义一些自己使用的属性，我们可以如下方式直接定义：

```properties
com.didispace.blog.name=程序猿DD
com.didispace.blog.title=Spring Boot教程
```

然后通过`@Value("${属性名}")`注解来加载对应的配置属性，具体如下：

```java
@Component
public class BlogProperties {

    @Value("${com.didispace.blog.name}")
    private String name;
    @Value("${com.didispace.blog.title}")
    private String title;

    // 省略getter和setter

}
```

按照惯例，通过单元测试来验证BlogProperties中的属性是否已经根据配置文件加载了。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private BlogProperties blogProperties;


	@Test
	public void getHello() throws Exception {
		Assert.assertEquals(blogProperties.getName(), "程序猿DD");
		Assert.assertEquals(blogProperties.getTitle(), "Spring Boot教程");
	}

}
```

## 参数间的引用

在`application.properties`中的各个参数之间也可以直接引用来使用，就像下面的设置：

```properties
com.didispace.blog.name=程序猿DD
com.didispace.blog.title=Spring Boot教程
com.didispace.blog.desc=${com.didispace.blog.name}正在努力写《${com.didispace.blog.title}》
```

`com.didispace.blog.desc`参数引用了上文中定义的`name`和`title`属性，最后该属性的值就是`程序猿DD正在努力写《Spring Boot教程》`。

## 使用随机数

在一些情况下，有些参数我们需要希望它不是一个固定的值，比如密钥、服务端口等。Spring Boot的属性配置文件中可以通过`${random}`来产生int值、long值或者string字符串，来支持属性的随机值。

```properties
# 随机字符串
com.didispace.blog.value=${random.value}
# 随机int
com.didispace.blog.number=${random.int}
# 随机long
com.didispace.blog.bignumber=${random.long}
# 10以内的随机数
com.didispace.blog.test1=${random.int(10)}
# 10-20的随机数
com.didispace.blog.test2=${random.int[10,20]}
```

## 通过命令行设置属性值

相信使用过一段时间Spring Boot的用户，一定知道这条命令：`java -jar xxx.jar --server.port=8888`，通过使用`–server.port`属性来设置xxx.jar应用的端口为8888。

在命令行运行时，**==连续的两个减号`--`就是对`application.properties`中的属性值进行赋值的标识。==**所以，`java -jar xxx.jar --server.port=8888`命令，等价于我们在`application.properties`中添加属性`server.port=8888`，该设置在样例工程中可见，读者可通过删除该值或使用命令行来设置该值来验证。

通过命令行来修改属性值固然提供了不错的便利性，但是通过命令行就能更改应用运行的参数，那岂不是很不安全？是的，所以Spring Boot也贴心的提供了屏蔽命令行访问属性的设置，只需要这句设置就能屏蔽：`SpringApplication.setAddCommandLineProperties(false)`。

## 多环境配置

我们在开发Spring Boot应用时，通常同一套程序会被应用和安装到几个不同的环境，比如：开发、测试、生产等。其中每个环境的数据库地址、服务器端口等等配置都会不同，如果在为不同环境打包时都要频繁修改配置文件的话，那必将是个非常繁琐且容易发生错误的事。

对于多环境的配置，各种项目构建工具或是框架的基本思路是一致的，通过配置多份不同环境的配置文件，再通过打包命令指定需要打包的内容之后进行区分打包，Spring Boot也不例外，或者说更加简单。

在Spring Boot中多环境配置文件名需要满足`application-{profile}.properties`的格式，其中`{profile}`对应你的环境标识，比如：

- `application-dev.properties`：开发环境
- `application-test.properties`：测试环境
- `application-prod.properties`：生产环境

至于哪个具体的配置文件会被加载，需要在`application.properties`文件中通过`spring.profiles.active`属性来设置，其值对应`{profile}`值。

如：`spring.profiles.active=test`就会加载`application-test.properties`配置文件内容

下面，以不同环境配置不同的服务端口为例，进行样例实验。

- 针对各环境新建不同的配置文件`application-dev.properties`、`application-test.properties`、`application-prod.properties`
- 在这三个文件均都设置不同的`server.port`属性，如：dev环境设置为1111，test环境设置为2222，prod环境设置为3333
- application.properties中设置`spring.profiles.active=dev`，就是说默认以dev环境设置
- 测试不同配置的加载
  - 执行`java -jar xxx.jar`，可以观察到服务端口被设置为`1111`，也就是默认的开发环境（dev）
  - 执行`java -jar xxx.jar --spring.profiles.active=test`，可以观察到服务端口被设置为`2222`，也就是测试环境的配置（test）
  - 执行`java -jar xxx.jar --spring.profiles.active=prod`，可以观察到服务端口被设置为`3333`，也就是生产环境的配置（prod）

按照上面的实验，可以如下总结多环境的配置思路：

- `application.properties`中配置通用内容，并设置`spring.profiles.active=dev`，以开发环境为默认配置
- `application-{profile}.properties`中配置各个环境不同的内容
- 通过命令行方式去激活不同环境的配置

[完整示例chapter2-1-1](http://git.oschina.net/didispace/SpringBoot-Learning)

# 4 Spring Boot构建RESTful API与单元测试

首先，回顾并详细说明一下在[快速入门](http://blog.didispace.com/spring-boot-learning-1/)中使用的`@Controller`、`@RestController`、`@RequestMapping`注解。如果您对Spring MVC不熟悉并且还没有尝试过快速入门案例，建议先看一下[快速入门](http://blog.didispace.com/spring-boot-learning-1/)的内容。

- `@Controller`：修饰class，用来创建处理http请求的对象
- `@RestController`：Spring4之后加入的注解，原来在`@Controller`中返回json需要`@ResponseBody`来配合，如果直接用`@RestController`替代`@Controller`就不需要再配置`@ResponseBody`，默认返回json格式。
- `@RequestMapping`：配置url映射

下面我们尝试使用Spring MVC来实现一组对User对象操作的RESTful API，配合注释详细说明在Spring MVC中如何映射HTTP请求、如何传参、如何编写单元测试。

**RESTful API具体设计如下：**

[![img](http://blog.didispace.com/content/images/posts/springbootrestfulapi-1.png)](http://blog.didispace.com/content/images/posts/springbootrestfulapi-1.png)

User实体定义：

```java
public class User { 
 
    private Long id; 
    private String name; 
    private Integer age; 
 
    // 省略setter和getter 
     
}
```

实现对User对象的操作接口

```java
@RestController 
@RequestMapping(value="/users")     // 通过这里配置使下面的映射都在/users下 
public class UserController { 
 
    // 创建线程安全的Map 
    static Map<Long, User> users = Collections.synchronizedMap(new HashMap<Long, User>()); 
 
    @RequestMapping(value="/", method=RequestMethod.GET) 
    public List<User> getUserList() { 
        // 处理"/users/"的GET请求，用来获取用户列表 
        // 还可以通过@RequestParam从页面中传递参数来进行查询条件或者翻页信息的传递 
        List<User> r = new ArrayList<User>(users.values()); 
        return r; 
    } 
 
    @RequestMapping(value="/", method=RequestMethod.POST) 
    public String postUser(@ModelAttribute User user) { 
        // 处理"/users/"的POST请求，用来创建User 
        // 除了@ModelAttribute绑定参数之外，还可以通过@RequestParam从页面中传递参数 
        users.put(user.getId(), user); 
        return "success"; 
    } 
 
    @RequestMapping(value="/{id}", method=RequestMethod.GET) 
    public User getUser(@PathVariable Long id) { 
        // 处理"/users/{id}"的GET请求，用来获取url中id值的User信息 
        // url中的id可通过@PathVariable绑定到函数的参数中 
        return users.get(id); 
    } 
 
    @RequestMapping(value="/{id}", method=RequestMethod.PUT) 
    public String putUser(@PathVariable Long id, @ModelAttribute User user) { 
        // 处理"/users/{id}"的PUT请求，用来更新User信息 
        User u = users.get(id); 
        u.setName(user.getName()); 
        u.setAge(user.getAge()); 
        users.put(id, u); 
        return "success"; 
    } 
 
    @RequestMapping(value="/{id}", method=RequestMethod.DELETE) 
    public String deleteUser(@PathVariable Long id) { 
        // 处理"/users/{id}"的DELETE请求，用来删除User 
        users.remove(id); 
        return "success"; 
    } 
 
}
```

下面针对该Controller编写测试用例验证正确性，具体如下。当然也可以通过浏览器插件等进行请求提交验证。

```java
@RunWith(SpringJUnit4ClassRunner.class) 
@SpringApplicationConfiguration(classes = MockServletContext.class) 
@WebAppConfiguration 
public class ApplicationTests { 
 
	private MockMvc mvc; 
 
	@Before 
	public void setUp() throws Exception { 
		mvc = MockMvcBuilders.standaloneSetup(new UserController()).build(); 
	} 
 
	@Test 
	public void testUserController() throws Exception { 
        // 测试UserController 
		RequestBuilder request = null; 
 
		// 1、get查一下user列表，应该为空 
		request = get("/users/"); 
		mvc.perform(request) 
				.andExpect(status().isOk()) 
				.andExpect(content().string(equalTo("[]"))); 
 
		// 2、post提交一个user 
		request = post("/users/") 
				.param("id", "1") 
				.param("name", "测试大师") 
				.param("age", "20"); 
		mvc.perform(request) 
		        .andExpect(content().string(equalTo("success"))); 
 
		// 3、get获取user列表，应该有刚才插入的数据 
		request = get("/users/"); 
		mvc.perform(request) 
				.andExpect(status().isOk()) 
				.andExpect(content().string(equalTo("[{\"id\":1,\"name\":\"测试大师\",\"age\":20}]"))); 
 
		// 4、put修改id为1的user 
		request = put("/users/1") 
				.param("name", "测试终极大师") 
				.param("age", "30"); 
		mvc.perform(request) 
				.andExpect(content().string(equalTo("success"))); 
 
		// 5、get一个id为1的user 
		request = get("/users/1"); 
		mvc.perform(request) 
				.andExpect(content().string(equalTo("{\"id\":1,\"name\":\"测试终极大师\",\"age\":30}"))); 
 
		// 6、del删除id为1的user 
		request = delete("/users/1"); 
		mvc.perform(request) 
				.andExpect(content().string(equalTo("success"))); 
 
		// 7、get查一下user列表，应该为空 
		request = get("/users/"); 
		mvc.perform(request) 
				.andExpect(status().isOk()) 
				.andExpect(content().string(equalTo("[]"))); 
 
	} 
 
}
```

至此，我们通过引入web模块（没有做其他的任何配置），就可以轻松利用Spring MVC的功能，以非常简洁的代码完成了对User对象的RESTful API的创建以及单元测试的编写。其中同时介绍了Spring MVC中最为常用的几个核心注解：`@Controller`,`@RestController`,`RequestMapping`以及一些参数绑定的注解：`@PathVariable`,`@ModelAttribute`,`@RequestParam`等。

[Spring Boot教程完整案例](http://git.oschina.net/didispace/SpringBoot-Learning)

# 5 Spring Boot开发Web应用

> [Spring Boot快速入门](http://blog.didispace.com/spring-boot-learning-1/)中我们完成了一个简单的RESTful Service，体验了快速开发的特性。在留言中也有朋友提到如何把处理结果渲染到页面上。那么本篇就在上篇基础上介绍一下如何进行Web应用的开发。

## 静态资源访问

在我们开发Web应用的时候，需要引用大量的js、css、图片等静态资源。

### 默认配置

Spring Boot默认提供静态资源目录位置需置于classpath下，目录名需符合如下规则：

- /static
- /public
- /resources
- /META-INF/resources

举例：我们可以在`src/main/resources/`目录下创建`static`，在该位置放置一个图片文件。启动程序后，尝试访问`http://localhost:8080/D.jpg`。如能显示图片，配置成功。

## 渲染Web页面

在之前的示例中，我们都是通过@RestController来处理请求，所以返回的内容为json对象。那么如果需要渲染html页面的时候，要如何实现呢？

### 模板引擎

在动态HTML实现上Spring Boot依然可以完美胜任，并且提供了多种模板引擎的默认配置支持，所以在推荐的模板引擎下，我们可以很快的上手开发动态网站。

Spring Boot提供了默认配置的模板引擎主要有以下几种：

- Thymeleaf
- FreeMarker
- Velocity
- Groovy
- Mustache

**Spring Boot建议使用这些模板引擎，避免使用JSP，若一定要使用JSP将无法实现Spring Boot的多种特性，具体可见后文：支持JSP的配置**

当你使用上述模板引擎中的任何一个，它们默认的模板配置路径为：`src/main/resources/templates`。当然也可以修改这个路径，具体如何修改，可在后续各模板引擎的配置属性中查询并修改。

#### Thymeleaf

Thymeleaf是一个XML/XHTML/HTML5模板引擎，可用于Web与非Web环境中的应用开发。它是一个开源的Java库，基于Apache License 2.0许可，由Daniel Fernández创建，该作者还是Java加密库Jasypt的作者。

Thymeleaf提供了一个用于整合Spring MVC的可选模块，在应用开发中，你可以使用Thymeleaf来完全代替JSP或其他模板引擎，如Velocity、FreeMarker等。Thymeleaf的主要目标在于提供一种可被浏览器正确显示的、格式良好的模板创建方式，因此也可以用作静态建模。你可以使用它创建经过验证的XML与HTML模板。相对于编写逻辑或代码，开发者只需将标签属性添加到模板中即可。接下来，这些标签属性就会在DOM（文档对象模型）上执行预先制定好的逻辑。

示例模板：

```html
<table>
  <thead>
    <tr>
      <th th:text="#{msgs.headers.name}">Name</td>
      <th th:text="#{msgs.headers.price}">Price</td>
    </tr>
  </thead>
  <tbody>
    <tr th:each="prod : ${allProducts}">
      <td th:text="${prod.name}">Oranges</td>
      <td th:text="${#numbers.formatDecimal(prod.price,1,2)}">0.99</td>
    </tr>
  </tbody>
</table>
```

可以看到Thymeleaf主要以属性的方式加入到html标签中，浏览器在解析html时，当检查到没有的属性时候会忽略，所以Thymeleaf的模板可以通过浏览器直接打开展现，这样非常有利于前后端的分离。

在Spring Boot中使用Thymeleaf，只需要引入下面依赖，并在默认的模板路径`src/main/resources/templates`下编写模板文件即可完成。

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

在完成配置之后，举一个简单的例子，在快速入门工程的基础上，举一个简单的示例来通过Thymeleaf渲染一个页面。

```java
@Controller
public class HelloController {

    @RequestMapping("/")
    public String index(ModelMap map) {
        // 加入一个属性，用来在模板中读取
        map.addAttribute("host", "http://blog.didispace.com");
        // return模板文件的名称，对应src/main/resources/templates/index.html
        return "index";  
    }

}
```

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8" />
    <title></title>
</head>
<body>
<h1 th:text="${host}">Hello World</h1>
</body>
</html>
```

如上页面，直接打开html页面展现Hello World，但是启动程序后，访问`http://localhost:8080/`，则是展示Controller中host的值：`http://blog.didispace.com`，做到了不破坏HTML自身内容的数据逻辑分离。

更多Thymeleaf的页面语法，还请访问Thymeleaf的官方文档查询使用。

**Thymeleaf的默认参数配置**

如有需要修改默认配置的时候，只需复制下面要修改的属性到`application.properties`中，并修改成需要的值，如修改模板文件的扩展名，修改默认的模板路径等。

```properties
# Enable template caching.
spring.thymeleaf.cache=true 
# Check that the templates location exists.
spring.thymeleaf.check-template-location=true 
# Content-Type value.
spring.thymeleaf.content-type=text/html 
# Enable MVC Thymeleaf view resolution.
spring.thymeleaf.enabled=true 
# Template encoding.
spring.thymeleaf.encoding=UTF-8 
# Comma-separated list of view names that should be excluded from resolution.
spring.thymeleaf.excluded-view-names= 
# Template mode to be applied to templates. See also StandardTemplateModeHandlers.
spring.thymeleaf.mode=HTML5 
# Prefix that gets prepended to view names when building a URL.
spring.thymeleaf.prefix=classpath:/templates/ 
# Suffix that gets appended to view names when building a URL.
spring.thymeleaf.suffix=.html  spring.thymeleaf.template-resolver-order= # Order of the template resolver in the chain. spring.thymeleaf.view-names= # Comma-separated list of view names that can be resolved.
```

### 支持JSP的配置

Spring Boot并不建议使用jsp，但如果一定要使用，可以参考此工程作为脚手架：[JSP支持](https://github.com/spring-projects/spring-boot/tree/v1.3.2.RELEASE/spring-boot-samples/spring-boot-sample-web-jsp)

[Spring Boot教程完整案例](http://git.oschina.net/didispace/SpringBoot-Learning)

# 6 Spring Boot中使用Swagger2构建强大的RESTful API文档

由于Spring Boot能够快速开发、便捷部署等特性，相信有很大一部分Spring Boot的用户会用来构建RESTful API。而我们构建RESTful API的目的通常都是由于多终端的原因，这些终端会共用很多底层业务逻辑，因此我们会抽象出这样一层来同时服务于多个移动端或者Web前端。

这样一来，我们的RESTful API就有可能要面对多个开发人员或多个开发团队：IOS开发、Android开发或是Web开发等。为了减少与其他团队平时开发期间的频繁沟通成本，传统做法我们会创建一份RESTful API文档来记录所有接口细节，然而这样的做法有以下几个问题：

- 由于接口众多，并且细节复杂（需要考虑不同的HTTP请求类型、HTTP头部信息、HTTP请求内容等），高质量地创建这份文档本身就是件非常吃力的事，下游的抱怨声不绝于耳。
- 随着时间推移，不断修改接口实现的时候都必须同步修改接口文档，而文档与代码又处于两个不同的媒介，除非有严格的管理机制，不然很容易导致不一致现象。

为了解决上面这样的问题，本文将介绍RESTful API的重磅好伙伴Swagger2，它可以轻松的整合到Spring Boot中，并与Spring MVC程序配合组织出强大RESTful API文档。它既可以减少我们创建文档的工作量，同时说明内容又整合入实现代码中，让维护文档和修改代码整合为一体，可以让我们在修改代码逻辑的同时方便的修改文档说明。另外Swagger2也提供了强大的页面测试功能来调试每个RESTful API。具体效果如下图所示：

[![alt=](http://blog.didispace.com/content/images/2016/04/swagger2_1.png)](http://blog.didispace.com/content/images/2016/04/swagger2_1.png)

下面来具体介绍，如果在Spring Boot中使用Swagger2。首先，我们需要一个Spring Boot实现的RESTful API工程，若您没有做过这类内容，建议先阅读
[Spring Boot构建一个较为复杂的RESTful APIs和单元测试](http://blog.didispace.com/springbootrestfulapi/)。

下面的内容我们会以[教程样例](http://git.oschina.net/didispace/SpringBoot-Learning)中的Chapter3-1-1进行下面的实验（Chpater3-1-5是我们的结果工程，亦可参考）。

#### 添加Swagger2依赖

在`pom.xml`中加入Swagger2的依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.2.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.2.2</version>
</dependency>
```

#### 创建Swagger2配置类

在`Application.java`同级创建Swagger2的配置类`Swagger2`。

```java
@Configuration
@EnableSwagger2
public class Swagger2 {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.didispace.web"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Spring Boot中使用Swagger2构建RESTful APIs")
                .description("更多Spring Boot相关文章请关注：http://blog.didispace.com/")
                .termsOfServiceUrl("http://blog.didispace.com/")
                .contact("程序猿DD")
                .version("1.0")
                .build();
    }

}
```

如上代码所示，通过`@Configuration`注解，让Spring来加载该类配置。再通过`@EnableSwagger2`注解来启用Swagger2。

再通过`createRestApi`函数创建`Docket`的Bean之后，`apiInfo()`用来创建该Api的基本信息（这些基本信息会展现在文档页面中）。`select()`函数返回一个`ApiSelectorBuilder`实例用来控制哪些接口暴露给Swagger来展现，本例采用指定扫描的包路径来定义，Swagger会扫描该包下所有Controller定义的API，并产生文档内容（除了被`@ApiIgnore`指定的请求）。

#### 添加文档内容

在完成了上述配置后，其实已经可以生产文档内容，但是这样的文档主要针对请求本身，而描述主要来源于函数等命名产生，对用户并不友好，我们通常需要自己增加一些说明来丰富文档内容。如下所示，我们通过`@ApiOperation`注解来给API增加说明、通过`@ApiImplicitParams`、`@ApiImplicitParam`注解来给参数增加说明。

```java
@RestController
@RequestMapping(value="/users")     // 通过这里配置使下面的映射都在/users下，可去除
public class UserController {

    static Map<Long, User> users = Collections.synchronizedMap(new HashMap<Long, User>());

    @ApiOperation(value="获取用户列表", notes="")
    @RequestMapping(value={""}, method=RequestMethod.GET)
    public List<User> getUserList() {
        List<User> r = new ArrayList<User>(users.values());
        return r;
    }

    @ApiOperation(value="创建用户", notes="根据User对象创建用户")
    @ApiImplicitParam(name = "user", value = "用户详细实体user", required = true, dataType = "User")
    @RequestMapping(value="", method=RequestMethod.POST)
    public String postUser(@RequestBody User user) {
        users.put(user.getId(), user);
        return "success";
    }

    @ApiOperation(value="获取用户详细信息", notes="根据url的id来获取用户详细信息")
    @ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long")
    @RequestMapping(value="/{id}", method=RequestMethod.GET)
    public User getUser(@PathVariable Long id) {
        return users.get(id);
    }

    @ApiOperation(value="更新用户详细信息", notes="根据url的id来指定更新对象，并根据传过来的user信息来更新用户详细信息")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long"),
            @ApiImplicitParam(name = "user", value = "用户详细实体user", required = true, dataType = "User")
    })
    @RequestMapping(value="/{id}", method=RequestMethod.PUT)
    public String putUser(@PathVariable Long id, @RequestBody User user) {
        User u = users.get(id);
        u.setName(user.getName());
        u.setAge(user.getAge());
        users.put(id, u);
        return "success";
    }

    @ApiOperation(value="删除用户", notes="根据url的id来指定删除对象")
    @ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long")
    @RequestMapping(value="/{id}", method=RequestMethod.DELETE)
    public String deleteUser(@PathVariable Long id) {
        users.remove(id);
        return "success";
    }

}
```

完成上述代码添加上，启动Spring Boot程序，访问：<http://localhost:8080/swagger-ui.html>
。就能看到前文所展示的RESTful API的页面。我们可以再点开具体的API请求，以POST类型的/users请求为例，可找到上述代码中我们配置的Notes信息以及参数user的描述信息，如下图所示。

[![alt](http://blog.didispace.com/content/images/2016/04/swagger2_2.png)](http://blog.didispace.com/content/images/2016/04/swagger2_2.png)

#### API文档访问与调试

在上图请求的页面中，我们看到user的Value是个输入框？是的，Swagger除了查看接口功能外，还提供了调试测试功能，我们可以点击上图中右侧的Model Schema（黄色区域：它指明了User的数据结构），此时Value中就有了user对象的模板，我们只需要稍适修改，点击下方`“Try it out！”`按钮，即可完成了一次请求调用！

此时，你也可以通过几个GET请求来验证之前的POST请求是否正确。

相比为这些接口编写文档的工作，我们增加的配置内容是非常少而且精简的，对于原有代码的侵入也在忍受范围之内。因此，在构建RESTful API的同时，加入swagger来对API文档进行管理，是个不错的选择。

完整结果示例可查看[Chapter3-1-5](http://git.oschina.net/didispace/SpringBoot-Learning)。

#### 参考信息

- [Swagger官方网站](http://swagger.io/)

# 7 Spring Boot中Web应用的统一异常处理

我们在做Web应用的时候，请求处理过程中发生错误是非常常见的情况。Spring Boot提供了一个默认的映射：`/error`，当处理中抛出异常之后，会转到该请求中处理，并且该请求有一个全局的错误页面用来展示异常内容。

选择一个之前实现过的Web应用（[Chapter3-1-2](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter3-1-2)）为基础，启动该应用，访问一个不存在的URL，或是修改处理内容，直接抛出异常，如：

```java
@RequestMapping("/hello")
public String hello() throws Exception {
    throw new Exception("发生错误");
}
```

此时，可以看到类似下面的报错页面，该页面就是Spring Boot提供的默认error映射页面。

[![alt=默认的错误页面](http://blog.didispace.com/content/images/2016/04/241FA8A7-2493-44B9-A0A3-79849656074A.png)](http://blog.didispace.com/content/images/2016/04/241FA8A7-2493-44B9-A0A3-79849656074A.png)

## 统一异常处理

虽然，Spring Boot中实现了默认的error映射，但是在实际应用中，上面你的错误页面对用户来说并不够友好，我们通常需要去实现我们自己的异常提示。

下面我们以之前的Web应用例子为基础（[Chapter3-1-2](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter3-1-2)），进行统一异常处理的改造。

- 创建全局异常处理类：通过使用`@ControllerAdvice`定义统一的异常处理类，而不是在每个Controller中逐个定义。`@ExceptionHandler`用来定义函数针对的异常类型，最后将Exception对象和请求URL映射到`error.html`中

```java
@ControllerAdvice
class GlobalExceptionHandler {

    public static final String DEFAULT_ERROR_VIEW = "error";

    @ExceptionHandler(value = Exception.class)
    public ModelAndView defaultErrorHandler(HttpServletRequest req, Exception e) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("exception", e);
        mav.addObject("url", req.getRequestURL());
        mav.setViewName(DEFAULT_ERROR_VIEW);
        return mav;
    }

}
```

- 实现`error.html`页面展示：在`templates`目录下创建`error.html`，将请求的URL和Exception对象的message输出。

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8" />
    <title>统一异常处理</title>
</head>
<body>
    <h1>Error Handler</h1>
    <div th:text="${url}"></div>
    <div th:text="${exception.message}"></div>
</body>
</html>
```

启动该应用，访问：`http://localhost:8080/hello`，可以看到如下错误提示页面。

[![alt=自定义的错误页面](http://blog.didispace.com/content/images/2016/05/8C9EACEE-9F7C-42F3-85D1-B5CAD746FA86.png)](http://blog.didispace.com/content/images/2016/05/8C9EACEE-9F7C-42F3-85D1-B5CAD746FA86.png)

*通过实现上述内容之后，我们只需要在Controller中抛出Exception，当然我们可能会有多种不同的Exception。然后在@ControllerAdvice类中，根据抛出的具体Exception类型匹配@ExceptionHandler中配置的异常类型来匹配错误映射和处理。*

## 返回JSON格式

在上述例子中，通过`@ControllerAdvice`统一定义不同Exception映射到不同错误处理页面。而当我们要实现RESTful API时，返回的错误是JSON格式的数据，而不是HTML页面，这时候我们也能轻松支持。

本质上，只需在`@ExceptionHandler`之后加入`@ResponseBody`，就能让处理函数return的内容转换为JSON格式。

下面以一个具体示例来实现返回JSON格式的异常处理。

- 创建统一的JSON返回对象，code：消息类型，message：消息内容，url：请求的url，data：请求返回的数据

```java
public class ErrorInfo<T> {

    public static final Integer OK = 0;
    public static final Integer ERROR = 100;

    private Integer code;
    private String message;
    private String url;
    private T data;

    // 省略getter和setter

}
```

- 创建一个自定义异常，用来实验捕获该异常，并返回json

```java
public class MyException extends Exception {

    public MyException(String message) {
        super(message);
    }
    
}
```

- `Controller`中增加json映射，抛出`MyException`异常

```
@Controller
public class HelloController {

    @RequestMapping("/json")
    public String json() throws MyException {
        throw new MyException("发生错误2");
    }

}
```

- 为`MyException`异常创建对应的处理

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = MyException.class)
    @ResponseBody
    public ErrorInfo<String> jsonErrorHandler(HttpServletRequest req, MyException e) throws Exception {
        ErrorInfo<String> r = new ErrorInfo<>();
        r.setMessage(e.getMessage());
        r.setCode(ErrorInfo.ERROR);
        r.setData("Some Data");
        r.setUrl(req.getRequestURL().toString());
        return r;
    }

}
```

- 启动应用，访问：[http://localhost:8080/json，可以得到如下返回内容：](http://localhost:8080/json%EF%BC%8C%E5%8F%AF%E4%BB%A5%E5%BE%97%E5%88%B0%E5%A6%82%E4%B8%8B%E8%BF%94%E5%9B%9E%E5%86%85%E5%AE%B9%EF%BC%9A)

```json
{
    code: 100，
    data: "Some Data"，
    message: "发生错误2"，
    url: "http://localhost:8080/json"
}
```

至此，已完成在Spring Boot中创建统一的异常处理，实际实现还是依靠Spring MVC的注解，更多更深入的使用可参考Spring MVC的文档。

本文完整示例：[chapter3-1-6](http://git.oschina.net/didispace/SpringBoot-Learning)



# 8 Spring Boot中使用JdbcTemplate访问数据库

之前介绍了很多Web层的例子，包括[构建RESTful API](http://blog.didispace.com/springbootrestfulapi/)、[使用Thymeleaf模板引擎渲染Web视图](http://blog.didispace.com/springbootweb/)，但是这些内容还不足以构建一个动态的应用。通常我们做App也好，做Web应用也好，都需要内容，而内容通常存储于各种类型的数据库，服务端在接收到访问请求之后需要访问数据库获取并处理成展现给用户使用的数据形式。

本文介绍在Spring Boot基础下配置数据源和通过JdbcTemplate编写数据访问的示例。

## 数据源配置

在我们访问数据库的时候，需要先配置一个数据源，下面分别介绍一下几种不同的数据库配置方式。

首先，为了连接数据库需要引入jdbc支持，在`pom.xml`中引入如下配置：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

#### 嵌入式数据库支持

嵌入式数据库通常用于**==开发和测试环境==**，不推荐用于生产环境。Spring Boot提供自动配置的嵌入式数据库有H2、HSQL、Derby，你不需要提供任何连接配置就能使用。

比如，我们可以在`pom.xml`中引入如下配置使用HSQL

```xml
<dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>hsqldb</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### 连接生产数据源

以MySQL数据库为例，先引入MySQL连接的依赖包，在`pom.xml`中加入：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.21</version>
</dependency>
```

在`src/main/resources/application.properties`中配置数据源信息

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

#### 连接JNDI数据源

当你将应用部署于应用服务器上的时候想让数据源由应用服务器管理，那么可以使用如下配置方式引入JNDI数据源。

```properties
spring.datasource.jndi-name=java:jboss/datasources/customers
```

## 使用JdbcTemplate操作数据库

Spring的JdbcTemplate是自动配置的，你可以直接使用`@Autowired`来注入到你自己的bean中来使用。

举例：我们在创建`User`表，包含属性`name`、`age`，下面来编写数据访问对象和单元测试用例。

- 定义包含有插入、删除、查询的抽象接口UserService

```java
public interface UserService {

    /**
     * 新增一个用户
     * @param name
     * @param age
     */
    void create(String name, Integer age);

    /**
     * 根据name删除一个用户高
     * @param name
     */
    void deleteByName(String name);

    /**
     * 获取用户总量
     */
    Integer getAllUsers();

    /**
     * 删除所有用户
     */
    void deleteAllUsers();

}
```

- 通过JdbcTemplate实现UserService中定义的数据访问操作

```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public void create(String name, Integer age) {
        jdbcTemplate.update("insert into USER(NAME, AGE) values(?, ?)", name, age);
    }

    @Override
    public void deleteByName(String name) {
        jdbcTemplate.update("delete from USER where NAME = ?", name);
    }

    @Override
    public Integer getAllUsers() {
        return jdbcTemplate.queryForObject("select count(1) from USER", Integer.class);
    }

    @Override
    public void deleteAllUsers() {
        jdbcTemplate.update("delete from USER");
    }
}
```

- 创建对UserService的单元测试用例，通过创建、删除和查询来验证数据库操作的正确性。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private UserService userSerivce;

	@Before
	public void setUp() {
		// 准备，清空user表
		userSerivce.deleteAllUsers();
	}

	@Test
	public void test() throws Exception {
		// 插入5个用户
		userSerivce.create("a", 1);
		userSerivce.create("b", 2);
		userSerivce.create("c", 3);
		userSerivce.create("d", 4);
		userSerivce.create("e", 5);

		// 查数据库，应该有5个用户
		Assert.assertEquals(5, userSerivce.getAllUsers().intValue());

		// 删除两个用户
		userSerivce.deleteByName("a");
		userSerivce.deleteByName("e");

		// 查数据库，应该有5个用户
		Assert.assertEquals(3, userSerivce.getAllUsers().intValue());

	}

}
```

*上面介绍的JdbcTemplate只是最基本的几个操作，更多其他数据访问操作的使用请参考：JdbcTemplate API*

通过上面这个简单的例子，我们可以看到在Spring Boot下访问数据库的配置依然秉承了框架的初衷：简单。我们只需要在pom.xml中加入数据库依赖，再到application.properties中配置连接信息，不需要像Spring应用中创建JdbcTemplate的Bean，就可以直接在自己的对象中注入使用。

[本文完整示例](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master)



# 9 Spring Boot中使用Spring-data-jpa让数据访问更简单、更优雅

在上一篇[Spring中使用JdbcTemplate访问数据库 ](http://blog.didispace.com/springbootdata1/)中介绍了一种基本的数据访问方式，结合[构建RESTful API](http://blog.didispace.com/springbootrestfulapi/)和[使用Thymeleaf模板引擎渲染Web视图](http://blog.didispace.com/springbootweb/)的内容就已经可以完成App服务端和Web站点的开发任务了。

然而，在实际开发过程中，对数据库的操作无非就“增删改查”。就最为普遍的单表操作而言，除了表和字段不同外，语句都是类似的，开发人员需要写大量类似而枯燥的语句来完成业务逻辑。

为了解决这些大量枯燥的数据操作语句，我们第一个想到的是使用ORM框架，比如：Hibernate。通过整合Hibernate之后，我们以操作Java实体的方式最终将数据改变映射到数据库表中。

为了解决抽象各个Java实体基本的“增删改查”操作，我们通常会==**以泛型的方式封装一个模板Dao来进行抽象简化**==，但是这样依然不是很方便，我们需要针对每个实体编写一个继承自泛型模板Dao的接口，再编写该接口的实现。虽然一些基础的数据访问已经可以得到很好的复用，但是在代码结构上针对每个实体都会有一堆Dao的接口和实现。

由于模板Dao的实现，使得这些具体实体的Dao层已经变的非常“薄”，有一些具体实体的Dao实现可能完全就是对模板Dao的简单代理，并且往往这样的实现类可能会出现在很多实体上。Spring-data-jpa的出现正可以让这样一个已经很“薄”的数据访问层变成只是一层接口的编写方式。比如，下面的例子：

```java
public interface UserRepository extends JpaRepository<User, Long> {

    User findByName(String name);

    @Query("from User u where u.name=:name")
    User findUser(@Param("name") String name);

}
```

我们只需要通过编写一个继承自`JpaRepository`的接口就能完成数据访问，下面以一个具体实例来体验Spring-data-jpa给我们带来的强大功能。

## 使用示例

由于==**Spring-data-jpa依赖于Hibernate**==。如果您对Hibernate有一定了解，下面内容可以毫不费力的看懂并上手使用Spring-data-jpa。如果您还是Hibernate新手，您可以先按如下方式入门，再建议回头学习一下Hibernate以帮助这部分的理解和进一步使用。

#### 工程配置

在`pom.xml`中添加相关依赖，加入以下内容：

```xml
<dependency
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

在`application.xml`中配置：数据库连接信息（如使用嵌入式数据库则不需要）、自动创建表结构的设置，例如使用mysql的情况如下：

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.jpa.properties.hibernate.hbm2ddl.auto=create-drop
```

`spring.jpa.properties.hibernate.hbm2ddl.auto`是hibernate的配置属性，其主要作用是：自动创建、更新、验证数据库表结构。该参数的几种配置如下：

- `create`：每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再**==重新来生成新==**表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
- `create-drop`：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
- `update`：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
- `validate`：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。

至此已经完成基础配置，如果您有在Spring下整合使用过它的话，相信你已经感受到Spring Boot的便利之处：**==JPA的传统配置在`persistence.xml`文件中==**，但是这里我们不需要。当然，最好在构建项目时候按照之前提过的[最佳实践的工程结构](http://blog.didispace.com/springbootproject/)来组织，这样以确保各种配置都能被框架扫描到。

#### 创建实体

创建一个User实体，包含id（主键）、name（姓名）、age（年龄）属性，通过ORM框架其会被映射到数据库表中，由于配置了`hibernate.hbm2ddl.auto`，在应用启动的时候框架会自动去数据库中创建对应的表。

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private Integer age;

    // 省略构造函数

    // 省略getter和setter

}
```

#### 创建数据访问接口

下面针对User实体创建对应的`Repository`接口实现对该实体的数据访问，如下代码：

```java
public interface UserRepository extends JpaRepository<User, Long> {

    User findByName(String name);

    User findByNameAndAge(String name, Integer age);

    @Query("from User u where u.name=:name")
    User findUser(@Param("name") String name);

}
```

在Spring-data-jpa中，只需要编写类似上面这样的接口就可实现数据访问。==**不再像我们以往编写了接口时候还需要自己编写接口实现类**==，直接减少了我们的文件清单。

下面对上面的`UserRepository`做一些解释，该接口继承自`JpaRepository`，通过查看`JpaRepository`接口的[API文档](http://docs.spring.io/spring-data/data-jpa/docs/current/api/)，可以看到该接口本身已经实现了创建（save）、更新（save）、删除（delete）、查询（findAll、findOne）等基本操作的函数，因此对于这些基础操作的数据访问就不需要开发者再自己定义。

在我们实际开发中，`JpaRepository`接口定义的接口往往还不够或者性能不够优化，我们需要进一步实现更复杂一些的查询或操作。由于本文重点在spring boot中整合spring-data-jpa，在这里先抛砖引玉简单介绍一下spring-data-jpa中让我们兴奋的功能，后续再单独开篇讲一下spring-data-jpa中的常见使用。

在上例中，我们可以看到下面两个函数：

- `User findByName(String name)`
- `User findByNameAndAge(String name, Integer age)`

它们分别实现了按name查询User实体和按name和age查询User实体，可以看到我们这里没有任何类SQL语句就完成了两个条件查询方法。这就是Spring-data-jpa的一大特性：**通过解析方法名创建查询**。

除了通过解析方法名来创建查询外，它也提供通过使用@Query 注解来创建查询，您只需要编写JPQL语句，并通过类似“:name”来映射@Param指定的参数，就像例子中的第三个findUser函数一样。

**Spring-data-jpa的能力远不止本文提到的这些，由于本文主要以整合介绍为主，对于Spring-data-jpa的使用只是介绍了常见的使用方式。诸如@Modifying操作、分页排序、原生SQL支持以及与Spring MVC的结合使用等等内容就不在本文中详细展开，这里先挖个坑，后续再补文章填坑，如您对这些感兴趣可以关注我博客或简书，同样欢迎大家留言交流想法。**

#### 单元测试

在完成了上面的数据访问接口之后，按照惯例就是编写对应的单元测试来验证编写的内容是否正确。这里就不多做介绍，主要通过数据操作和查询来反复验证操作的正确性。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private UserRepository userRepository;

	@Test
	public void test() throws Exception {

		// 创建10条记录
		userRepository.save(new User("AAA", 10));
		userRepository.save(new User("BBB", 20));
		userRepository.save(new User("CCC", 30));
		userRepository.save(new User("DDD", 40));
		userRepository.save(new User("EEE", 50));
		userRepository.save(new User("FFF", 60));
		userRepository.save(new User("GGG", 70));
		userRepository.save(new User("HHH", 80));
		userRepository.save(new User("III", 90));
		userRepository.save(new User("JJJ", 100));

		// 测试findAll, 查询所有记录
		Assert.assertEquals(10, userRepository.findAll().size());

		// 测试findByName, 查询姓名为FFF的User
		Assert.assertEquals(60, userRepository.findByName("FFF").getAge().longValue());

		// 测试findUser, 查询姓名为FFF的User
		Assert.assertEquals(60, userRepository.findUser("FFF").getAge().longValue());

		// 测试findByNameAndAge, 查询姓名为FFF并且年龄为60的User
		Assert.assertEquals("FFF", userRepository.findByNameAndAge("FFF", 60).getName());

		// 测试删除姓名为AAA的User
		userRepository.delete(userRepository.findByName("AAA"));

		// 测试findAll, 查询所有记录, 验证上面的删除是否成功
		Assert.assertEquals(9, userRepository.findAll().size());

	}


}
```

[完整示例](http://git.oschina.net/didispace/SpringBoot-Learning)

# 10 Spring Boot多数据源配置与使用

之前在介绍使用JdbcTemplate和Spring-data-jpa时，都使用了单数据源。在单数据源的情况下，Spring Boot的配置非常简单，只需要在`application.properties`文件中配置连接参数即可。但是往往随着业务量发展，我们通常会进行数据库拆分或是引入其他数据库，从而我们需要配置多个数据源，下面基于之前的JdbcTemplate和Spring-data-jpa例子分别介绍两种多数据源的配置方式。

### 多数据源配置

创建一个Spring配置类，定义两个DataSource用来读取`application.properties`中的不同配置。如下例子中，主数据源配置为`spring.datasource.primary`开头的配置，第二数据源配置为`spring.datasource.secondary`开头的配置。

```java
@Configuration
public class DataSourceConfig {

    @Bean(name = "primaryDataSource")
    @Qualifier("primaryDataSource")
    @ConfigurationProperties(prefix="spring.datasource.primary")
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "secondaryDataSource")
    @Qualifier("secondaryDataSource")
    @Primary
    @ConfigurationProperties(prefix="spring.datasource.secondary")
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create().build();
    }

}
```

对应的`application.properties`配置如下：

```
spring.datasource.primary.url=jdbc:mysql://localhost:3306/test1
spring.datasource.primary.username=root
spring.datasource.primary.password=root
spring.datasource.primary.driver-class-name=com.mysql.jdbc.Driver

spring.datasource.secondary.url=jdbc:mysql://localhost:3306/test2
spring.datasource.secondary.username=root
spring.datasource.secondary.password=root
spring.datasource.secondary.driver-class-name=com.mysql.jdbc.Driver
```

### JdbcTemplate支持

对JdbcTemplate的支持比较简单，只需要为其注入对应的datasource即可，如下例子，在创建JdbcTemplate的时候分别注入名为`primaryDataSource`和`secondaryDataSource`的数据源来区分不同的JdbcTemplate。

```java
@Bean(name = "primaryJdbcTemplate")
public JdbcTemplate primaryJdbcTemplate(
        @Qualifier("primaryDataSource") DataSource dataSource) {
    return new JdbcTemplate(dataSource);
}

@Bean(name = "secondaryJdbcTemplate")
public JdbcTemplate secondaryJdbcTemplate(
        @Qualifier("secondaryDataSource") DataSource dataSource) {
    return new JdbcTemplate(dataSource);
}
```

接下来通过测试用例来演示如何使用这两个针对不同数据源的JdbcTemplate。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	@Qualifier("primaryJdbcTemplate")
	protected JdbcTemplate jdbcTemplate1;

	@Autowired
	@Qualifier("secondaryJdbcTemplate")
	protected JdbcTemplate jdbcTemplate2;

	@Before
	public void setUp() {
		jdbcTemplate1.update("DELETE  FROM  USER ");
		jdbcTemplate2.update("DELETE  FROM  USER ");
	}

	@Test
	public void test() throws Exception {

		// 往第一个数据源中插入两条数据
		jdbcTemplate1.update("insert into user(id,name,age) values(?, ?, ?)", 1, "aaa", 20);
		jdbcTemplate1.update("insert into user(id,name,age) values(?, ?, ?)", 2, "bbb", 30);

		// 往第二个数据源中插入一条数据，若插入的是第一个数据源，则会主键冲突报错
		jdbcTemplate2.update("insert into user(id,name,age) values(?, ?, ?)", 1, "aaa", 20);

		// 查一下第一个数据源中是否有两条数据，验证插入是否成功
		Assert.assertEquals("2", jdbcTemplate1.queryForObject("select count(1) from user", String.class));

		// 查一下第一个数据源中是否有两条数据，验证插入是否成功
		Assert.assertEquals("1", jdbcTemplate2.queryForObject("select count(1) from user", String.class));

	}


}
```

[完整示例:Chapter3-2-3](http://git.oschina.net/didispace/SpringBoot-Learning)

### Spring-data-jpa支持

对于数据源的配置可以沿用上例中`DataSourceConfig`的实现。

新增对第一数据源的JPA配置，注意两处注释的地方，用于指定数据源对应的`Entity`实体和`Repository`定义位置，用`@Primary`区分主数据源。

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef="entityManagerFactoryPrimary",
        transactionManagerRef="transactionManagerPrimary",
        basePackages= { "com.didispace.domain.p" }) //设置Repository所在位置
public class PrimaryConfig {

    @Autowired @Qualifier("primaryDataSource")
    private DataSource primaryDataSource;

    @Primary
    @Bean(name = "entityManagerPrimary")
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return entityManagerFactoryPrimary(builder).getObject().createEntityManager();
    }

    @Primary
    @Bean(name = "entityManagerFactoryPrimary")
    public LocalContainerEntityManagerFactoryBean entityManagerFactoryPrimary (EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(primaryDataSource)
                .properties(getVendorProperties(primaryDataSource))
                .packages("com.didispace.domain.p") //设置实体类所在位置
                .persistenceUnit("primaryPersistenceUnit")
                .build();
    }

    @Autowired
    private JpaProperties jpaProperties;

    private Map<String, String> getVendorProperties(DataSource dataSource) {
        return jpaProperties.getHibernateProperties(dataSource);
    }

    @Primary
    @Bean(name = "transactionManagerPrimary")
    public PlatformTransactionManager transactionManagerPrimary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(entityManagerFactoryPrimary(builder).getObject());
    }

}
```

新增对第二数据源的JPA配置，内容与第一数据源类似，具体如下：

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef="entityManagerFactorySecondary",
        transactionManagerRef="transactionManagerSecondary",
        basePackages= { "com.didispace.domain.s" }) //设置Repository所在位置
public class SecondaryConfig {

    @Autowired @Qualifier("secondaryDataSource")
    private DataSource secondaryDataSource;

    @Bean(name = "entityManagerSecondary")
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return entityManagerFactorySecondary(builder).getObject().createEntityManager();
    }

    @Bean(name = "entityManagerFactorySecondary")
    public LocalContainerEntityManagerFactoryBean entityManagerFactorySecondary (EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(secondaryDataSource)
                .properties(getVendorProperties(secondaryDataSource))
                .packages("com.didispace.domain.s") //设置实体类所在位置
                .persistenceUnit("secondaryPersistenceUnit")
                .build();
    }

    @Autowired
    private JpaProperties jpaProperties;

    private Map<String, String> getVendorProperties(DataSource dataSource) {
        return jpaProperties.getHibernateProperties(dataSource);
    }

    @Bean(name = "transactionManagerSecondary")
    PlatformTransactionManager transactionManagerSecondary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(entityManagerFactorySecondary(builder).getObject());
    }

}
```

完成了以上配置之后，主数据源的实体和数据访问对象位于：`com.didispace.domain.p`，次数据源的实体和数据访问接口位于：`com.didispace.domain.s`。

分别在这两个package下创建==**各自的实体和数据访问接口**==

- 主数据源下，创建User实体和对应的Repository接口

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private Integer age;

    public User(){}

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    // 省略getter、setter

}
```

```java
public interface UserRepository extends JpaRepository<User, Long> {

}
```

- 从数据源下，创建Message实体和对应的Repository接口

```java
@Entity
public class Message {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String content;

    public Message(){}

    public Message(String name, String content) {
        this.name = name;
        this.content = content;
    }

    // 省略getter、setter

}
```

```java
public interface MessageRepository extends JpaRepository<Message, Long> {

}
```

接下来通过测试用例来验证使用这两个针对不同数据源的配置进行数据操作。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private UserRepository userRepository;
	@Autowired
	private MessageRepository messageRepository;

	@Test
	public void test() throws Exception {

		userRepository.save(new User("aaa", 10));
		userRepository.save(new User("bbb", 20));
		userRepository.save(new User("ccc", 30));
		userRepository.save(new User("ddd", 40));
		userRepository.save(new User("eee", 50));

		Assert.assertEquals(5, userRepository.findAll().size());

		messageRepository.save(new Message("o1", "aaaaaaaaaa"));
		messageRepository.save(new Message("o2", "bbbbbbbbbb"));
		messageRepository.save(new Message("o3", "cccccccccc"));

		Assert.assertEquals(3, messageRepository.findAll().size());

	}

}
```

[完整示例:Chapter3-2-4](http://git.oschina.net/didispace/SpringBoot-Learning)



# 11 Spring Boot中使用Redis数据库

Spring Boot中除了对常用的关系型数据库提供了优秀的自动化支持之外，对于很多NoSQL数据库一样提供了自动化配置的支持，包括：Redis, MongoDB, Elasticsearch, Solr和Cassandra。

## 使用Redis

Redis是一个开源的使用ANSIC语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库。

- [Redis官网](http://redis.io/)
- [Redis中文社区](http://www.redis.cn/)

#### 引入依赖

Spring Boot提供的数据访问框架Spring Data Redis基于Jedis。可以通过引入`spring-boot-starter-redis`来配置依赖关系。

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
```

#### 参数配置

按照惯例在`application.properties`中加入Redis服务端的相关配置，具体说明如下：

```properties
# REDIS (RedisProperties)
# Redis数据库索引（默认为0）
spring.redis.database=0
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=0
```

**其中spring.redis.database的配置通常使用0即可，Redis在配置的时候可以设置数据库数量，默认为16，可以理解为数据库的schema**

#### 测试访问

通过编写测试用例，举例说明如何访问Redis。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private StringRedisTemplate stringRedisTemplate;

	@Test
	public void test() throws Exception {

		// 保存字符串
		stringRedisTemplate.opsForValue().set("aaa", "111");
		Assert.assertEquals("111", stringRedisTemplate.opsForValue().get("aaa"));

    }

}
```

通过上面这段极为简单的测试案例演示了如何通过自动配置的`StringRedisTemplate`对象进行Redis的读写操作，该对象从命名中就可注意到支持的是String类型。如果有使用过spring-data-redis的开发者一定熟悉`RedisTemplate<K, V>`接口，`StringRedisTemplate`就相当于`RedisTemplate<String, String>`的实现。

除了String类型，实战中我们还经常会在Redis中存储对象，这时候我们就会想是否可以使用类似`RedisTemplate<String, User>`来初始化并进行操作。但是Spring Boot并不支持直接使用，需要我们自己实现`RedisSerializer<T>`接口来对传入对象进行序列化和反序列化，下面我们通过一个实例来完成对象的读写操作。

- 创建要存储的对象：User

```java
public class User implements Serializable {

    private static final long serialVersionUID = -1L;

    private String username;
    private Integer age;

    public User(String username, Integer age) {
        this.username = username;
        this.age = age;
    }

    // 省略getter和setter

}
```

- 实现对象的序列化接口

```java
public class RedisObjectSerializer implements RedisSerializer<Object> {

  private Converter<Object, byte[]> serializer = new SerializingConverter();
  private Converter<byte[], Object> deserializer = new DeserializingConverter();

  static final byte[] EMPTY_ARRAY = new byte[0];

  public Object deserialize(byte[] bytes) {
    if (isEmpty(bytes)) {
      return null;
    }

    try {
      return deserializer.convert(bytes);
    } catch (Exception ex) {
      throw new SerializationException("Cannot deserialize", ex);
    }
  }

  public byte[] serialize(Object object) {
    if (object == null) {
      return EMPTY_ARRAY;
    }

    try {
      return serializer.convert(object);
    } catch (Exception ex) {
      return EMPTY_ARRAY;
    }
  }

  private boolean isEmpty(byte[] data) {
    return (data == null || data.length == 0);
  }
}
```

- 配置针对User的RedisTemplate实例

```java
@Configuration
public class RedisConfig {

    @Bean
    JedisConnectionFactory jedisConnectionFactory() {
        return new JedisConnectionFactory();
    }

    @Bean
    public RedisTemplate<String, User> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, User> template = new RedisTemplate<String, User>();
        template.setConnectionFactory(jedisConnectionFactory());
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new RedisObjectSerializer());
        return template;
    }


}
```

- 完成了配置工作后，编写测试用例实验效果

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private RedisTemplate<String, User> redisTemplate;

	@Test
	public void test() throws Exception {

		// 保存对象
		User user = new User("超人", 20);
		redisTemplate.opsForValue().set(user.getUsername(), user);

		user = new User("蝙蝠侠", 30);
		redisTemplate.opsForValue().set(user.getUsername(), user);

		user = new User("蜘蛛侠", 40);
		redisTemplate.opsForValue().set(user.getUsername(), user);

		Assert.assertEquals(20, redisTemplate.opsForValue().get("超人").getAge().longValue());
		Assert.assertEquals(30, redisTemplate.opsForValue().get("蝙蝠侠").getAge().longValue());
		Assert.assertEquals(40, redisTemplate.opsForValue().get("蜘蛛侠").getAge().longValue());

	}

}
```

当然spring-data-redis中提供的数据操作远不止这些，本文仅作为在Spring Boot中使用redis时的配置参考，更多对于redis的操作使用，请参考[Spring-data-redis Reference](http://docs.spring.io/spring-data/redis/docs/1.6.2.RELEASE/reference/html/)。

- [本文完整示例chapter3-2-5](http://git.oschina.net/didispace/SpringBoot-Learning)

## MongoDB简介

MongoDB是一个基于分布式文件存储的数据库，它是一个介于关系数据库和非关系数据库之间的产品，其主要目标是在键/值存储方式（提供了高性能和高度伸缩性）和传统的RDBMS系统（具有丰富的功能）之间架起一座桥梁，它集两者的优势于一身。

MongoDB支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型，也因为他的存储格式也使得它所存储的数据在Nodejs程序应用中使用非常流畅。

既然称为NoSQL数据库，Mongo的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

但是，MongoDB也不是万能的，同MySQL等关系型数据库相比，它们在针对不同的数据类型和事务要求上都存在自己独特的优势。在数据存储的选择中，坚持多样化原则，选择更好更经济的方式，而不是自上而下的统一化。

较常见的，我们可以直接用MongoDB来存储键值对类型的数据，如：验证码、Session等；由于MongoDB的横向扩展能力，也可以用来存储数据规模会在未来变的非常巨大的数据，如：日志、评论等；由于MongoDB存储数据的弱类型，也可以用来存储一些多变json数据，如：与外系统交互时经常变化的JSON报文。而==对于一些对数据有复杂的高事务性要求的操作，如：账户交易等就不适合使用MongoDB来存储==。

[MongoDB官网](https://www.mongodb.org/)

## 访问MongoDB

在Spring Boot中，对如此受欢迎的MongoDB，同样提供了自配置功能。

#### 引入依赖

Spring Boot中可以通过在`pom.xml`中加入`spring-boot-starter-data-mongodb`引入对mongodb的访问支持依赖。它的实现依赖`spring-data-mongodb`。是的，您没有看错，又是`spring-data`的子项目，之前介绍过`spring-data-jpa`、`spring-data-redis`，对于mongodb的访问，`spring-data`也提供了强大的支持，下面就开始动手试试吧。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

#### 快速开始使用Spring-data-mongodb

若MongoDB的安装配置采用默认端口，那么在自动配置的情况下，我们不需要做任何参数配置，就能马上连接上本地的MongoDB。下面直接使用`spring-data-mongodb`来尝试对mongodb的存取操作。（记得mongod启动您的mongodb）

- 创建要存储的User实体，包含属性：id、username、age

```java
public class User {

    @Id
    private Long id;

    private String username;
    private Integer age;

    public User(Long id, String username, Integer age) {
        this.id = id;
        this.username = username;
        this.age = age;
    }

    // 省略getter和setter

}
```

- 实现User的数据访问对象：UserRepository

```java
public interface UserRepository extends MongoRepository<User, Long> {

    User findByUsername(String username);

}
```

- 在单元测试中调用

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private UserRepository userRepository;

	@Before
	public void setUp() {
		userRepository.deleteAll();
	}

	@Test
	public void test() throws Exception {

		// 创建三个User，并验证User总数
		userRepository.save(new User(1L, "didi", 30));
		userRepository.save(new User(2L, "mama", 40));
		userRepository.save(new User(3L, "kaka", 50));
		Assert.assertEquals(3, userRepository.findAll().size());

		// 删除一个User，再验证User总数
		User u = userRepository.findOne(1L);
		userRepository.delete(u);
		Assert.assertEquals(2, userRepository.findAll().size());

		// 删除一个User，再验证User总数
		u = userRepository.findByUsername("mama");
		userRepository.delete(u);
		Assert.assertEquals(1, userRepository.findAll().size());

	}

}
```

#### 参数配置

通过上面的例子，我们可以轻而易举的对MongoDB进行访问，但是实战中，应用服务器与MongoDB通常不会部署于同一台设备之上，这样就无法使用自动化的本地配置来进行使用。这个时候，我们也可以方便的配置来完成支持，只需要在application.properties中加入mongodb服务端的相关配置，具体示例如下：

```
spring.data.mongodb.uri=mongodb://name:pass@localhost:27017/test
```

*在尝试此配置时，记得在mongo中对test库创建具备读写权限的用户（用户名为name，密码为pass），不同版本的用户创建语句不同，注意查看文档做好准备工作*

若使用mongodb 2.x，也可以通过如下参数配置，该方式不支持mongodb 3.x。

```
spring.data.mongodb.host=localhost spring.data.mongodb.port=27017
```



# 12 Spring Boot整合MyBatis

最近项目原因可能会继续开始使用MyBatis，已经习惯于spring-data的风格，再回头看xml的映射配置总觉得不是特别舒服，接口定义与映射离散在不同文件中，使得阅读起来并不是特别方便。

Spring中整合MyBatis就不多说了，最近大量使用Spring Boot，因此整理一下Spring Boot中整合MyBatis的步骤。搜了一下Spring Boot整合MyBatis的文章，方法都比较老，比较繁琐。查了一下文档，实际已经支持较为简单的整合与使用。下面就来详细介绍如何在Spring Boot中整合MyBatis，并通过注解方式实现映射。

## 整合MyBatis

- 新建Spring Boot项目，或以[Chapter1](https://git.oschina.net/didispace/SpringBoot-Learning)为基础来操作
- `pom.xml`中引入依赖
  - 这里用到spring-boot-starter基础和spring-boot-starter-test用来做单元测试验证数据访问
  - 引入连接mysql的必要依赖mysql-connector-java
  - 引入整合MyBatis的核心依赖mybatis-spring-boot-starter
  - 这里不引入spring-boot-starter-jdbc依赖，是由于mybatis-spring-boot-starter中已经包含了此依赖
  - ```xml
    <parent>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-parent</artifactId>
    	<version>1.3.2.RELEASE</version>
    	<relativePath/> <!-- lookup parent from repository -->
    </parent>
    <dependencies>
    	<dependency>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter</artifactId>
    	</dependency>
    	<dependency>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-test</artifactId>
    		<scope>test</scope>
    	</dependency>
    	<dependency>
    		<groupId>org.mybatis.spring.boot</groupId>
    		<artifactId>mybatis-spring-boot-starter</artifactId>
    		<version>1.1.1</version>
    	</dependency>
    	<dependency>
    		<groupId>mysql</groupId>
    		<artifactId>mysql-connector-java</artifactId>
    		<version>5.1.21</version>
    	</dependency>
    </dependencies>
    ```
- 同之前介绍的使用jdbc和spring-data连接数据库一样，在`application.properties`中配置mysql的连接配置

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

同其他Spring Boot工程一样，简单且简洁的的完成了基本配置，下面看看如何在这个基础下轻松方便的使用MyBatis访问数据库。

## 使用MyBatis

- 在Mysql中创建User表，包含id(BIGINT)、name(INT)、age(VARCHAR)字段。同时，创建映射对象User

```java
public class User {

    private Long id;
    private String name;
    private Integer age;

    // 省略getter和setter

}
```

- 创建User映射的操作UserMapper，为了后续单元测试验证，实现插入和查询操作

```java
@Mapper
public interface UserMapper {

    @Select("SELECT * FROM USER WHERE NAME = #{name}")
    User findByName(@Param("name") String name);

    @Insert("INSERT INTO USER(NAME, AGE) VALUES(#{name}, #{age})")
    int insert(@Param("name") String name, @Param("age") Integer age);

}
```

- 创建Spring Boot主类

```java
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

- 创建单元测试
  - 测试逻辑：插入一条name=AAA，age=20的记录，然后根据name=AAA查询，并判断age是否为20
  - 测试结束回滚数据，保证测试单元每次运行的数据环境独立

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = Application.class)
public class ApplicationTests {

	@Autowired
	private UserMapper userMapper;

	@Test
	@Rollback
	public void findByName() throws Exception {
		userMapper.insert("AAA", 20);
		User u = userMapper.findByName("AAA");
		Assert.assertEquals(20, u.getAge().intValue());
	}

}
```

完整示例[Chapter3-2-7](https://git.oschina.net/didispace/SpringBoot-Learning)

**【转载请注明出处】：http://blog.didispace.com/springbootmybatis/**

如果您有任何想法或问题需要讨论或交流，可进入[交流区](http://qa.didispace.com/)发表您的想法或问题。

# 13 Spring Boot中使用MyBatis注解配置详解

之前在Spring Boot中整合MyBatis时，采用了注解的配置方式，相信很多人还是比较喜欢这种优雅的方式的，也收到不少读者朋友的反馈和问题，主要集中于针对各种场景下注解如何使用，下面就对几种常见的情况举例说明用法。

在做下面的示例之前，先准备一个整合好MyBatis的工程，可参见[Spring Boot整合MyBatis](http://blog.didispace.com/springbootmybatis/)，也可直接使用整合好的样例：[Chapter3-2-7](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter3-2-7)。

## 传参方式

下面通过几种不同传参方式来实现前文中实现的插入操作。

#### 使用@Param

在之前的整合示例中我们已经使用了这种最简单的传参方式，如下：

```java
@Insert("INSERT INTO USER(NAME, AGE) VALUES(#{name}, #{age})")
int insert(@Param("name") String name, @Param("age") Integer age);
```

这种方式很好理解，`@Param`中定义的`name`对应了SQL中的`#{name}`，`age`对应了SQL中的`#{age}`。

#### 使用Map

如下代码，通过Map对象来作为传递参数的容器：

```java
@Insert("INSERT INTO USER(NAME, AGE) VALUES(#{name,jdbcType=VARCHAR}, #{age,jdbcType=INTEGER})")
int insertByMap(Map<String, Object> map);
```

对于Insert语句中需要的参数，我们只需要在map中填入同名的内容即可，具体如下面代码所示：

```java
Map<String, Object> map = new HashMap<>();
map.put("name", "CCC");
map.put("age", 40);
userMapper.insertByMap(map);
```

#### 使用对象

除了Map对象，我们也可直接使用普通的Java对象来作为查询条件的传参，比如我们可以直接使用User对象:

```java
@Insert("INSERT INTO USER(NAME, AGE) VALUES(#{name}, #{age})")
int insertByUser(User user);
```

这样语句中的`#{name}`、`#{age}`就分别对应了User对象中的`name`和`age`属性。

## 增删改查

MyBatis针对不同的数据库操作分别提供了不同的注解来进行配置，在之前的示例中演示了`@Insert`，下面针对User表做一组最基本的增删改查作为示例：

```java
public interface UserMapper {

    @Select("SELECT * FROM user WHERE name = #{name}")
    User findByName(@Param("name") String name);

    @Insert("INSERT INTO user(name, age) VALUES(#{name}, #{age})")
    int insert(@Param("name") String name, @Param("age") Integer age);

    @Update("UPDATE user SET age=#{age} WHERE name=#{name}")
    void update(User user);

    @Delete("DELETE FROM user WHERE id =#{id}")
    void delete(Long id);
}
```

在完成了一套增删改查后，不妨我们试试下面的单元测试来验证上面操作的正确性：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = Application.class)
@Transactional
public class ApplicationTests {

	@Autowired
	private UserMapper userMapper;

	@Test
	@Rollback
	public void testUserMapper() throws Exception {
		// insert一条数据，并select出来验证
		userMapper.insert("AAA", 20);
		User u = userMapper.findByName("AAA");
		Assert.assertEquals(20, u.getAge().intValue());
		// update一条数据，并select出来验证
		u.setAge(30);
		userMapper.update(u);
		u = userMapper.findByName("AAA");
		Assert.assertEquals(30, u.getAge().intValue());
		// 删除这条数据，并select验证
		userMapper.delete(u.getId());
		u = userMapper.findByName("AAA");
		Assert.assertEquals(null, u);
	}
}
```

## 返回结果的绑定

对于增、删、改操作相对变化较小。而对于“查”操作，我们往往需要进行多表关联，汇总计算等操作，那么对于查询的结果往往就不再是简单的实体对象了，往往需要返回一个与数据库实体不同的包装类，那么对于这类情况，就可以通过`@Results`和`@Result`注解来进行绑定，具体如下：

```java
@Results({
    @Result(property = "name", column = "name"),
    @Result(property = "age", column = "age")
})
@Select("SELECT name, age FROM user")
List<User> findAll();
```

在上面代码中，@Result中的property属性对应User对象中的成员名，column对应SELECT出的字段名。在该配置中故意没有查出id属性，只对User对应中的name和age对象做了映射配置，这样可以通过下面的单元测试来验证查出的id为null，而其他属性不为null：

```
@Test
@Rollback
public void testUserMapper() throws Exception {
	List<User> userList = userMapper.findAll();
	for(User user : userList) {
		Assert.assertEquals(null, user.getId());
		Assert.assertNotEquals(null, user.getName());
	}
}
```

## 后记

本文主要介绍几种最为常用的方式，更多其他注解的使用可参见文档：<http://www.mybatis.org/mybatis-3/zh/java-api.html>

本文示例完整代码：[Chapter3-2-8](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter3-2-8)



# 14 Spring Boot中的事务管理

## 什么是事务？

我们在开发企业应用时，对于业务人员的一个操作实际是对数据读写的多步操作的结合。由于数据操作在顺序执行的过程中，任何一步操作都有可能发生异常，异常会导致后续操作无法完成，此时由于业务逻辑并未正确的完成，之前成功操作数据的并不可靠，需要在这种情况下进行回退。

事务的作用就是为了保证用户的每一个操作都是可靠的，事务中的每一步操作都必须成功执行，只要有发生异常就回退到事务开始未进行操作的状态。

事务管理是Spring框架中最为常用的功能之一，我们在使用Spring Boot开发应用时，大部分情况下也都需要使用事务。

## 快速入门

在Spring Boot中，当我们使用了spring-boot-starter-jdbc或spring-boot-starter-data-jpa依赖的时候，框架会自动默认分别注入DataSourceTransactionManager或JpaTransactionManager。所以我们不需要任何额外配置就可以用@Transactional注解进行事务的使用。

我们以之前实现的《用spring-data-jpa访问数据库》的示例[Chapter3-2-2](http://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter3-2-2)作为基础工程进行事务的使用常识。

在该样例工程中（若对该数据访问方式不了解，可先阅读该[文章](http://blog.didispace.com/springbootdata2/)），我们引入了spring-data-jpa，并创建了User实体以及对User的数据访问对象UserRepository，在ApplicationTest类中实现了使用UserRepository进行数据读写的单元测试用例，如下：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

	@Autowired
	private UserRepository userRepository;

	@Test
	public void test() throws Exception {

		// 创建10条记录
		userRepository.save(new User("AAA", 10));
		userRepository.save(new User("BBB", 20));
		userRepository.save(new User("CCC", 30));
		userRepository.save(new User("DDD", 40));
		userRepository.save(new User("EEE", 50));
		userRepository.save(new User("FFF", 60));
		userRepository.save(new User("GGG", 70));
		userRepository.save(new User("HHH", 80));
		userRepository.save(new User("III", 90));
		userRepository.save(new User("JJJ", 100));

		// 省略后续的一些验证操作
	}


}
```

可以看到，在这个单元测试用例中，使用UserRepository对象连续创建了10个User实体到数据库中，下面我们人为的来制造一些异常，看看会发生什么情况。

通过定义User的name属性长度为5，这样通过创建时User实体的name属性超长就可以触发异常产生。

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false, length = 5)
    private String name;

    @Column(nullable = false)
    private Integer age;

    // 省略构造函数、getter和setter

}
```

修改测试用例中创建记录的语句，将一条记录的name长度超过5，如下：name为HHHHHHHHH的User对象将会抛出异常。

```java
// 创建10条记录
userRepository.save(new User("AAA", 10));
userRepository.save(new User("BBB", 20));
userRepository.save(new User("CCC", 30));
userRepository.save(new User("DDD", 40));
userRepository.save(new User("EEE", 50));
userRepository.save(new User("FFF", 60));
userRepository.save(new User("GGG", 70));
userRepository.save(new User("HHHHHHHHHH", 80));
userRepository.save(new User("III", 90));
userRepository.save(new User("JJJ", 100));
```

执行测试用例，可以看到控制台中抛出了如下异常，name字段超长：

```powershell
2016-05-27 10:30:35.948  WARN 2660 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 1406, SQLState: 22001
2016-05-27 10:30:35.948 ERROR 2660 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Data truncation: Data too long for column 'name' at row 1
2016-05-27 10:30:35.951  WARN 2660 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Warning Code: 1406, SQLState: HY000
2016-05-27 10:30:35.951  WARN 2660 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Data too long for column 'name' at row 1

org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; nested exception is org.hibernate.exception.DataException: could not execute statement
```

此时查数据库中，创建了name从AAA到GGG的记录，没有HHHHHHHHHH、III、JJJ的记录。而若这是一个希望保证完整性操作的情况下，AAA到GGG的记录希望能在发生异常的时候被回退，这时候就可以使用事务让它实现回退，做法非常简单，我们只需要在test函数上添加`@Transactional`注解即可。

```java
@Test
@Transactional
public void test() throws Exception {

    // 省略测试内容

}
```

再来执行该测试用例，可以看到控制台中输出了回滚日志（Rolled back transaction for test context），

```shell
2016-05-27 10:35:32.210  WARN 5672 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 1406, SQLState: 22001
2016-05-27 10:35:32.210 ERROR 5672 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Data truncation: Data too long for column 'name' at row 1
2016-05-27 10:35:32.213  WARN 5672 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Warning Code: 1406, SQLState: HY000
2016-05-27 10:35:32.213  WARN 5672 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Data too long for column 'name' at row 1
2016-05-27 10:35:32.221  INFO 5672 --- [           main] o.s.t.c.transaction.TransactionContext   : Rolled back transaction for test context [DefaultTestContext@1d7a715 testClass = ApplicationTests, testInstance = com.didispace.ApplicationTests@95a785, testMethod = test@ApplicationTests, testException = org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; nested exception is org.hibernate.exception.DataException: could not execute statement, mergedContextConfiguration = [MergedContextConfiguration@11f39f9 testClass = ApplicationTests, locations = '{}', classes = '{class com.didispace.Application}', contextInitializerClasses = '[]', activeProfiles = '{}', propertySourceLocations = '{}', propertySourceProperties = '{}', contextLoader = 'org.springframework.boot.test.SpringApplicationContextLoader', parent = [null]]].

org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; nested exception is org.hibernate.exception.DataException: could not execute statement
```

再看数据库中，User表就没有AAA到GGG的用户数据了，成功实现了自动回滚。

这里主要通过单元测试演示了如何使用`@Transactional`注解来声明一个函数需要被事务管理，通常我们单元测试为了保证每个测试之间的数据独立，会使用`@Rollback`注解让每个单元测试都能在结束时回滚。而真正在开发业务逻辑时，我们==**通常在service层接口中使用`@Transactional`来对各个业务逻辑进行事务管理的配置**==，例如：

```java
public interface UserService {
    
    @Transactional
    User login(String name, String password);
    
}
```

## 事务详解

上面的例子中我们使用了默认的事务配置，可以满足一些基本的事务需求，但是当我们项目较大较复杂时（比如，有多个数据源等），这时候需要在声明事务时，指定不同的事务管理器。对于不同数据源的事务管理配置可以见[《Spring Boot多数据源配置与使用》](http://blog.didispace.com/springbootmultidatasource/)中的设置。在声明事务时，只需要通过value属性指定配置的事务管理器名即可，例如：`@Transactional(value="transactionManagerPrimary")`。

除了指定不同的事务管理器之后，还能对事务进行隔离级别和传播行为的控制，下面分别详细解释：

#### 隔离级别

隔离级别是指若干个并发的事务之间的隔离程度，与我们开发时候主要相关的场景包括：脏读取、重复读、幻读。

我们可以看`org.springframework.transaction.annotation.Isolation`枚举类中定义了五个表示隔离级别的值：

```java
public enum Isolation {
    DEFAULT(-1),
    READ_UNCOMMITTED(1),
    READ_COMMITTED(2),
    REPEATABLE_READ(4),
    SERIALIZABLE(8);
}
```

- `DEFAULT`：这是默认值，表示使用底层数据库的默认隔离级别。对大部分数据库而言，通常这值就是：`READ_COMMITTED`。
- `READ_UNCOMMITTED`：该隔离级别表示一个事务可以读取另一个事务修改但还没有提交的数据。该级别不能防止脏读和不可重复读，因此很少使用该隔离级别。
- `READ_COMMITTED`：该隔离级别表示一个事务只能读取另一个事务已经提交的数据。该级别可以防止脏读，这也是大多数情况下的推荐值。
- `REPEATABLE_READ`：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。即使在多次查询之间有新增的数据满足该查询，这些新增的记录也会被忽略。该级别可以防止脏读和不可重复读。
- `SERIALIZABLE`：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

指定方法：通过使用`isolation`属性设置，例如：

```java
@Transactional(isolation = Isolation.DEFAULT)
```

#### 传播行为

所谓事务的传播行为是指，**如果在开始当前事务之前，一个事务上下文已经存在，此时有若干选项可以指定一个事务性方法的执行行为**。

我们可以看`org.springframework.transaction.annotation.Propagation`枚举类中定义了6个表示传播行为的枚举值：

```java
public enum Propagation {
    REQUIRED(0),
    SUPPORTS(1),
    MANDATORY(2),
    REQUIRES_NEW(3),
    NOT_SUPPORTED(4),
    NEVER(5),
    NESTED(6);
}
```

- `REQUIRED`：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- `SUPPORTS`：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- `MANDATORY`：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- `REQUIRES_NEW`：创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- `NOT_SUPPORTED`：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- `NEVER`：以非事务方式运行，如果当前存在事务，则抛出异常。
- `NESTED`：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`REQUIRED`。

指定方法：通过使用`propagation`属性设置，例如：

```java
@Transactional(propagation = Propagation.REQUIRED)
```

完整示例[Chapter3-3-1](https://git.oschina.net/didispace/SpringBoot-Learning)

# 15 Spring Boot中使用@Scheduled创建定时任务

我们在编写Spring Boot应用中经常会遇到这样的场景，比如：我需要定时地发送一些短信、邮件之类的操作，也可能会定时地检查和监控一些标志、参数等。

## 创建定时任务

在Spring Boot中编写定时任务是非常简单的事，下面通过实例介绍如何在Spring Boot中创建定时任务，实现每过5秒输出一下当前时间。

- 在Spring Boot的主类中加入`@EnableScheduling`注解，启用定时任务的配置

```java
@SpringBootApplication
@EnableScheduling
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

- 创建定时任务实现类

```java
@Component
public class ScheduledTasks {

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        System.out.println("现在时间：" + dateFormat.format(new Date()));
    }

}
```

- 运行程序，控制台中可以看到类似如下输出，定时任务开始正常运作了。

```shell
2016-05-15 10:40:04.073  INFO 1688 --- [           main] com.didispace.Application                : Started Application in 1.433 seconds (JVM running for 1.967)
现在时间：10:40:09
现在时间：10:40:14
现在时间：10:40:19
现在时间：10:40:24
现在时间：10:40:29522
现在时间：10:40:34
```

关于上述的简单入门示例也可以参见官方的[Scheduling Tasks](http://spring.io/guides/gs/scheduling-tasks/)

## @Scheduled详解

在上面的入门例子中，使用了`@Scheduled(fixedRate = 5000)` 注解来定义每过5秒执行的任务，对于`@Scheduled`的使用可以总结如下几种方式：

- `@Scheduled(fixedRate = 5000)` ：上一次开始执行时间点之后5秒再执行
- `@Scheduled(fixedDelay = 5000)` ：上一次执行完毕时间点之后5秒再执行
- `@Scheduled(initialDelay=1000, fixedRate=5000)` ：第一次延迟1秒后执行，之后按fixedRate的规则每5秒执行一次
- `@Scheduled(cron="*/5 * * * * *")` ：通过cron表达式定义规则

[完整示例Chapter4-1-1](http://git.oschina.net/didispace/SpringBoot-Learning)

# Spring Boot中使用@Async实现异步调用

原创

** [2016-05-16](http://blog.didispace.com/springbootasync/)

** 翟永超

** [Spring Boot](http://blog.didispace.com/categories/Spring-Boot/)

被围观 15097 次

什么是“异步调用”？

“异步调用”对应的是“同步调用”，*同步调用*指程序按照定义顺序依次执行，每一行程序都必须等待上一行程序执行完成之后才能执行；*异步调用*指程序在顺序执行时，不等待异步调用的语句返回结果就执行后面的程序。

#### 同步调用

下面通过一个简单示例来直观的理解什么是同步调用：

- 定义Task类，创建三个处理函数分别模拟三个执行任务的操作，操作消耗时间随机取（10秒内）

```
@Component
public class Task {

    public static Random random =new Random();

    public void doTaskOne() throws Exception {
        System.out.println("开始做任务一");
        long start = System.currentTimeMillis();
        Thread.sleep(random.nextInt(10000));
        long end = System.currentTimeMillis();
        System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
    }

    public void doTaskTwo() throws Exception {
        System.out.println("开始做任务二");
        long start = System.currentTimeMillis();
        Thread.sleep(random.nextInt(10000));
        long end = System.currentTimeMillis();
        System.out.println("完成任务二，耗时：" + (end - start) + "毫秒");
    }

    public void doTaskThree() throws Exception {
        System.out.println("开始做任务三");
        long start = System.currentTimeMillis();
        Thread.sleep(random.nextInt(10000));
        long end = System.currentTimeMillis();
        System.out.println("完成任务三，耗时：" + (end - start) + "毫秒");
    }

}
```

- 在单元测试用例中，注入Task对象，并在测试用例中执行`doTaskOne`、`doTaskTwo`、`doTaskThree`三个函数。

```
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = Application.class)
public class ApplicationTests {

	@Autowired
	private Task task;

	@Test
	public void test() throws Exception {
		task.doTaskOne();
		task.doTaskTwo();
		task.doTaskThree();
	}

}
```

- 执行单元测试，可以看到类似如下输出：

```
开始做任务一
完成任务一，耗时：4256毫秒
开始做任务二
完成任务二，耗时：4957毫秒
开始做任务三
完成任务三，耗时：7173毫秒
```

任务一、任务二、任务三顺序的执行完了，换言之`doTaskOne`、`doTaskTwo`、`doTaskThree`三个函数顺序的执行完成。

#### 异步调用

上述的同步调用虽然顺利的执行完了三个任务，但是可以看到执行时间比较长，若这三个任务本身之间不存在依赖关系，可以并发执行的话，同步调用在执行效率方面就比较差，可以考虑通过异步调用的方式来并发执行。

在Spring Boot中，我们只需要通过使用`@Async`注解就能简单的将原来的同步函数变为异步函数，Task类改在为如下模式：

```
@Component
public class Task {

    @Async
    public void doTaskOne() throws Exception {
        // 同上内容，省略
    }

    @Async
    public void doTaskTwo() throws Exception {
        // 同上内容，省略
    }

    @Async
    public void doTaskThree() throws Exception {
        // 同上内容，省略
    }

}
```

为了让@Async注解能够生效，还需要在Spring Boot的主程序中配置@EnableAsync，如下所示：

```
@SpringBootApplication
@EnableAsync
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

此时可以反复执行单元测试，您可能会遇到各种不同的结果，比如：

- 没有任何任务相关的输出
- 有部分任务相关的输出
- 乱序的任务相关的输出

原因是目前`doTaskOne`、`doTaskTwo`、`doTaskThree`三个函数的时候已经是异步执行了。主程序在异步调用之后，主程序并不会理会这三个函数是否执行完成了，由于没有其他需要执行的内容，所以程序就自动结束了，导致了不完整或是没有输出任务相关内容的情况。

**注： @Async所修饰的函数不要定义为static类型，这样异步调用不会生效**

#### 异步回调

为了让`doTaskOne`、`doTaskTwo`、`doTaskThree`能正常结束，假设我们需要统计一下三个任务并发执行共耗时多少，这就需要等到上述三个函数都完成调动之后记录时间，并计算结果。

那么我们如何判断上述三个异步调用是否已经执行完成呢？我们需要使用`Future<T>`来返回异步调用的结果，就像如下方式改造`doTaskOne`函数：

```
@Async
public Future<String> doTaskOne() throws Exception {
    System.out.println("开始做任务一");
    long start = System.currentTimeMillis();
    Thread.sleep(random.nextInt(10000));
    long end = System.currentTimeMillis();
    System.out.println("完成任务一，耗时：" + (end - start) + "毫秒");
    return new AsyncResult<>("任务一完成");
}
```

按照如上方式改造一下其他两个异步函数之后，下面我们改造一下测试用例，让测试在等待完成三个异步调用之后来做一些其他事情。

```
@Test
public void test() throws Exception {

	long start = System.currentTimeMillis();

	Future<String> task1 = task.doTaskOne();
	Future<String> task2 = task.doTaskTwo();
	Future<String> task3 = task.doTaskThree();

	while(true) {
		if(task1.isDone() && task2.isDone() && task3.isDone()) {
			// 三个任务都调用完成，退出循环等待
			break;
		}
		Thread.sleep(1000);
	}

	long end = System.currentTimeMillis();

	System.out.println("任务全部完成，总耗时：" + (end - start) + "毫秒");

}
```

看看我们做了哪些改变：

- 在测试用例一开始记录开始时间
- 在调用三个异步函数的时候，返回`Future<String>`类型的结果对象
- 在调用完三个异步函数之后，开启一个循环，根据返回的`Future<String>`对象来判断三个异步函数是否都结束了。若都结束，就结束循环；若没有都结束，就等1秒后再判断。
- 跳出循环之后，根据结束时间 - 开始时间，计算出三个任务并发执行的总耗时。

执行一下上述的单元测试，可以看到如下结果：

```
开始做任务一
开始做任务二
开始做任务三
完成任务三，耗时：37毫秒
完成任务二，耗时：3661毫秒
完成任务一，耗时：7149毫秒
任务全部完成，总耗时：8025毫秒
```

可以看到，通过异步调用，让任务一、二、三并发执行，有效的减少了程序的总运行时间。

[完整示例Chapter4-1-2](http://git.oschina.net/didispace/SpringBoot-Learning)