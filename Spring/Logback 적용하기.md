# 스프링 부트에 Logback 적용하기  

## Logback 이란?  

Java에서 많이 사용되던 로깅 라이브러리인 log4j를 기반으로 개발된 로깅 라이브러리이다.  

## Logback 특징  

- 로그에 레벨을 설정할 수 있다.  
- 운영 환경과 테스트 환경에 따라 각각 다른 출력 레벨을 설정하여 로그를 확인할 수 있다.  
- 출력 방식에 대해 설정할 수 있다.  
- 설정 파일을 일정 시간마다 스캔해 어플리케이션의 중단 없이 설정을 변경할 수 있다.  
- 별도의 프로그램 없이 자체적으로 로그 압축을 지원하며 로그 보관 기간도 설정 할 수 있다.  

## Logback 설정  

일반적으로 classpath에 있는 logback 설정 파일을 참조한다.  

Spring Boot의 경우 `logback-spring.xml` 파일을 참조한다.  

## Logback 설정 파일 형식  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds" debug="true">

    <property name="LOGS_ABSOLUTE_PATH" value="logs"/>

    <!-- 콘솔로 로그를 남김 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <!-- 로그 메시지 패턴 -->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 파일로 로그를 남김 -->
    <appender name="ROLLFILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOGS_ABSOLUTE_PATH}/logback.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOGS_ABSOLUTE_PATH}/logback.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- or whenever the file size reaches 100MB -->
                <!-- kb, mb, gb -->
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>

            <!-- 30일 -->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- 에러 일 경우 파일에 로그를 남김 -->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${LOG_DIR}/bamdule-error.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOGS_ABSOLUTE_PATH}/logback.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- Database Section -->
    <logger name="org.apache.commons.dbcp2" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <logger name="java.sql" level="warn" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="jdbc" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <!-- SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 포함 -->
    <logger name="jdbc.sqltiming" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="jdbc.resultsettable" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <!-- netflix -->
    <logger name="com.netflix.discovery" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <!-- Hikari CP -->
    <logger name="com.zaxxer.hikari.pool.HikariPool" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- Application Section -->
    <logger name="pbb" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- Framework Section -->
    <logger name="egovframework" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework.scheduling" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework.web" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- root는 글로벌 로거를 의미하며, 위의 logger에 해당하지 않으면 root 로거가 실행 -->
    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </root>

</configuration>
```

#### appender  

로그의 형태 및 어디에 출력할지 설정하기 위한 영역이다.  

자주 사용하는 Appender 형식은 다음과 같다.  

- **ch.qos.logback.core.ConsoleAppender:** 콘솔에 로그를 기록한다.
- **ch.qos.logback.core.FileAppender:** 파일에 로그를 기록한다.
- **ch.qos.logback.core.rolling.RollingAppender:** 여러 개의 파일을 순회하며 로그를 저장한다.
- **ch.qos.logback.classic.net.SMTPAppender:** 로그를 메일로 보낸다.
- **ch.qos.logback.classic.db.DBAppender:** 데이터베이스에 로그를 저장한다.

#### encoder  

Appender 내에 포함되는 항목으로 pattern을 사용해 원하는 형식으로 로그를 표현할 수 있다.  

사용되는 pattern 형식은 다음과 같다.  

- %Logger{length} : Logger name을 축약할 수 있다. {length}는 최대 자리 수, ex)logger{35}
- %-5level : 로그 레벨, -5는 출력의 고정폭 값(5글자) 
- %msg : - 로그 메시지 (=%message) 
- ${PID:-} : 프로세스 아이디 
- %d : 로그 기록시간 
- %p : 로깅 레벨 
- %F : 로깅이 발생한 프로그램 파일명 
- %M : 로깅일 발생한 메소드의 명 
- %l : 로깅이 발생한 호출지의 정보 
- %L : 로깅이 발생한 호출지의 라인 수 
- %thread : 현재 Thread 명 
- %t : 로깅이 발생한 Thread 명 
- %c : 로깅이 발생한 카테고리 
- %C : 로깅이 발생한 클래스 명 
- %m : 로그 메시지 
- %n : 줄바꿈(new line) 
- %% : %를 출력 
- %r : 애플리케이션 시작 이후부터 로깅이 발생한 시점까지의 시간(ms)

```xml
<pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %logger %msg%n</pattern>
```

#### root  

설정한 Appender를 참조해 로그의 레벨을 설정할 수 있다. `root`는 전역 설정이며 지역 설정을 하기 위해서는 `logger`를 사용한다.  

#### property  

설정파일에서 사용될 변수 값을 선언한다.  

```xml
<property name="LOG_PATH" value="{log.config.path}" />
```

## 로그 레벨  

```text
TRACH → DEBUG → INFO → WARN → ERROR

1) ERROR : 로직 수행 중 오류가 발생한 경우, 보통 시스템적으로 심각한 문제가 발생해 동작이 불가능한 경우이다.
2) WARN  : 시스템 에러의 원인이 될 수 있는 경고 레벨로 처리가 가능한 상황이다.
3) INFO  : 상태 변경과 같은 정보성 로그 레벨이다.
4) DEBUG : 어플리케이션의 디버깅을 위한 로그 레벨이다.
5) TRACE : DEBUG 레벨보다 더욱 세부적인 로그를 표현하기 위한 레벨이다.
```

로그에 설정할 수 있는 레벨은 총 5가지이며 순서대로 높은 레벨을 가진다.  

출력할 로그 레벨에 따라 설정한 레벨 이상의 로그를 출력한다.  

예를 들어 로깅레벨을 `INFO`로 설정하면 `INFO`를 포함해 `WARN, ERROR`를 출력한다.  

---

#### 📌 Reference  

- <https://medium.com/jongho-developer/spring-boot-logging-구조-logback-e62c691128b>
- <https://goddaehee.tistory.com/206>
- <https://icarus8050.tistory.com/117>
- <https://dejavuhyo.github.io/posts/spring-boot-logback/>