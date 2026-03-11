---
layout: post
title: Java Spring Boot框架
categories: [Java]
---

# Spring Boot 核心 MVP 开发知识手册

## 一、 项目结构与核心文件

### 1. Maven 配置文件 (`pom.xml`)
项目的“说明书”，管理所有依赖的第三方库。
*   **作用**：定义项目坐标、引入 Spring Boot 父项目、添加具体功能依赖（Web, JPA, H2, Test）。
*   **关键标签**：
    *   `<parent>`：继承 Spring Boot 的默认配置（版本管理、插件管理等）。
    *   `<dependencies>`：存放项目需要的 jar 包。
    *   `<dependencyManagement>`：管理依赖版本（通常由父 POM 处理）。

### 2. 配置文件 (`src/main/resources/application.properties`)
项目的“设置中心”，用于覆盖 Spring Boot 的默认配置。
*   **常见配置**：
    *   `server.port=8080`：修改端口号。
    *   `spring.datasource.url`：数据库连接地址。
    *   `spring.jpa.hibernate.ddl-auto=update`：启动时自动更新数据库表结构（生产环境慎用）。

### 3. 启动类 (`*Application.java`)
项目的“入口”，包含 `main` 方法。
*   **注解**：`@SpringBootApplication`
    *   这是一个组合注解，包含了：
        *   `@Configuration`：标记为配置类。
        *   `@EnableAutoConfiguration`：开启自动配置（根据依赖自动装配 Bean）。
        *   `@ComponentScan`：自动扫描当前包及子包下的组件。

---

## 二、 核心注解

Spring Boot 的核心思想是“约定优于配置”，大量使用注解来简化开发。

### 1. 实体层 注解
用于定义数据库表的结构。

| 注解 | 位置 | 作用 |
| :--- | :--- | :--- |
| `@Entity` | 类名 | 标记该类是一个 JPA 实体，对应数据库中的一张表。 |
| `@Table(name="xxx")` | 类名 | 指定表名（可选，默认类名即表名）。 |
| `@Id` | 字段 | 标记主键。 |
| `@GeneratedValue` | 字段 | 主键生成策略（如自增）。 |
| `@Column` | 字段 | 详细定义字段属性（如长度、是否为空、唯一性等）。 |

### 2. 持久层 注解
用于操作数据库，无需写 SQL 实现类。

| 注解 | 位置 | 作用 |
| :--- | :--- | :--- |
| `@Repository` (可选) | 接口名 | 标记为持久层组件（Spring Data JPA 的接口通常不需要此注解也能被扫描）。 |
| `extends JpaRepository` | 接口 | 继承后立马拥有增删改查功能（save, findAll, findById, delete 等）。 |

### 3. 控制层 注解
用于处理 HTTP 请求，返回数据。

| 注解 | 位置 | 作用 |
| :--- | :--- | :--- |
| `@RestController` | 类名 | 组合注解 = `@Controller` + `@ResponseBody`。表示该类处理 Web 请求，且返回 JSON 数据而非页面。 |
| `@RequestMapping` | 类名/方法 | 定义基础 URL 路径。 |
| `@GetMapping` | 方法 | 映射 HTTP GET 请求（如查询）。 |
| `@PostMapping` | 方法 | 映射 HTTP POST 请求（如新增）。 |
| `@PutMapping` | 方法 | 映射 HTTP PUT 请求（如更新）。 |
| `@DeleteMapping` | 方法 | 映射 HTTP DELETE 请求（如删除）。 |
| `@PathVariable` | 方法参数 | 获取 URL 路径中的参数（如 `/books/{id}`）。 |
| `@RequestBody` | 方法参数 | 将请求体中的 JSON 数据自动转换为 Java 对象。 |

---

## 三、 核心机制与概念

### 1. IoC (控制反转) 与 DI (依赖注入)
*   **概念**：不用 `new` 关键字创建对象，而是把对象的创建和管理交给 Spring 容器。需要使用时，Spring 自动把对象“注入”进来。
*   **实践**：在 Controller 中使用 `private BookRepository repository;` 并不需要手动实例化，Spring 会自动注入一个实现类。

### 2. 自动配置
*   **原理**：Spring Boot 启动时会扫描 `classpath` 下的类。
    *   发现了 `spring-boot-starter-web` -> 自动配置 Tomcat 和 Spring MVC。
    *   发现了 `spring-boot-starter-data-jpa` 和 `H2` -> 自动配置数据源和 Hibernate。
*   **日志体现**：启动日志中的 `ConditionEvaluationReport` 就是在判断哪些自动配置生效了。

### 3. JPA (Java Persistence API)
*   **ORM (对象关系映射)**：将 Java 对象映射到数据库表。
*   **Hibernate**：JPA 的标准实现。我们在 `application.properties` 中设置的 `ddl-auto=update` 就是利用 Hibernate 自动帮我们在数据库建表。

### 4. RESTful API 设计
一种风格，不是技术。
*   **GET** `/api/books`：获取列表。
*   **GET** `/api/books/1`：获取 ID 为 1 的书。
*   **POST** `/api/books`：新增一本书（数据在 Body 中）。
*   **PUT** `/api/books/1`：更新 ID 为 1 的书。
*   **DELETE** `/api/books/1`：删除 ID 为 1 的书。

---
