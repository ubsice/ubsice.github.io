# 数据类型转换

------



## rewin.ubsi.common.Codec

- **将Java数据转换为UBSI基础数据对象**

  ```java
  public static Object toObject(Object value);
  ```

  参数：

  - value - Java数据对象

  返回：

  - [UBSI基础数据对象](data-type.md)

  

- **将数据对象转换为指定的数据类型**

  ```java
  public static <T> T toType(Object obj, Type type, Type... typeArguments);
  ```

  参数：

  - obj - Java数据对象
  - type - 目标数据类型
  - typeArguments - 如果目标数据类型是"泛型"，指明泛型需要的数据类型

  返回：

  - 指定数据类型的对象

  示例：

  ```java
  List<Integer> value = Codec.toType(new Object[] { 1, 2, 3 }, ArrayList.class, Integer.class);
  ```




- **将数据对象编码为Base64编码的字符串**

  ```java
  public static String encode(Object data);
  ```

  参数：

  - data - Java数据对象

  返回：

  - Base64编码的字符串

  

- **将Base64编码的字符串解码为数据对象**

  ```java
  public static Object decode(String data) throws Exception;
  ```

  参数：

  - data - Base64编码的字符串

  返回：

  - Java数据对象

  

- **将数据对象编码为字节数据**

  ```java
  public static byte[] encodeBytes(Object data);
  ```

  参数：

  - data - Java数据对象

  返回：

  - 字节数据

  

- **将字节数据解码为数据对象**

  ```java
  public static Object decodeBytes(byte[] data) throws Exception;
  ```

  参数：

  - data - 字节数据

  返回：

  - Java数据对象



## rewin.ubsi.common.XmlCodec

- **将UBSI格式的xml字符串解码为Java数据对象**

  ```java
  public static Object decode(String str) throws Exception;
  ```

  参数：

  - str - UBSI格式的xml字符串，详见 [UBSI数据编码](data-type.md)

  返回：

  - Java数据对象

  

- **将Java数据对象编码为UBSI格式的xml字符串**

  ```java
  public static String encode(Object obj, boolean strCData, boolean filterHeader) throws Exception;
  ```

  参数：

  - obj - Java数据对象
  - strCData - 是否将String内容放在<![CDATA[...]]>中
  - filterHeader - 是否滤掉<?xml version="1.0" encoding="UTF-8"?>

  返回：

  - UBSI格式的xml字符串



## rewin.ubsi.common.JsonCodec

- **将UBSI格式的json字符串解码为Java数据对象**

  ```java
  public static Object fromJson(String str) throws Exception;
  ```

  参数：

  - str - UBSI格式的json字符串，详见 [UBSI数据编码](data-type.md)

  返回：

  - Java数据对象
  
  


- **将正常格式的json字符串解码为Java数据对象**

  ```java
  public static Object simpleJson(String str) throws Exception;
  ```

  参数：

  - str - 正常格式的json字符串

  返回：

  - Java数据对象
  
  


- **将Java数据对象编码为UBSI格式的JsonElement**

  ```java
  public static JsonElement toJson(Object obj) throws Exception;
  ```

  参数：

  - obj - Java数据对象

  返回：

  - com.google.gson.JsonElement对象



## rewin.ubsi.common.Util

- **将json字符串转换为指定的数据类型**

  ```java
  public static <T> T json2Type(String json, Type type, Type... typeArguments);
  ```

  参数：

  - json - 正常的json格式字符串
  - type - 目标数据类型
  - typeArguments - 如果目标数据类型是"泛型"，指明泛型需要的数据类型

  返回：

  - 指定数据类型的对象


