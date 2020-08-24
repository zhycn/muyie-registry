# Service Discovery: Eureka Clients

本文基于 Spring Boot 来快速搭建 Eureka Client。首先，在 Spring Boot 项目中添加 Maven 依赖管理：

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
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

然后，在启动类上添加 @EnableDiscoveryClient 注解类：

```
@SpringBootApplication
@EnableDiscoveryClient
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
```

## 1. 单机模式

单机模式下的 Eureka Client 配置如下：

```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

## 2. 集群模式

集群模式下，指定一个或多个服务端地址即可：

```
eureka:
  client:
    serviceUrl:
      defaultZone: http://peer1:8761/eureka/,http://peer2:8761/eureka/,http://peer3:8761/eureka/,http://peer4:8761/eureka/,http://peer5:8761/eureka/
```

同样，修改集群地址的映射：

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

多节点高可用和服务端的配置一样：

```
eureka:
  client:
    region: cn-shanghai-1
    serviceUrl:
      zone1: http://zone1:8761/eureka/
      zone2: http://zone2:8761/eureka/
    availability-zones:
      cn-shanghai-1: zone1,zone2
```

也可以像集群方式一样（但不推荐）：

```
eureka:
  client:
    serviceUrl:
      defaultZone: http://zone1:8761/eureka/,http://zone2:8761/eureka/
```
