# expcetion-tracking-with-sentry

# 概述
> Open-source error tracking that helps developers monitor and fix crashes in real time. Iterate continuously. Boost efficiency. Improve user experience.

sentry是一个现代化的错误日志记录和聚合平台。支持几乎所有主流开发语言和平台, 并提供了现代化UI, 本文简单介绍一下使用sentry平台记录后台错误日志。


# 1.注册

访问[https://sentry.io/signup/](https://sentry.io/signup/)注册一个sentry账号。


# 2.创建Team，Project
用创建好的账号登录，创建Team和Project.

![image](/assets/blogImg/expcetion-tracking-with-sentry/project_setting.jpg)


设置下语言和时区

![image](/assets/blogImg/expcetion-tracking-with-sentry/account_setting.jpg)



# 3.集成到Spring Boot项目

参考[https://github.com/pwielgolaski/sentry](https://github.com/pwielgolaski/sentry)

1). pom.xml
增加raven-logback依赖
```xml
<dependency>
    <groupId>net.kencochrane.raven</groupId>
    <artifactId>raven-logback</artifactId>
    <version>6.0.0</version>
</dependency>
```

2). logback.xml
增加sentry配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />
    <property name="LOG_FILE" value="${LOG_FILE:-${LOG_PATH:-${LOG_TEMP:-${java.io.tmpdir:-/tmp}}/}spring.log}"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml" />

    <appender name="SENTRY" class="net.kencochrane.raven.logback.SentryAppender">
        <dsn>YOUR_URL_PROVIDED_BY_SENTRY</dsn>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>WARN</level>
        </filter>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="SENTRY" />
    </root>

</configuration>
```
dsn这里配置项目的dsn地址，如下图所示：

![image](/assets/blogImg/expcetion-tracking-with-sentry/dsn.jpg)

3). 测试类
```java
@SpringBootApplication
@RestController
public class SentryApplication {

    private final static Logger logger = LoggerFactory.getLogger(SentryApplication.class);

    public static void main(String[] args) {
        SpringApplication.run(SentryApplication.class, args);
    }

    @RequestMapping(method = RequestMethod.GET)
    public String generateErrors() {
        logException(new NullPointerException("NPE"));
        logException(new IllegalArgumentException("Illegal"));
        return "Exceptions generated";
    }

    private void logException(Exception ex) {
        logger.error("Logger message for " + ex.getClass().getSimpleName(), ex);
    }
}
```
运行测试类，可以看到日志已经记录到sentry上去了。

![image](/assets/blogImg/expcetion-tracking-with-sentry/Unresolved_Issues.jpg)


点击问题可以查看问题详细：

![image](/assets/blogImg/expcetion-tracking-with-sentry/issue_detail.jpg)

点击[分享该事件]可以将分享该事件：

![image](/assets/blogImg/expcetion-tracking-with-sentry/share_issue.jpg)


以上便是利用sentry记录分析错误日志基本功能。
