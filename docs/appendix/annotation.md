# UBSI注解

---

UBSI通过一系列预定义的注解来声明微服务及其接口，这些注解包括：

- [服务注解](annotation/service.md)
  - @UService - 声明微服务
  - @USFilter - 声明过滤器
  - @USDepend - 声明服务的依赖关系
- [接口注解](annotation/entry.md)
  - @USEntry - 声明服务接口
  - @USParam - 声明接口参数
  - @USBefore - 声明前置拦截方法
  - @USAfter - 声明后置拦截方法
  - @USInit - 声明初始化方法
  - @USClose - 声明结束方法
  - @USInfo - 声明获取运行信息的方法
  - @USConfigGet - 声明获取配置参数的方法
  - @USConfigSet - 声明设置配置参数的方法
- [数据注解](annotation/data.md)
  - @USNotes - 声明一个数据模型
  - @USNote - 声明数据模型中的一个属性
