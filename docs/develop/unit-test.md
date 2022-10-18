# 单元测试

---

微服务的代码已经开发完成，那如何进行接口的访问测试呢？我们先从JUnit单元测试开始介绍。

创建一个JUnit 4的测试类（IntelliJ中可以使用数标右键的"generate > JUnit Test > Junit 4"），代码如下：

```java
package ubsi.demo.hello;

import com.google.gson.Gson;
import org.junit.Test;
import org.junit.Before; 
import org.junit.After;
import rewin.ubsi.cli.Request;
import rewin.ubsi.common.Util;
import rewin.ubsi.consumer.Context;
import rewin.ubsi.container.Bootstrap;

import java.util.Map;

/** 
* ServiceEntry Tester. 
*/
public class ServiceEntryTest { 

    @Before
    public void before() throws Exception {
        Bootstrap.start();      // 启动测试容器，缺省为 localhost#7112
        /*
          启动容器时需要rewin.ubsi.module.json文件，否则容器不会自动加载微服务
          微服务加载时如果"startup"为true，则启动微服务（执行@USInit方法）
         */
    }

    @After
    public void after() throws Exception {
        Bootstrap.stop();       // 关闭测试容器，关闭时会依次停止加载的所有微服务（执行@USClose方法）
    }

    /**
    * 服务接口测试
    */
    @Test
    public void testHello() throws Exception {
        // 创建ubsi请求对象
        Context ubsi = Context.request(Service.SERVICE_NAME, "hello", "tester");
        // 发起ubsi请求：direct直连方式通常用于测试/容器治理，正常情况下应该使用call()路由方式
        String ack = (String)ubsi.direct("localhost", 7112);
        // 通过日志输出结果
        Context.getLogger("junit", "test-hello").info("return", ack);
        // 通过console输出结果
        System.out.println("return: " + ack);
    }

    /**
     * 参数配置测试
     *      微服务的参数配置接口通常会由UBSI治理工具调用，这里的测试可以用来验证接口的实现是否正确
     */
    @Test
    public void testConfig() throws Exception {
        // 获取微服务的配置参数：向容器控制器（名为""的微服务）发getConfig请求
        Context ubsi = Context.request("", "getConfig", Service.SERVICE_NAME);
        Map config = (Map)ubsi.direct("localhost", 7112);   // UBSI在传输数据时会将自定义的Java-Class映射为Map
        Request.printJson(config);  // 输出获取到的配置信息

        // 设置微服务的配置参数：向容器控制器（名为""的微服务）发setConfig请求
        Map new_cfg = Util.toMap("name", "new-name");
        String cfg_json = new Gson().toJson(new_cfg);
        ubsi = Context.request("", "setConfig", Service.SERVICE_NAME, cfg_json);
        ubsi.direct("localhost", 7112);     // 设置新的参数（会生成配置文件：rewin.ubsi.modules/ubsi.demo.hello/config.json）

        testHello();    // 调用hello接口，查看运行参数是否修改
    }

}
```

在执行测试前，需要在Project的根目录下手工创建rewin.ubsi.module.json文件，用来指定容器启动时需要自动加载的微服务，内容如下：

```json
{
  "services": {
    "ubsi.demo.hello": {
      "class_name": "ubsi.demo.hello.Service",
      "startup": true
    }
  }
}
```



执行testHello()测试案例，我们会得到如下结果：

```
[INFO]	2022-03-28 14:19:19.922	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello	[1]ubsi.demo.hello.Service#init()#35	start	"ubsi"
[INFO]	2022-03-28 14:19:24.234	liuxd-hp#7112	rewin.ubsi.container	rewin.ubsi.container	[1]rewin.ubsi.container.Bootstrap#start()#154	startup	"2.3.0"
return: ubsi: hello, tester
[INFO]	2022-03-28 14:19:26.531	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello#hello	[17]ubsi.demo.hello.ServiceEntry#hello()#20	ubsi	"hello, tester"
[INFO]	2022-03-28 14:19:26.546	liuxd-hp#7112	junit	test-hello	[1]ubsi.demo.hello.ServiceEntryTest#testHello()#43	return	"ubsi: hello, tester"
[INFO]	2022-03-28 14:19:26.546	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello	[1]ubsi.demo.hello.Service#close()#42	stop	"ubsi"
[INFO]	2022-03-28 14:19:26.546	liuxd-hp#7112	rewin.ubsi.container	rewin.ubsi.container	[1]rewin.ubsi.container.Bootstrap#stop()#190	shutdown	"2.3.0"
```

从日志我们可以看到代码的完整执行序列：

1. 容器加载微服务：start "ubsi"
2. 容器启动成功：startup "2.3.0"
3. 接口hello被访问：ubsi "hello, tester"
4. 测试用例得到返回结果：return "ubsi: hello, tester"
5. 微服务被关闭：stop "ubsi"
6. 容器被关闭：shutdown "2.3.0"

> 关于日志格式以及日志配置的更多说明请见 [UBSI日志](../core/logger.md)

关于服务结果的输出有一点需要特别注意：在代码顺序中后面的System.out.println()语句的输出结果反而出现在格式化日志输出之前，这是由于UBSI日志采用了异步/批量的输出方式，会有延迟，但不会由于日志信息的I/O处理造成任务线程的等待，从而优化微服务的性能；



再执行testConfig()测试案例，得到如下日志输出：

```
[INFO]	2022-03-28 14:36:19.402	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello	[1]ubsi.demo.hello.Service#init()#35	start	"ubsi"
[INFO]	2022-03-28 14:36:23.267	liuxd-hp#7112	rewin.ubsi.container	rewin.ubsi.container	[1]rewin.ubsi.container.Bootstrap#start()#154	startup	"2.3.0"
{
  "name_restart": "ubsi",
  "name": "ubsi",
  "name_comment": "服务实例的名字"
}
return: new-name: hello, tester
[INFO]	2022-03-28 14:36:25.370	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello#hello	[23]ubsi.demo.hello.ServiceEntry#hello()#20	new-name	"hello, tester"
[INFO]	2022-03-28 14:36:25.372	liuxd-hp#7112	junit	test-hello	[1]ubsi.demo.hello.ServiceEntryTest#testHello()#43	return	"new-name: hello, tester"
[INFO]	2022-03-28 14:36:25.377	liuxd-hp#7112	rewin.ubsi.service	ubsi.demo.hello	[1]ubsi.demo.hello.Service#close()#42	stop	"new-name"
[INFO]	2022-03-28 14:36:25.377	liuxd-hp#7112	rewin.ubsi.container	rewin.ubsi.container	[1]rewin.ubsi.container.Bootstrap#stop()#190	shutdown	"2.3.0"
```

我们可以看到微服务的配置项已经成功地从默认的"ubsi"改为了"new-name"，同时该配置项也已经保存到了配置文件 rewin.ubsi.modules/ubsi.demo.hello/config.json 中。