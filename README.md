# muyie-registry

本项目基于 Spring Cloud Netflix Eureka 实现服务注册和发现，支持单机模式和集群模式。

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现。Eureka 采用了 C-S 的设计架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka Client 连接到 Eureka Server，并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。Spring Cloud 的一些其他模块（比如Zuul）就可以通过 Eureka Server 来发现系统中的其他微服务，并执行相关的逻辑。

Eureka 由两个组件组成：Eureka Server 和 Eureka Client。Eureka Server 用作服务注册服务器。Eureka Client 用来简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持。Netflix 在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。

- Release Train Version: Hoxton.SR3
- Supported Boot Version: 2.2.5.RELEASE

https://cloud.spring.io/spring-cloud-static/Hoxton.SR3/reference/html/spring-cloud.html