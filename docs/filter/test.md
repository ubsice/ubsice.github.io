# 过滤器部署及测试

---

首先要部署容器端过滤器，部署步骤如下：

- 启动一个容器，并加载ubsi.demo.hello服务 -> 在demo.hello的项目目录下执行：

  `mvn ubsi:run`

- 将ubsi.demo.filter的容器过滤器部署到刚启动的容器 localhost#7112 -> 在demo.filter的项目目录下执行：

  `mvn ubsi:deploy`



容器端过滤器部署成功后，我们可以用Request命令行工具来访问一下服务接口。

先要设置consumer-filter，在一个单独的目录下创建rewin.ubsi.consumer.json文件，内容如下：

```json
{
  "filters": [ "ubsi.demo.filter.Consumer" ]
}
```


然后执行Request命令：

`java -cp ubsi-core-ce-2.3.1-jar-with-dependencies.jar;ubsi-filter-demo-1.0.jar rewin.ubsi.cli.Request ubsi.demo.hello hello 测试员`

会得到如下输出结果：

```
before: 参数 "测试员" 被加密为 "e6b58be8af95e59198"
after: 结果 "756273693a2068656c6c6f2c20e6b58be8af95e59198" 被解密为 "ubsi: hello, 测试员"
"ubsi: hello, 测试员"

```
