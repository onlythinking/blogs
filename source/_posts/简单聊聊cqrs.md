---
title: 简单聊聊CQRS
date: 2020-05-17 18:06:48
tags:
	- axon
	- cqrs
categories: 框架
copyright: true
top: 1
typora-root-url: ../
typora-copy-images-to: ../images
---



## 序言

- Domain Driven Design (DDD) 
- CQRS
- Axonframework
- Mvc vs CQRS
- 参考

## Domain driver design

​	**领域驱动设计** 也就是我们在设计系统时的一种模式。领域是个很宽泛的概念，比如银行领域，消费领域等，可大可小。驱动也意味推动，也就是用领域方面的知识来推动软件的设计。（那么也可以有产品驱动设计，测试驱动设计（TDD），也就是产品测试催着开发办事么？哈哈，这里是我瞎掰！）

​	

## CQRS (命令查询责任隔离)

CQRS 属于DDD应用领域的一个模式。

 设计架构将应用程序分为两部分：命令端和查询端。命令端负责创建，更新和删除请求，并在数据更改时发出事件。查询端通过执行查询来处理，查询的这些数据通过订阅数据更改时发出的事件流而保持最新。

![00_ddd_](/images/00_ddd_.png)



## Axonframework

Axon Framework 是一个严格按照CQRS模式实现的java框架。

> [Axon Framework](https://github.com/AxonFramework/AxonFramework)



## Mvc vs CQRS

下面以创建订单为例子

1. 用户请求创建订单；
2. 接受创建请求后，命令总线分发命令请求，订单聚合加载对象历史事件，并执行相应业务逻辑；
3. 订单聚合对象处理成功后，产生事件，事件发送到事件总线和存储到事件仓库；
4. 事件总线分发到订阅的组件进行处理，存储数据库，发送邮件；
5. 用户通过API直接查询存储的订单数据。



![0_ddd_](/images/0_ddd_.png)



1. 用户请求创建订单；
2. 接受创建请求后，服务层执行业务逻辑处理。

![1_ddd_](/images/1_ddd_.png)

代码例子

订单Controller

```java
@RestController
public class OrderCommandController {

  @Autowired
  private CommandGateway commandGateway;

  @PostMapping("/create")
  public void create(@Valid @RequestBody OrderDto dto) {
    CreateOrderCommand command = new CreateOrderCommand(dto.getOrderNo());
    commandGateway.sendAndWait(command);
  }
}
```



订单聚合对象

```java
@Aggregate
@Data
@NoArgsConstructor
public class OrderAggregate {

  @NotNull
  @AggregateIdentifier
  private String id;
  private String orderNo;

  @CommandHandler
  public UsersAggregate(CreateOrderCommand command) {
    apply(new OrderCreatedEvent(command.getId(), command.getOrderNo()));
  }

  @EventSourcingHandler
  public void on(OrderCreatedEvent event) {
    this.id = event.getId();
    this.orderNo = event.getOrderNo();
  }

}
```

事件处理器

```java
@Slf4j
@Component
@ProcessingGroup("order")
public class OrderEventHandler {

  @Autowired
  private orderRepository repository;

  @EventHandler
  public void handle(final OrderCreatedEvent event, @Timestamp Instant timestamp, @SequenceNumber Long version) {
    log.info("OrderCreatedEvent: [{}] ", event.getId());
    Order order = new Order();
    order.setId(event.getId().getValue());
    order.setOrderNo(event.getOrderNo());
    order.setAggregateVersion(version);

    order.setCreatedDate(timestamp);
    order.setLastModifiedDate(timestamp);
    repository.save(users);
  }
}

```



【微服务版本】查看以下例子

> Micro service with spring cloud and the AxonFramework
>
>  https://github.com/onlythinking/micro-starter

![](/images/architecture-01-9721894.png)



## 参考

- https://github.com/AxonFramework/AxonFramework
- https://microservices.io/patterns/data/cqrs.html
- https://www.jdon.com/cqrs.html
- https://microservices.io/patterns/data/cqrs.html