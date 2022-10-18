# Consumer组件

---

要访问UBSI微服务，必须使用Consumer组件。UBSI目前只提供了Java的实现（后续会陆续提供其他语言的接口包），如果异构系统需要访问，可以使用 [UBSI Gateway网关](../gateway/readme.md)。



UBSI Consumer提供的功能：

- 统一的微服务访问接口（不依赖任何微服务Provider的实现类）
- 支持同步/异步机制
- 基于注册中心的服务发现和动态路由
- 静态路由配置
- 服务接口的仿真



一个Consumer组件的简单示例：

```java
import rewin.ubsi.consumer.Context;

public class Test {
	public static void main(String[] args) throws Exception {
        // 启动UBSI Consumer，指定"."为当前工作目录
        Context.startup(".");
        // 创建UBSI请求对象
        Context ubsi = Context.request("ubsi.demo.hello", "hello", "consumer"); 
        // 同步方式发送请求，并等待返回结果
        String res = (String)ubsi.call();
        // 关闭UBSI Consumer
        Context.shutdown();
    }
}
```

> 关于Context类的更多说明请见 [Consumer Context API](../appendix/context.md)



需要注意的是：如果是在一个微服务中访问其他的微服务，请不要直接使用Consumer组件的Context.request()来创建请求对象，应该使用容器提供的ServiceContext的request()方法，ServiceContext封装了容器中的Consumer组件，可以保证服务依赖、访问链路以及事务传播的有效性。另外，也不需要进行Context的startup/shutdown操作，容器启动时已经完成了Consumer组件的准备工作。

> 关于ServiceContext类的更多说明请见 [Service Context API](../appendix/service-context.md)



#### Consumer的运行参数配置

Consumer的运行参数保存在配置文件rewin.ubsi.consumer.json中，一个简单的样例如下：

```json
{
  "io_threads": 4,
  "timeout_connect": 5,
  "timeout_request": 10,
  "timeout_reconnect": 600,

  "redis_host": "{redis-server-address}",
  "redis_port": 6379,
  "redis_conn_idle": 2,
  "redis_conn_max": 16,
}
```

- io_thread

  Consumer组件用来处理socket I/O的线程数，0表示默认设置（CPU内核数*2）

- timeout_connect

  向服务容器发起socket连接时的缺省超时时间，秒数

- timeout_request

  服务请求的缺省超时时间，秒数

- timeout_reconnect

  发现容器/redis服务节点失效后，再次重试的时间间隔，秒数

- redis_host | redis_port

  redis服务的地址和端口

- redis_conn_idle

  redis连接池的最大空闲数量

- redis_conn_max

  redis连接池的最大数量

上面的配置是针对redis单节点模式，如果部署了多节点的"哨兵"模式，需要设置redis_master_name和redis_sentinel_addr；

> 对于容器和WebApp，Consumer的运行参数可以通过UBSI治理工具进行动态配置



#### Consumer的静态路由配置

Consumer的静态路由保存在配置文件rewin.ubsi.router.json中，一个样例如下：

```json
[
  {
    "Service": "ubsi.demo.*",
    "Nodes": [
      {
        "Host": "localhost",
        "Port": 7112,
        "Weight": 2
      },
      {
        "Host": "localhost",
        "Port": 7113,
        "Weight": 1
      }
    ]
  }
]
```

这个路由配置表示：所有以"ubsi.demo."开头的微服务的访问请求都按照2:1的比例分别发送到"localhost#7112"和"localhost#7113"两个容器。如果将某个容器的Weight设置为0（默认值为1），表示这是一个"备用"容器，只有当另一个容器失效后才会启用。

> 需要注意同时存在动态路由和静态路由时，静态路由的优先级在动态路由之上

> 对于容器和WebApp，Consumer的静态路由可以通过UBSI治理工具进行动态配置

