---
title: 一文彻底理解 Google 依赖注入(DI) 框架 Guice
categories: java
tags: java
abbrlink: 85df51f2
date: 2025-10-25 19:55:50
---

Guice是Google开发的轻量级依赖注入(DI)框架，遵循JSR-330标准，通过@Inject等注解实现依赖管理。与Spring相比，Guice更轻量、启动更快，适合需要精细控制对象生命周期或对性能敏感的场景。依赖注入通过将对象依赖从外部注入而非内部创建，实现解耦、可测试性和生命周期管理。Guice提供构造函数、字段和方法三种注入方式，并通过Module配置绑定关系。@Provides注解用于复杂对象的创建，而@Inject用于自动构造对象。与Spring自动扫描不同，Guice需要显式声明依赖关系，
<!--more-->

## 什么是Guice
Guice（发音同"juice"）是Google于2007年开源的一款轻量级依赖注入（Dependency Injection，DI）框架。作为Java平台上的IoC容器实现，它通过注解驱动的方式简化了依赖管理，是Spring框架之外的重要选择。通常用在非Spring的项目中，实现依赖注入的功能。

核心特性包括：
1. 基于JSR-330标准实现，使用@Inject等标准注解
2. 采用编译时验证机制，能在启动阶段发现大部分配置错误
3. 相比Spring更轻量，启动速度更快（基准测试显示启动时间可缩短50%以上）

典型应用场景：
- 需要精细控制对象生命周期的应用（如服务器中间件）
- 对启动性能敏感的服务（如命令行工具）

## 什么是DI（依赖注入）

依赖注入就是：

把一个对象需要的依赖（即它所依赖的其他对象），从外部“注入”进去，而不是自己在内部创建。通俗一点讲：
	•	不再由类自己去 new 出它需要的对象；
	•	而是由一个“容器”（比如 Spring 或 Guice）帮你创建这些对象，并自动传给需要它们的类。

举个例子
✅ 没有依赖注入的写法

```java
public class UserService {
    private final UserRepository repo;

    public UserService() {
        this.repo = new UserRepository(); // ❌ 直接在这里 new
    }

    public void createUser(String name) {
        repo.save(name);
    }
}
```
👉 这样写的问题：
	1.	UserService 强依赖具体实现 UserRepository；
	2.	你无法轻易换成别的实现，比如 MockUserRepository；
	3.	测试时不方便（没法注入 mock 对象）；
	4.	对象的生命周期（什么时候创建、销毁）不好统一管理。

那使用依赖注入的写法:

```java
public class UserService {
    private final UserRepository repo;

    // 依赖通过构造函数注入
    @Inject
    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}
```

然后在 Guice 或 Spring 里配置：

Guice:

```java
public class MyModule extends AbstractModule {
    @Override
    protected void configure() {
        bind(UserRepository.class).to(PostgresUserRepository.class);
    }
}
```
Spring:

```java
@Service
public class UserService {
    private final UserRepository repo;

    @Autowired
    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}
```

👉 这样容器会自动：
	•	创建 PostgresUserRepository；
	•	创建 UserService；
	•	并把前者注入给后者。

## 为什么需要依赖注入？

*  **解耦** 类不再依赖于具体实现，只依赖接口。
* **可替换性** 可以在不同环境（dev/test/prod）注入不同实现。
* **易测试** 单元测试中可注入 mock 对象。
* **生命周期管理** 容器统一管理对象的创建、销毁、单例/多例。

## 如何进行依赖注入 ?

举个例子

1. 定义一个服务类

```java
public interface AddressService {
    String queryCity();
}

@Singleton
public class BaiduAddressService implements AddressService {
    @Override
    public String queryCity() {
        return "百度地图";
    }
}

@Singleton
public class GaodeAddressService implements AddressService {
    @Override
    public String queryCity() {
        return "高德地图";
    }
}
```

2. 创建依赖使用者（Consumer）

```java
@Singleton
public class UserService {

    private final AddressService addressService;

    @Inject
    public UserService(@Named("gaodeAddressService") AddressService addressService) {
        this.addressService = addressService;
    }

    public void queryUser() {
        System.out.println(addressService.queryCity());
    }
}
```

💬 解释：

 -  @Inject 表示构造函数由 Guice 自动注入
 - @Named("gaodeAddressService")，用于区分不同实现；
 - 依赖关系通过构造函数明确表达。

3. 配置 Guice 模块（Module）

```java
public class ServiceModule extends AbstractModule {
    @Override
    protected void configure() {
        // 绑定接口到实现
        bind(AddressService.class)
            .annotatedWith(Names.named("gaodeAddressService"))
            .to(GaodeAddressService.class);

        bind(AddressService.class)
            .annotatedWith(Names.named("baiduAddressService"))
            .to(BaiduAddressService.class);
    }

    // 使用 @Provides 提供复杂对象
    @Provides
    @Singleton
    public DataSource provideDataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:postgresql://localhost:5432/testdb");
        config.setUsername("test");
        config.setPassword("test");
        return new HikariDataSource(config);
    }
}
```

💬 解释：
	•	bind(...).to(...)：声明接口与实现的映射；
	•	@Provides：当类无法直接用 @Inject 创建时，手动提供一个“工厂方法”；就是不能通过构造函数这种方式创建对象时。
	•	@Singleton：让对象全局只创建一次。

4. 启动并获取对象

```java
public class Client {
    public static void main(String[] args) {
        Injector injector = Guice.createInjector(new ServiceModule());

        UserService userService = injector.getInstance(UserService.class);
        userService.queryUser(); // 输出: 高德地图
    }
}
```

 - Guice.createInjector(...) 创建依赖注入容器； 
 - injector.getInstance(...)
 - 从容器中获取实例; 
 - 所有依赖（UserService → AddressService）自动注入完成

## 依赖注入的方式


| 注入方式 | 代码示例 | 优点 | 缺点 |
| --- | --- | --- | --- |
| ✅ 构造函数注入 | `@Inject public UserService(AddressService a)` | 依赖明确，不可变 | 依赖较多时构造函数冗长 |
| ⚙️ 字段注入 | `@Inject private AddressService a;` | 代码简洁 | 隐藏依赖关系，难以测试 |
| 🧩 方法注入 | `@Inject void setAddressService(AddressService a)` | 适合可选依赖或循环依赖 | 不够直观，延迟初始化时使用 |

## @Provides 与 @Inject 的区别


| 特性 | `@Inject` | `@Provides` |
| --- | --- | --- |
| **用法** | 在构造函数或字段上标注 | 在 Module 中定义方法 |
| **目的** | 让 Guice 自动构造对象 | 手动定义复杂对象的创建逻辑 |
| **适用场景** | 类能被直接构造 | 类构造逻辑复杂、或需外部依赖 |

## Guice和Spring的对比


| 特性 | Guice | Spring |
| --- | --- | --- |
| **注解** | `@Inject`, `@Provides`, `@Singleton` | `@Autowired`, `@Service`, `@Component` |
| **配置方式** | 手动声明 Module | 自动扫描 classpath |
| **优点** | 轻量、启动快、显式依赖 | 自动化、生态完善 |
| **典型场景** | 微服务、工具、内部服务 | 企业应用、大型项目 |



| 功能 | Spring Boot 注解 | Guice 注解 | 说明 |
| --- | --- | --- | --- |
| 声明依赖注入点 | `@Autowired` | `@Inject` | Guice 遵循 JSR-330 标准，与 Spring 的 `@Autowired` 类似。 |
| 声明组件/服务 | `@Component`, `@Service`, `@Repository`, `@Controller` | 无对应注解（需在 Module 中绑定） | Spring 自动扫描这些 Bean；Guice 需要显式在 Module 里 `bind()`。 |
| 构造函数注入 | `@Autowired` 放在构造函数上 | `@Inject` 放在构造函数上 | 两者都支持构造函数注入。 |
| 提供自定义实例 | `@Bean`（在 `@Configuration` 类中） | `@Provides`（在 `AbstractModule` 中） | 两者都用于手动创建并返回依赖对象。 |
| 限定注入（多实现选择） | `@Qualifier("beanName")` | `@Named("name")` 或自定义 `@BindingAnnotation` | 都可用于在多个实现中指定要注入哪一个。 |
| 生命周期管理 | `@PostConstruct`, `@PreDestroy` | 无内置支持（需手动管理） | Spring 自动管理 Bean 生命周期；Guice 更加简单直接。 |
| 配置类声明 | `@Configuration` | `extends AbstractModule` | Spring 用配置类声明 Bean；Guice 用模块绑定依赖。 |




希望看完本文后，你对Guice有一个彻底的理解，并且能够顺利应用到项目中。

