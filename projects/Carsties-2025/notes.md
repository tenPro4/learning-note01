Repo: https://github.com/TryCatchLearn/Carsties-2025

## Section 2

### Dotnet migrations
dotnet tool list -g

```sh
# check if dotnet-ef is install

# install if dont
dotnet tool install dotnet-ef -g
# or update
dotnet tool update dotnet-ef -g

# create migration file
dotnet ef migrations add "InitialCreate" -o Data/Migrations

# execute migration
dotnet ef database update
```

### Create gitignore for dotnet

```sh
dotnet new gitignore
```

## Section 4: RabbitMq

### 总体结构

```yaml
Producer
   |
   v
Exchange
   |
   v
Queue
   |
   v
Consumer
```

**🔑 核心原则**: 
1. Producer 和 Consumer 完全不知道对方是谁
2. Exchange 决定消息要送到哪些 Queue。Producer 不直接把消息丢进 Queue。
3. Binding = Exchange 和 Queue 的“规则”
4. 

### Exchange 的 3 种常见类型

**1. Direct Exchange**
📌 按 routing key 精确匹配
```java
routing key = "order.created"
```
只有绑定了同样 key 的 Queue 才会收到。

**2. Fanout Exchange（广播）**
📌 无视 routing key，全部转发

```rust
Producer -> Exchange -> 所有 Queue
```

**3. Topic Exchange**
📌 支持 通配符

```rust
order.*
order.created
order.#
```

### 为什么不用HTTP，而是使用RabbitMQ

让我们看以下分别是HTTP和RabbitMQ的例子:

```csharp
 var items = await httpClient.GetAsync("/api/service1");
```

![s4_1](/assets/s4_1.png)

根据上图**HTTP**的沟通方式，能得到几个点：
1. 传输是同步的，请求方需要等待答复方
2. 沟通的对象必须在线，否则无法顺利请求回复
3. 当某微服务被多个用户高并发请求时，响应速度将会降低或者直接显示未知错误

下面则是总体构图
![s4_2](/assets/s4_2.png)
1. Producer负责发送消息，consumer负责消耗消息。
2. 由于Producer和Consumer不是直接对接的，所以这是异步传输。
3. 哪怕Consumer因为某些原因暂时下线，只要消息还在queue中，那么当consumer重新上线时，依然可以继续消耗积累的消息。

### 实践:安装需求

1. 安装RabbitMq
可通过docker进行安装

2. C# Package
安装MassTransit系列的package。

好处是，它除了支持 `RabbitMQ` ，还支持其他类似的服务，如 `Azure Service Bus`, `ActiveMQ`, `Kafka` 等等。即是说，哪怕途中被迫换其他服务，大多数代码依然可以保持不变。

安装package的后缀根据服务而不同：
MassTransit.**RabbitMQ**
MassTransit.**ActiveMQ**
MassTransit.*

### 实践：代码设置

**Consumer 方**
```csharp title="Program.cs"
builder.Services.AddMassTransit(x =>
{
    x.AddConsumersFromNamespaceContaining<ItemCreatedConsumer>();

   // kebab case: item-xxx-xxx
   // "item" can be replace by module name
   // for this case, the exchange name will be: item-item-created
    x.SetEndpointNameFormatter(new KebabCaseEndpointNameFormatter("item", false));

    x.UsingRabbitMq((context, cfg) =>
    {
        cfg.ConfigureEndpoints(context);
    });
});
```

**Producer 方**
```csharp title="Program.cs"
builder.Services.AddMassTransit(x =>
{
    x.UsingRabbitMq((context, cfg) =>
    {
        cfg.ConfigureEndpoints(context);
    });
});
```

```csharp title="ItemCreatedConsumer.cs"
public class ItemCreatedConsumer(IMapper mapper) : IConsumer<ItemCreated>
{
    public async Task Consume(ConsumeContext<ItemCreated> context)
    {
        Console.WriteLine($"ItemCreatedConsumer: {context.Message.Id}");

        var item = mapper.Map<Item>(context.Message);

        // do something
    }
}
```

> Consumer 类的名字后缀必须是`Consumer`

**Publisher 例子**
```csharp title="OtherMicroServiceController.cs"
public class OtherMicroServiceController:ControlBase
{
   private readonly IPublishEndpoint _publishEndpoint;

   public OtherMicroServiceController(IPublishEndpoint publishEndpoint){
      _publishEndpoint = publishEndpoint;
   }

   [HttpGet]
   public async Task<ActionResult> Test1()
   {
      //...
      var itemCreated = new ItemCreated();
      await _publishEndpoint.Publish(itemCreated);
      //...
   }
}
```

### 实践：Outbox -- 处理RabbitMQ下线的情况

使用RabbitMQ会有一个问题，那就是producer和consumer方因为中间人(也即是RabbitMQ service bus)的下线，导致双方数据处理上无法达成一致。

```css
T0 producer SaveChanges()
T1 producer Publish() ❌（bus down）
```

结果：
- DB：✅ 已提交
- Message：❌ 没进 queue
- Consumer：❌ 永远不知道
- 系统状态：💥 数据不一致

**Outbox**是这场景的处理方式：把要发到 Service Bus / RabbitMQ的消息先存到数据库，然后由后台任务每 x 秒尝试一次发送。

**时间线示例**
```
T0  : Save Order + Publish OrderCreated
T0  : Service Bus down ❌
T0  : OutboxMessage 写入数据库 ✅

T+10s : 第一次重试 ❌
T+20s : 第二次重试 ❌
T+35s : Service Bus 恢复 ✅
T+40s : 消息成功发送 🎉
```

**代码配置 (Producer)**
```csharp
builder.Services.AddMassTransit(x =>
{
   // 加Outbox后台服务
   x.AddEntityFrameworkOutbox<YourDbContext>(o => 
    {
        o.QueryDelay = TimeSpan.FromSeconds(10);

        o.UsePostgres();
        o.UseBusOutbox();
    });

    x.UsingRabbitMq((context, cfg) =>
    {
        cfg.ConfigureEndpoints(context);
    });
});
```

```csharp title="YourDbContext.cs"
protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.AddInboxStateEntity();
        modelBuilder.AddOutboxMessageEntity();
        modelBuilder.AddOutboxStateEntity();
    }
```

### 实践：处理Consumer方数据库下线的情况

MassTransit内部有提供retry的功能，类似`polly`。

Retry policy分为两类：`总体性`或`个例性`

**个例性**
```csharp title="Program.cs"
builder.Services.AddMassTransit(x =>
{
    x.AddConsumersFromNamespaceContaining<ItemCreatedConsumer>();
    x.SetEndpointNameFormatter(new KebabCaseEndpointNameFormatter("item", false));

    x.UsingRabbitMq((context, cfg) =>
    {
      cfg.ReceiveEndpoint("item-item-created",e =>{
         e.UseMessageRetry(r => r.Interval(5,5)); //每隔五秒尝试一次

         e.ConfigureConsumer<ItemCreatedConsumer>(context);
      });

      cfg.ConfigureEndpoints(context);
    });
});
```

如果你需要进一步手动处理，你可以定义一个`fault`类。
```csharp title='ItemCreatedFaultConsumer.cs'
public class ItemCreatedFaultConsumer : IConsumer<Fault<ItemCreated>>
{
    public async Task Consume(ConsumeContext<Fault<ItemCreated>> context)
    {
        Console.WriteLine("--> Consuming faulty creation");

        var exception = context.Message.Exceptions.First();

        if (exception.ExceptionType == "System.ArgumentException")
        {
            // example here i try to override isDeleted value to true
            // and retry publish
            context.Message.Message.IsDeleted = true;
            await context.Publish(context.Message.Message);
        }
        else
        {
            Console.WriteLine($"--> Exception: Update error dashboard somewhere");
        }
    }
}

```
图像流程如下
![s4_3](/assets/s4_3.png)

从中能看出几点：
1. Microservice可以同时是producer和consumer
2. RabbitMQ具备重复尝试写入的机制

除此之外，消息的消耗是可以被多个一个消费者消费。

例如，生产者发布消息1，而消费者1和消费者2能则各自根据自己的逻辑消费消息1.

![s4_4](/assets/s4_4.png)


## Section 5: IdentityServer

![s5_1](/assets/s5_1.png)

[References](https://christianlydemann.com/creating-an-openid-connect-system-with-angular-8-and-identityserver4-oidc-part-1/)


## Section 6: Gateway service

Package use: Yarp.ReverseProxy

Benefit:
- Very typical in Micro Services
- Single surface area for requests
- Client unaware of any internal services
- Security
- SSL Termination
- URL Rewriting
- Load Balancing
- Caching

## Section 7: Docker

Environment Key Pattern in Dockerfile and appsetting

The following environment key are equally on appsetting and Dockerfile

```json
"RabbitMq": {
    "Host": "localhost"
  }
```

```yaml
 environment:
      - RabbitMQ__Host=rabbitmq
```


ASP.NET Core configuration rules:
| Rule                     | Explanation                                  |
| ------------------------ | -------------------------------------------- |
| `__` (double underscore) | Maps to `:` (nested section)                 |
| Case-insensitive         | `RabbitMQ`, `RabbitMq`, `rabbitmq` all match |
| Last provider wins       | Env vars override JSON                       |

```
RabbitMQ__Host  →  RabbitMq:Host
```

`depends_on` no enough to make sure dependencies services is working. 

Use `health check` feature by adding `condition` to improve the reliability. [Reference](https://github.com/rodrigobdz/docker-compose-healthchecks)

```yml
postgres:
    image: postgres:latest
    environment:
      POSTGRES_PASSWORD: postgrespw
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck: #This is need when use condition
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

depends_on:
      postgres:
        condition: service_healthy
```

Only build specific service inside the docker-compose file
```shell
docker compose build service1
```