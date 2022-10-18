# JS脚本API

---

### Java代码中的API

如果希望在Java代码中嵌入JS代码的执行能力，可以使用ScriptUtil的API：

```java
package rewin.ubsi.common;

public class ScriptUtil {

    /** 脚本输出消息的格式 */
    public static class Message {
        public long     time;   // 时间戳，毫秒数
        public int      type;   // 消息类别，LogUtil.DEBUG | INFO | ERROR
        public String   text;   // 消息内容
    }

    /** API说明，格式：{ "方法": "说明" } */
    public static Map Api;
    
    /** 脚本的执行结果 */
    public Object  Result;

	/** 脚本执行过程中的输出消息记录 */
    public List<Message> Messages;

    /** 构造函数 */
    public ScriptUtil() {}
    /** 构造函数，指定服务容器的地址 */
    public ScriptUtil(String host, int port);
    /** 构造函数，指定日志记录的属性 */
    public ScriptUtil(String appTag, String appID, String tips);
    
    /** 设置脚本日志的输出级别："NONE" | "DEBUG" | "INFO" | "ERROR" */
	public static void setLogLevel(String level);
	
	/** 获得脚本日志的输出级别 */
    public static String getLogLevel();
    
    /** 获得JavaScript脚本执行引擎
     *  参数：
	 *    context - ScriptUtil对象实例，为JS脚本提供'$'对象
	 *    var - 供JS脚本使用的其他环境变量
     *  返回：
	 *     javax.script.ScriptEngine对象
     */
	public static ScriptEngine getEngine(ScriptUtil context, Map<String, Object> var);
    
    /** 执行JavaScript脚本
     *  参数：
	 *    engine - JavaScript执行引擎
	 *    js - JavaScript脚本代码
     *  返回：
	 *     JavaScript脚本代码的执行结果
     */
	public static Object runJs(ScriptEngine engine, String js) throws Exception;
    
    /** 执行JavaScript脚本
     *  参数：
	 *    js - JavaScript脚本代码
	 *    context - ScriptUtil对象实例，为JS脚本提供'$'对象
	 *    var - 供JS脚本使用的其他环境变量
     *  返回：
	 *     JavaScript脚本代码的执行结果
     */
	public static Object runJs(String js, ScriptUtil context, Map<String, Object> var) throws Exception;

}
```



### JS代码中的API

通过ScriptUtil加载的JS代码，都可以使用"$"对象提供的API：

- $.host('container-host', port)
  设置UBSI请求的目标容器，container-host为null表示路由模式，缺省为路由模式

- $.header({...})
  设置UBSI请求的Header
  
- $.version('min_ver', 'max_ver', release)
  设置UBSI请求的服务版本限制，release取值：-1-不限，0-非release，1-release

- $.timeout(seconds)

  设置UBSI请求的超时时间（秒数），0表示不限，-1表示使用缺省值

- $.async(true|false)

  设置同步/异步方式发送请求，缺省为同步方式

- $.request('service', 'entry', ...)

  发送UBSI请求，同步方式返回结果，异步方式直接返回null

- $.result(data)

  设置脚本的返回结果，如果不设置，则将最后一条语句的值作为脚本结果

- $.sleep(millis)

  暂停millis毫秒

- $.json(obj)

  将obj转换为json字符串，JS解析：val=eval('(' + json + ')');

- $.debug('msg')

  输出一条debug信息

- $.info('msg')

  输出一条info信息

- $.error('msg')

  输出一条error信息

- $.broadcast('channel', data)

  发送一条广播消息

- $.throwEvent('channel', data)

  发送一条事件消息

- $._byte(n)

  将JS的number转换为Java的byte

- $._int(n)

  将JS的number转换为Java的int

- $._long(n)

  将JS的number转换为Java的long

- $._bigint('str')

  将JS字符串转换为Java的BigInteger

- $._double(n)

  将JS的number转换为Java的double

- $._bignum('str')

  将JS字符串转换为Java的BigDecimal

- $._bytes([...])

  将JS的number数组转换为Java的byte[]

- $._map({...})

  将JS的对象转换为Java的Map

- $._list([...])

  将JS数组转换为Java的List（注：Java会将JS的[]当成Map）

- $._set([...])

  将JS数组转换为Java的Set

- $._array([...])

  将JS数组转换为Java的Object[]



另外在JS代码中还可以直接使用Packages.xxx来引用UBSI核心包关联的任意Java类，例如：

`var array = new Packages.java.util.ArrayList();`