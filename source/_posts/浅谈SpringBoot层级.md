---
title: 浅谈SpringBoot层级问题
tags: 
      - Java
      - 后端
      - SpringBoot
---

浅谈SpringBoot层级问题
=================================

一、前言
-------------------------

- 一个后端项目所需要做的事情基本上聚集在这几点上：<!--more-->
    - 1 ) 与数据库进行连接，根据业务需求对相应的数据库数据表进行增删改查 
    - 2 ) 给前端提供返回数据与接收数据的接口 
    - 3 ) 从接口接收数据后，根据业务需求进行处理数据之后返回给接口并进行crud
<br>
- 这些是一个后端最基本的功能，而其他的例如什么高并发处理，redis缓冲处理，以及其他处理性能与稳定性的提升都不在本文谈论的范围内，毕竟这些是Pro的后端进行考虑的事情，我们前端在一开始了解最基础的那几点就足够了，这样就可以知道后端说xxx功能实现不来是不是想偷懒然后诓你的了23333

<center>![此处输入图片的描述][1]</center>

- 了解了这些我们就可以去好好看看Spring Boot里的层级了。它的层级分为**Mapper（DAO/持久）层**、**Model（Entity/实体）层**、**Controller（控制）层**和**Service（业务）层**。

- 以下内容默认读者已经会在idea/eclipse中建立SpringBoot项目。

二、Spring Boot
----------

### 1、Controller层
- Spring Boot中的必须的层级其实只有**Controller（控制）层**，该层可以实现返回接口和业务逻辑处理的功能，如果没有逻辑可以返回那就直接返回接口，此时写一个helloworld的class就可以直接看到效果：

```Java
package springboot.controller;

import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@CrossOrigin //跨域
@RestController
public class HelloWorldController {
    @RequestMapping("/")
    public String sayHello() {
        return "Hello,World!";
    }
}
```
<center>![此处输入图片的描述][2]</center>

- 其中`@RestController`和`@RequestMapping`两个注解分别起到的是注入Controller层并返回JSON格式数据和赋予路由路径的功能，虽然肯定不止这么简单，但是如果入门的话就先这么理解就好。
    - 注：SpringBoot需要在注解里加上各层的注解，不然框架无法注入。

- 然后这个时候我们又想给自己加难度，搞一搞业务逻辑处理，这咋搞呢，这个时候就要请出我们的Service层啦。

### 2、Service层

- **Service（业务）层**就是我们进行业务逻辑处理的地方了，和其他语言不一样的地方就在于，SpringBoot写的Service类是接口类，我们得继承之后才能发挥它的真正用处。

- 所以我们还要写一个ServiceImplements类来继承接口并实现具体逻辑例子如下所示：
Service.java:
```
@Service// service层注解
public interface HelloWorldService {
    String outputHelloWorld();
    /* other operations */
    /*  ... */
}
```
<br>
ServiceImpl.java
```
@Repository// impl注解
public HelloWorlServiceImpl implements HelloWorldService {
    public String outputHelloWorld(){
        return "Hello World";
    }
    /* other operations */
    /*  ... */
}

```

- 效果与之前一样。

- 之后在HelloWorldController类中调用HelloWorlServiceImpl里的方法处理逻辑即可。

- 这个时候我们想玩点花操作：能不能从前端接受一个"World"字符串，然后我们返回一个`{"hello": "world"}`JSON格式的数据，于此同时我们把这个World存在一个表名为test，字段只有一个`hello varchar(100)`的表内。这个时候我们就要用到我们前面没有用到的Model层和Mapper层了。

### 3、Model层和Mapper层

- Model层还好说，Mapper层就需要我们用到SpringBoot Mybatis的依赖和设置数据库的连接，这个网上很多这里就不再多赘诉。贴一个连接：https://www.cnblogs.com/ityouknow/p/6037431.html 这篇就讲的很好，就不多说了，直接进入正题：

- 我们先在Model层定义我们想要接受并修改的数据（基本上为表内字段），这里用到了lombok这个maven插件，可以节省写bean的时间，记得在idea的plugins里也安装读取插件，不然会报错：
```
public class HelloWorld {
    @Getter
    @Setter
    private String world;
}
```

- 然后在Mapper层写对应接口：

```
public interface HelloWorldMapper {
    void insertString(String world);
}
```

- 是的，Mapper层写的是接口类，它是为mybatis的xml文件服务的，这个时候我们还要在配置（一般都在resource/mapper里）的文件夹里创建一个`HelloWorldMapper.xml`

```
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="your.package.HelloWorldMapper">
<!--column为表内字段名，property为Model内命名的表的字段名-->
<resultMap id="BaseResultMap" type="your.package.model.HelloWorld">
 <result column="hello" jdbcType="VARCHAR" property="world" />
</resultMap>
<!--字段名-->
<sql id="Base_Column_List">
 world
</sql>
<!--insert-->
<!--#后面跟接口的函数定义接收的参数，表示接收该参数放入sql语句-->
<insert id="insertString" parameterType="your.package.model.HelloWorld">
insert into
test(Hello)
values (#{world,jdbcType=VARCHAR})
</insert>
</mapper>
```

- 最后在`HelloWorlServiceImpl`中的`outputHelloWorld()`函数中调用`HelloWorldMapper`里的函数处理数据即可，然后将最开始的Controller、Service和ServiceImpl修改成如下：

```
...
public class HelloWorldController {
    @Autowired
    HelloWorldService helloWorldService;
    @RequestMapping(value="/", method = RequestMethod.GET)
    public String sayHello(String world) {
        return helloWorldService.outputHelloWorld(world);
    }
}
...
public interface HelloWorldService {
    String outputHelloWorld(String world);
    /* other operations */
    /*  ... */
}
...
@Repository
public HelloWorlServiceImpl implements HelloWorldService {
    @Resource
    private HelloWorldMapper helloWorldMapper;
    @Override
    public String outputHelloWorld(String world){
        helloWorldMapper.insertString(world);
        return "hello "+world;
    }
    /* other operations */
    /*  ... */
}
...
```
- 如果运行main函数报错这样修改Application类：

```
@SpringBootApplication
@MapperScan("your.package.mapper")//加入这行
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```

- 最终效果：

<center>![此处输入图片的描述][3]</center>

<center>![此处输入图片的描述][4]</center>

- 到这里我们的工作就算完成了，此时运行main函数，然后我们进入`http:\\localhost:port?hello=world`就可以看见返回world了，在数据库的test表里也能看到hello字段里有个world……emmmm不对！我们是要返回json格式的`{"hello": "world"}`，那么有什么办法呢？下面就要介绍**DTO（数据传输对象）层了**

- ~~什么，你说明明前面只列了四层这个第五层是什么鬼？四大天王里有五个不是基本常识吗~~其实是因为DTO层相比前面几层不是必须的，你也可以在前面某一层就返回类似格式的数据，这样就可以不使用DTO层了，不过还是推荐新人用它，因为比较容易理解好上手。

### 4、DTO层

- DTO的定义很容易就在百度百科里找到，这里就不多加解释了，我们要做的就是定义一个这个对象然后进行输出，因为SpringBoot的Controller可以将某个类型的字符串返回成JSON字符，所以我们可以使用这个特点创造一个自己的DTO：

```
public class ResultDTO<T> {
    @Setter
    @Getter
    private T hello;
    @Override
    public String toString() {
        return "ResultDTO{" +
                "hello=" + hello +
                '}';
    }
}
```

-这里我们用到了泛型，因为在实际项目中我们不知道最终数据结果是咋样的，所以使用泛型来对结果进行处理，然后再在Controller中作如下修改：

```
...
@RequestMapping(value="/", method = RequestMethod.GET)
public ResultDTO<HelloWorld> sayHello(String world) {
    ResultDTO resultDTO = new ResultDTO();
    resultDTO.setHello(world);
    helloWorldService.outputHelloWorld(world);
    return resultDTO;
}
...
```

- 这样就能出来我们想要的效果了～

![此处输入图片的描述][5]

- 注：如果接收的数据里面有json类型，不妨看这篇文章解决： https://blog.csdn.net/lixiuu/article/details/88970255


###5、目录层级

- 总结一下上面几个层级，在项目中大概是这样的：

<center>![此处输入图片的描述][6]</center>

- 然后我也理清了一下SpringBoot框架大概的模型，做了个图片：

<center>![此处输入图片的描述][7]</center>

三、结语
------------------

- 总的来说Spring Boot就和它所使用的语言Java一样，有着很严谨的结构又很有可扩展性，而且代码可读性和可维护性极强。也较为好上手，而且Maven里的插件很多很全，不用自己造轮子，这点还是很不错的，例如我在项目里用的PageHelper进行查询分页，这个就很好用。

- 学习一门新语言和它的框架还是挺好玩的，而且能够触类旁通，感觉自己对于TS的理解也加深了不少。但是真要我写Java还是bulebule，写的头疼，还是JS和TS适合我。

<center>![-][8] </center>

- 参考：
    - https://www.cnblogs.com/almm/p/10802419.html
    - https://www.jianshu.com/p/b534b394dc7a
    - https://juejin.im/entry/59bb7a8f5188256bd871dc15

<br>
上传于2019-11-03


  [1]: https://i.loli.net/2019/11/03/IARgMwFVirquQ6X.gif
  [2]: https://i.loli.net/2019/11/03/6BMm7CDuOw8EYAg.png
  [3]: https://i.loli.net/2019/11/03/Bp7uEk2NQvlh9xO.png
  [4]: https://i.loli.net/2019/11/03/IWqYLkfGatUPTjv.png
  [5]: https://i.loli.net/2019/11/03/NSFboEcVGLY9kwP.png
  [6]: https://i.loli.net/2019/11/03/s3jN2bqkYiP8LHM.png
  [7]: https://i.loli.net/2019/11/03/yoYE3C5MimsJwbA.png
  [8]: https://i.loli.net/2019/11/03/Zo34DQiuvbgM2Gy.png