# 服务注解

---

UBSI的服务注解包括：

- @UService - 声明微服务
- @USFilter - 声明过滤器
- @USDepend - 声明服务的依赖关系



### @UService

用来标注Java Class，将其声明为一个微服务，例如：

```java
package my.service.samples;

import rewin.ubsi.annotation.*;
import rewin.ubsi.container.ServiceContext;

@UService(
	name = "my.samples.demo",           // 微服务的名字，缺省为""
	tips = "测试服务",                   // 微服务的说明，缺省为""
	version = "1.0.0",                  // 接口的版本号，缺省为"0.0.1"
	release = false,                    // 版本发行状态：true 或 false，缺省为false
	depend = {                          // 依赖的其他微服务，缺省为{}
		@USDepend(                      // 第一个依赖，可以有多个
			name = "my.samples.xxx",    // 依赖的微服务的名字
			version = "",               // 该微服务的最小版本
			release = false             // 该微服务是否必须是"release"状态
		)
	},
	container = "1.0.1",				// 依赖的容器的版本号（1.0.1版本开始支持），缺省为""
	syslib = { "jaxen-1.2.0.jar" },		// 需要加载到SystemClassLoader中的Jar包，缺省为{}（容器版本>=1.0.1）
	singleton = false					// 本服务为单例服务，可以部署多实例，在部署了redis注册中心的环境中，容器会保证只有1个实例在运行，其他实例会处于"单例等待"状态(备用)，缺省为false（容器版本>=2.0.0）
)
public class DemoService {
	// 这是一个UBSI微服务，请注意：
	//   class的声明必须是public，并且有无参数的构造函数
}
```



### @USFilter

用来声明一个UBSI微服务的过滤器，除了没有"name/singleton"属性，其他都跟@UService相同，示例如下：

```java
/** 这是一个UBSI Filter */
@USFilter(
	tips = "这是一个filter"
)
public class DemoFilter {
	@USBefore
	public void before(ServiceContext ctx) throws Exception {
		ctx.getLogger().info("服务容器开始处理一个服务请求");
	}
	
	@USAfter
	public void after(ServiceContext ctx) throws Exception {
		ctx.getLogger().info("服务容器已经完成一个服务请求的处理");
	}
}
```

@USFilter与@UService都是由服务容器(Container)加载运行的Class，但与@UService不同，@USFilter不提供对外的访问接口，而是可以通过@USBefore/@USAfter定义的入口拦截本容器所有的服务请求，从而可以记录或改变处理行为。

### @USDepend

用在@UService/@USFilter注解的depend属性中，声明依赖的其他微服务，示例请见@UService注解。