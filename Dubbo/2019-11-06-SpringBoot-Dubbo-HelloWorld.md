> SpringBoot Dubbo 项目结构

```shell
# 视图:shell: tree ./springboot-dubbo/
./springboot-dubbo/
├── pom.xml
├── springboot-dubbo-consumer
│   ├── pom.xml
│   └── src
│       ├── main
│       │   ├── java
│       │   │   └── com
│       │   │       └── example
│       │   │           └── consumer
│       │   │               └── ConsumerApplication.java
│       │   └── resources
│       │       └── application.properties
│       └── test
│           └── java
├── springboot-dubbo-interface
│   ├── pom.xml
│   └── src
│       ├── main
│       │   ├── java
│       │   │   └── com
│       │   │       └── example
│       │   │           └── dubbointer
│       │   │               ├── dto
│       │   │               │   └── OperationDTO.java
│       │   │               └── service
│       │   │                   └── OperationService.java
│       │   └── resources
│       └── test
│           └── java
├── springboot-dubbo-provider
│   ├── pom.xml
│   └── src
│       ├── main
│       │   ├── java
│       │   │   └── com
│       │   │       └── example
│       │   │           └── dubboprov
│       │   │               ├── ProviderApplication.java
│       │   │               └── service
│       │   │                   └── impl
│       │   │                       └── OperationServiceimpl.java
│       │   └── resources
│       │       └── application.properties
│       └── test
│           └── java
└── src
    ├── main
    │   ├── java
    │   └── resources
    └── test
        └── java
```

> [安装 ZK ( zookeeper )](https://gitee.com/jiangruyi/notebook/blob/master/MQ/Kafka/01-Kafka-%E5%AE%89%E8%A3%85%E4%B8%8ESpringBoot%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8.md)

> 在父工程中配置 springboot-dubbo/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>org.example</groupId>
    <artifactId>springboot-dubbo</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>

    <modules>
        <module>springboot-dubbo-interface</module>
        <module>springboot-dubbo-provider</module>
        <module>springboot-dubbo-consumer</module>
    </modules>

    <properties>
        <org.projectlombok.version>1.18.12</org.projectlombok.version>
        <org.apache.dubbo.starter.version>2.7.3</org.apache.dubbo.starter.version>
        <org.apache.curator.version>5.1.0</org.apache.curator.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${org.projectlombok.version}</version>
            <optional>false</optional>
        </dependency>

        <!-- Spring Boot相关依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

> 公共接口服务 ) 相关代码:: springboot-dubbo/springboot-dubbo-interface

> pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springboot-dubbo</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springboot-dubbo-interface</artifactId>

</project>
```

> 数据传输层DTO

```java
package com.example.dubbointer.dto;

import lombok.Data;
import java.io.Serializable;

@Data
public class OperationDTO implements Serializable {
    private StringBuilder chain = new StringBuilder();
}
```

> 公用接口服务

```java
package com.example.dubbointer.service;

import com.example.dubbointer.dto.OperationDTO;

public interface OperationService {
    OperationDTO operation(OperationDTO dto);
}
```

> 接口提供方服务 ) 相关代码:: springboot-dubbo/springboot-dubbo-provider

> pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springboot-dubbo</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springboot-dubbo-provider</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>springboot-dubbo-interface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <!-- 引入spring-boot-starter以及dubbo和curator的依赖 -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>${org.apache.dubbo.starter.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>${org.apache.curator.version}</version>
        </dependency>
    </dependencies>

</project>
```

> 实现接口提供服务

```java
package com.example.dubboprov.service.impl;

import com.example.dubbointer.dto.OperationDTO;
import com.example.dubbointer.service.OperationService;
import org.apache.dubbo.config.annotation.Service;

@Service
public class OperationServiceimpl implements OperationService {

    private static int icount;

    @Override
    public OperationDTO operation(OperationDTO dto) {
        dto.getChain().append("provider(").append(++icount).append(")");
        return dto;
    }

}

```

> SpringBoot启动类

```java
package com.example.dubboprov;

import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@EnableDubbo
@SpringBootApplication
public class ProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }

}
```

> 配置文件

```properties
#当前服务/应用的名字
dubbo.application.name=springboot-dubbo-provider
#注册中心的协议和地址
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181

#通信规则（通信协议和接口）
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

#连接监控中心
dubbo.monitor.protocol=registry
#开启包扫描，可替代 @EnableDubbo 注解
##dubbo.scan.base-packages=com.example.dubboprov
```

> 接口消费方服务 ) 相关代码:: springboot-dubbo/springboot-dubbo-consumer

> pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springboot-dubbo</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springboot-dubbo-consumer</artifactId>

    <dependencies>
        <!-- springboot web模块 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.example</groupId>
            <artifactId>springboot-dubbo-interface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <!-- 引入spring-boot-starter以及dubbo和curator的依赖 -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>${org.apache.dubbo.starter.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>${org.apache.curator.version}</version>
        </dependency>
    </dependencies>

</project>
```

> SpringBoot 启动类

```java
package com.example.consumer;

import com.example.dubbointer.dto.OperationDTO;
import com.example.dubbointer.service.OperationService;
import org.apache.dubbo.config.annotation.Reference;
import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@EnableDubbo
@RestController
@SpringBootApplication
public class ConsumerApplication {

    @Reference
    private OperationService operationService;

    @GetMapping("dubbo")
    public OperationDTO dubbo(OperationDTO dto) {
        return operationService.operation(dto);
    }

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class);
    }

}
```

> 配置文件

```properties
#避免和监控中心端口冲突，设为8081端口访问
server.port=8081

dubbo.application.name=springboot-dubbo-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.monitor.protocol=registry
```

* 然后先启动服务提供方
* 其次后启动服务消费方
* 访问: http://localhost:8081/dubbo?chain=chain

