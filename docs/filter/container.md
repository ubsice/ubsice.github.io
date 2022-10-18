# 容器端过滤器

---

容器端过滤器的代码如下：

```java
package ubsi.demo.filter;

import org.bouncycastle.util.encoders.Hex;
import rewin.ubsi.annotation.USAfter;
import rewin.ubsi.annotation.USBefore;
import rewin.ubsi.annotation.USFilter;
import rewin.ubsi.consumer.ErrorCode;
import rewin.ubsi.container.ServiceContext;

import java.nio.charset.StandardCharsets;

/** UBSI容器过滤器 */
@USFilter(
        tips = "UBSI过滤器示例",         // 说明
        version = "1.0.0",              // 版本
        release = false                 // 发布状态，false表示社区版
)
public class Container {

    final static String HEADER_KEY = "ubsi.demo.filter";

    boolean deal = false;       // 是否需要加解密处理

    /** 服务接口调用前的拦截动作 */
    @USBefore
    public void before(ServiceContext ctx) throws Exception {
        if ( !"ubsi.demo.hello".equals(ctx.getServiceName()) || !"hello".equals(ctx.getEntryName()) )
            return;     // 仅对ubsi.demo.hello服务的hello()接口做处理

        // 从请求的Header中获得加密标志
        Boolean encrypt = (Boolean) ctx.getHeader(HEADER_KEY);
        if ( encrypt != null && encrypt ) {
            // 请求参数已经加密了
            String param = (String)ctx.getParam(0);     // 获取参数
            if ( param != null && !param.isEmpty() ) {
                param = new String(Hex.decode(param), StandardCharsets.UTF_8);  // 将16进制字符串转换为实际参数(伪解密)
                ctx.setParam(param);    // 将参数设置为解密后的字符串
                deal = true;
            }
        }
    }

    /** 服务接口调用后的拦截动作 */
    @USAfter
    public void after(ServiceContext ctx) throws Exception {
        if ( deal && ctx.hasResult() && ctx.getResultCode() == ErrorCode.OK ) {
            // 接口成功返回且需要处理
            String res = (String)ctx.getResultData();   // 获得返回结果
            res = Hex.toHexString(res.getBytes(StandardCharsets.UTF_8));    // 将结果转换为16进制字符串(伪加密)
            ctx.setResultData(res);     // 将结果设置为加密后的字符串
        }
    }

}
```



说明：

- 容器端过滤器的主类需要用@USFilter注解进行声明
- 过滤器只对ubsi.demo.hello服务的hello()接口进行处理
- 过滤器进行处理前需要判断请求Header中的ubsi.demo.filter属性，如果未设置，可能是Consumer未设置对应的consumer-filter，这时不做处理，请求依然可以正常进行