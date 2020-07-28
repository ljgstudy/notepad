### 数据库访问报错

---

连接数据库报错如下：

```
The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration
```

解决方案：

```
在连接字符串后面加上?serverTimezone=UTC

其中UTC是统一标准世界时间。

完整的连接字符串示例：jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai

或者还有另一种选择：jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8，这个是解决中文乱码输入问题，当然也可以和上面的一起结合：
jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
```



深入思考，为什么连接数据库必须要指定serverTimezone参数？

# MySQL Connect/J 8.0时区陷阱

最近公司正在升级Spring Boot版本（从1.5升级到2.1），其间踩到一个非常隐晦的MySQL时区陷阱，具体来说，就是数据库读出的历史数据的时间和实际时间差了14个小时，而新写入的数据又都正常。如果你之前也是使用默认的MySQL时区配置，那么大概率会碰到这个问题，深究其背后的原因又涉及到很多技术细节，故整理出来分享给大家。

首先来看一下原因。升级到Boot 2.1之后，MySQL Connect/J版本也随之升级到8.0，会优先使用连接参数（`serverTimezone`）中指定的时区，如果没有指定，则再使用数据库配置的时区，参考下面的[官宣](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-other-changes.html)（对应的源代码是`com.mysql.cj.protocol.a.NativeProtocol#configureTimezone()`）。由于我们之前数据库连接参数没有指定时区，并且数据库配置的是默认的`CST`时区（美国中部时区，即-6:00），所以读取出来的时间出现偏差。

> Connector/J 8.0 always performs time offset adjustments on date-time values, and the adjustments require one of the following to be true:
>
> - The MySQL server is configured with a canonical time zone that is recognizable by Java (for example, Europe/Paris, Etc/GMT-5, UTC, etc.)
> - The server's time zone is overridden by setting the Connector/J connection property `serverTimezone` (for example, `serverTimezone=Europe/Paris`).

找到原因之后，解决办法就比较直白了，

方法一：数据库的连接参数添加`serverTimezone=Asia/Shanghai`或者`serverTimezone=GMT%2B8`。Boot 1.5下不需要添加此参数，但添加了也无妨。

方法二：修改MySQL数据库的time_zone配置，改为`+8:00`（默认是`SYSTEM`）。采用此方法，则不需要修改数据库连接参数。

方法二显然更优，一次修改，终生受益。但要注意，对于升级到Boot 2.1之后新生成的那批数据，如果包含时间类型的字段并且该字段值是应用指定的而不是数据库生成的（例如`DEFAULT CURRENT_TIMESTAMP`），那么需要手动修复（加上偏差的小时数）。

两个解决办法都很简单，有同学马上会问，为什么Boot 1.5下没有这个问题？为什么Boot 2.0下读取历史数据存在14个小时的偏差，而新生成的数据又是好的？要回答这两个问题，看官宣就不够了，需要读一下MySQL Connect/J的源代码。

谜题一，为什么Boot 1.5下没有这个问题？答案隐藏在`com.mysql.jdbc.ResultSetImpl`和`com.mysql.jdbc.ConnectionImpl`两个类的源代码中。

```
// 源代码：com.mysql.jdbc.ResultSetImpl
private TimeZone getDefaultTimeZone() {
        // useLegacyDatetimeCode默认为true，因此使用connection的默认时区
        return this.useLegacyDatetimeCode ? this.connection.getDefaultTimeZone() : this.serverTimeZoneTz;
    }
复制代码
// 源代码：com.mysql.jdbc.ConnectionImpl
public ConnectionImpl(String hostToConnectTo, int portToConnectTo, Properties info, String databaseToConnectTo, String url) throws SQLException {
        // connection的默认时区使用的是JVM的默认时区，一般为操作系统的时区
        // We store this per-connection, due to static synchronization issues in Java's built-in TimeZone class...
        this.defaultTimeZone = TimeUtil.getDefaultTimeZone(getCacheDefaultTimezone());
}
复制代码
```

Boot 1.5下，MySQL Connect/J默认使用操作系统的时区（Asia/Shanghai，即+8:00），而忽略连接参数或者数据库指定的时区，因此不管是读数据还是写数据都是使用统一的时区，因此不存在时间偏差。

谜题二，为什么Boot 2.0下读取历史数据存在14个小时的偏差，而新生成的数据又是好的？升级到Boot 2.0之后，MySQL Connect/J改为使用数据库配置的CST时区，而历史数据是在Boot 1.5下的Asia/Shanghai时区生成的，因此读出来存在14（-6:00和+8:00之间）个小时的偏差。对于新生成的数据，由于同处在CST时区下，因此没有偏差。

解完这两个谜题，你可能还有些疑惑。那么接下来，结合数据流转的顺序，我们再来分析一下数据流转过程中时区的变化。



![image](https://user-gold-cdn.xitu.io/2019/12/18/16f167174f27a42c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



设定Application-1为数据生产方，Application-2为数据消费方，TZ-IN1为Application-1所处的时区，TZ-IN2为Application-1写入数据库的时区，TZ-OUT1为Application-2读出数据库的时区，TZ-OUT2为Application-2所处的时区。如前所述，TZ-IN2和TZ-OUT1由连接参数或者数据库配置决定。

整个数据流转过程，会涉及3次显式的时区转换和1次隐式的时区转换。

- 转换①（显式）：TZ-IN1转TZ-IN2，这个转换由MySQL Connect/J完成（参考`com.mysql.cj.ClientPreparedQueryBindings#setTimestamp()`，限于篇幅，此处不再展开分析）。
- 转换②（隐式）：TZ-IN2转无时区，MySQL内部存储时间类型的字段时或者忽略时区（DateTime类型）或者使用UTC（Timestamp类型），参考MySQL官宣的时间类型部分。
- 转换③（显式）：无时区转TZ-OUT1，将MySQL读出的无时区时间置为TZ-OUT1时区（参考`com.mysql.cj.result.SqlTimestampValueFactory#localCreateFromTimestamp()`）。
- 转换④（显式）：TZ-OUT1转TZ-OUT2，这个转换由Application-2负责，一般在DAO层完成。

仔细分析这4次时区转换，其中①、②、③都是由MySQL完成，正确性不用怀疑，但由于TZ-IN2和TZ-OUT1都是由应用指定，如果两者值不相同，那么最后结果就会出现偏差（我们踩到的就是这个坑）。至于④，那么就得靠应用来保证正确性了，一般也不会出错。说句题外话，不管是时区转换，还是其他类型的数据转换（比如字符集转换），我们可以发现，正确转换的关键在于数据接收方必须使用和数据发送方相同的格式。这看上去像是一句废话，却是解决此类问题的底层心法。

至此，这个MySQL Connect/J 8.0的时区陷阱就算被填平了，希望你从中有所收获。

