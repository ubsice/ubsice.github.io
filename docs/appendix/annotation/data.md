# 数据注解

---

UBSI的数据注解包括：

- @USNotes - 声明一个数据模型
- @USNote - 声明数据模型中的一个属性



### @USNotes | @USNote

如果微服务的输入参数或返回结果包含有特定的数据结构时，可以通过这两个注解对自定义的数据结构进行描述，以方便开发人员对数据进行处理。示例：

```java
package my.service.samples;

import rewin.ubsi.annotation.*;
import rewin.ubsi.container.ServiceContext;
import java.util.*;

@UService(
	name = "my.samples.demo",           // 微服务的名字，缺省为""
	tips = "测试服务",                   // 微服务的说明，缺省为""
)
public class DemoService {
	@USNotes("运行信息")				 // 标注一个数据模型
	public static class RuntimeInfo {
		@USNote("实例的名字")			// 标注模型中的属性
		public String instanceName;
		@USNote("启动时间")
		public long timestamp;
	}
	
	@USEntry(
		tips = "获得数据模型的说明",
		result = "数据模型的说明，格式：{ \"模型1\": { \"字段1\": \"描述\", ... }, ... }"
	)
	public Map<String,Map<String,String>> getModels(ServiceContext ctx) {
		return Util.getUSNotes(RuntimeInfo.class);	// 提取@USNotes注解的工具
	}
	
	@USEntry(
		tips = "获得运行信息",
		result = "Map<String,Object>，对应RuntimeInfo模型"
	)
	public RuntimeInfo getRuntimeInfo(ServiceContext ctx) throws Exception {
		RuntimeInfo info = new RuntimeInfo();
		info.instanceName = "实例名字";
		info.timestamp = System.currentTimeMillis();
		return info;	// 访问端接收到的是一个经过泛化的Map对象
	}
}
```

注意：

- 被标注的必须是public class及其成员变量
- 可以通过一个特定的服务接口来返回标注的模型

