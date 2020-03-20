# Service Discovery: Eureka Server

本文基于 Spring Boot 来快速搭建 Eureka Server。首先，在 Spring Boot 项目中添加 Maven 依赖管理：

```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-dependencies</artifactId>
	<version>${spring-cloud.version}</version>
	<type>pom</type>
	<scope>import</scope>
</dependency>

<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

然后，在启动类上添加 @EnableEurekaServer 注解类：

```
@SpringBootApplication
@EnableEurekaServer
public class Application {

    public static void main(String[] args) {
        new SpringApplicationBuilder(Application.class).web(true).run(args);
    }

}
```

公共配置我们使用`bootstrap.yml`来管理：

```
# bootstrap.yml
eureka:
  instance:
    prefer-ip-address: true
    lease-renewal-interval-in-seconds: 10
    lease-expiration-duration-in-seconds: 30
    metadataMap:
      instanceId: ${spring.cloud.client.ipAddress}:${server.port}
  server:
    enable-self-preservation: true
    registry-sync-retry-wait-ms: 500
    a-s-g-cache-expiry-timeout-ms: 60000
    eviction-interval-timer-in-ms: 30000
    peer-eureka-nodes-update-interval-ms: 30000
    renewal-threshold-update-interval-ms: 15000
  client:
    register-with-eureka: false
    fetch-registry: false
    instance-info-replication-interval-seconds: 10
    registry-fetch-interval-seconds: 10
  dashboard:
    path: /
```

接下来，我们重点介绍不同模式如何使用配置参数。

## 1. 单机模式

单机模式仅适用于测试环境，生产环境还是要采用集群高可用模式。

```
# application-dev.yml
eureka:
  environment: development
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

## 2. 集群模式

搭建集群环境时，你只要确保每个节点指向集群中的其他节点即可。这里我们使用5个节点来搭建集群：

```
# application-peer1.yml
eureka:
  environment: production
  client:
    serviceUrl:
      defaultZone: http://peer2:8761/eureka/,http://peer3:8761/eureka/,http://peer4:8761/eureka/,http://peer5:8761/eureka/

---

# application-peer2.yml
eureka:
  environment: production
  client:
    serviceUrl:
      defaultZone: http://peer1:8761/eureka/,http://peer3:8761/eureka/,http://peer4:8761/eureka/,http://peer5:8761/eureka/

---

# application-peer3.yml
eureka:
  environment: production
  client:
    serviceUrl:
      defaultZone: http://peer1:8761/eureka/,http://peer2:8761/eureka/,http://peer4:8761/eureka/,http://peer5:8761/eureka/

---

# application-peer4.yml
eureka:
  environment: production
  client:
    serviceUrl:
      defaultZone: http://peer1:8761/eureka/,http://peer2:8761/eureka/,http://peer3:8761/eureka/,http://peer5:8761/eureka/

---

# application-peer5.yml
eureka:
  environment: production
  client:
    serviceUrl:
      defaultZone: http://peer1:8761/eureka/,http://peer2:8761/eureka/,http://peer3:8761/eureka/,http://peer4:8761/eureka/
```

修改集群地址的映射：

```
# Windows: C:\Windows\System32\drivers\etc\hosts
# Linux: /etc/hosts
192.168.10.1    peer1
192.168.10.2    peer2
192.168.10.3    peer3
192.168.10.4    peer4
192.168.10.5    peer5
```

## 3. 多节点高可用模式

在实际生产应用中，需要确保服务的高可用性。这时，我们可以在单机或集群模式的基础上增加多节点支持，修改如下：

```
eureka:
  environment: zone
  client:
    region: cn-shanghai-1
    serviceUrl:
      zone1: http://zone1:8761/eureka/
      zone2: http://zone2:8761/eureka/
    availability-zones:
      cn-shanghai-1: zone1,zone2
```

这样我们就有两个节点来确保服务的高可用性，即便一个节点故障还能有另一个节点来提供服务。