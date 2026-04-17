# AGENTS.md

本文件是AI Coding在本仓库开发的唯一最高准则，优先级高于所有通用开发规范与行业惯例，未明确约定的内容优先遵循rules/目录下的细分规范。

---

## 技术栈

- **框架**: Spring Boot 2.3.12.RELEASE + Spring Cloud Netflix Eureka
- **数据库**: MySQL 5.1.49 + MyBatis 2.3.0 + Druid 1.1.24
- **缓存**: Redis + Redisson 3.23.3
- **监控**: Micrometer 1.7.2 + Actuator + Log4j2 2.20.0
- **加密**: Jasypt + KMS

---

## 规范索引

细分规范通过glob路径自动按需加载，无需手动触发：
- `MEMORY.md`：全场景始终加载，包含项目历史决策、业务背景、线上踩坑记录
- `rules/项目通用约束.md`：匹配`**/*.java`/`**/*.properties`/`**/*.xml`/`**/*.ftl`，全项目基础约束
- `rules/项目编码规范.md`：匹配上述路径，定义编码风格、命名、注释、分层编码标准
- `rules/单元测试规范.md`：匹配`**/*Test.java`，单测编写全规则
- `rules/代码提交规范.md`：按需加载，定义提交格式、分支管理、合入流程
- `rules/代码安全规范.md`：按需加载，包含安全开发、敏感信息处理、权限校验规则
- `rules/代码性能规范.md`：按需加载，包含数据库、缓存、接口性能优化标准

---

## 项目结构

```
src/main/java/com/yourcompany/project
├── annotation/                 # 自定义注解类
│   ├── PersistenceCryptoClazz.java
│   ├── PersistenceCryptoField.java
├── common/                     # 通用常量和工具类
│   ├── GeneralExceptionMessage.java
│   ├── GlobalLanguageConstants.java
│   ├── PageRequest.java
│   └── PageResult.java
├── configuration/              # Spring配置类
│   └── SpringBootConfig.java
├── domain/                     # 核心业务领域层
│   ├── subdomain/              # 业务领域1
│   │   ├── XXXModule.java
│   │   └── ...
│   ├── subdomain/              # 业务领域2
│   │   ├── XXXModule.java
│   │   └── ...
│   ├── subdomain/              # 业务领域3
│   │   ├── XXXModule.java
│   │   └── ...
│   └── audit/                  # 操作审计模块
├── persistence/                # 数据持久化层
│   ├── entity/                 # 数据实体类
│   │   ├── XXXEntity.java
│   │   └── ...
│   ├── enums/                  # 枚举类型
│   │   ├── XXXTypeEnum.java
│   │   └── ...
│   └── mapper/                 # MyBatis Mapper接口
├── remote/                     # 远程调用相关
├── schedule/                   # 定时任务相关
├── security/                   # 安全认证相关
├── utils/                      # 工具类
├── validation/                 # 参数校验相关
├── web/                        # Web层
│   ├── controllers/            # 控制器类
│   │   ├── XXXController.java
│   │   └── ...
│   └── context/                # 上下文管理
└── Application.java            # 应用启动类

src/main/resources/
├── config/                     # 配置文件
│   ├── application.properties
│   ├── application-dev.properties
│   └── ...
├── i18n/                       # 国际化资源文件
├── mybatis/                    # MyBatis配置
└── aspect.xml                  # AOP切面配置
```

---

## 核心分层与职责边界

严格遵循分层架构，**禁止越层编写代码**：
- **web/controller层**：仅处理HTTP请求、参数校验、结果封装，禁止编写业务逻辑，业务处理统一委托domain层
- **domain层**：项目唯一业务逻辑存放处，包含档案读取/管理/写入/审计四大核心模块，负责业务规则与流程编排
- **persistence层**：仅负责数据读写，禁止编写业务逻辑，保证数据操作与业务规则解耦
- **remote层**：统一封装外部服务远程调用，禁止业务代码直接编写远程调用逻辑
- **utils层**：仅存放无状态、无业务耦合的通用工具，禁止编写业务逻辑
- **禁止跨层调用**：所有的Mapper代码只能在对应的Module里进行调用，禁止跨Module调用其他领域的Mapper代码

---

## 构建命令

```
# 远端打包构建（远端流水线）
./build.sh

# 本地打包构建（本地命令行）
mvn clean package -Dmaven.test.skip=true

# 执行单元测试
mvn test

# 代码规范检查
mvn validate
```

---

## 协作原则

**以第一性原理，从原始需求和问题本质出发，不从惯例或模板出发**

1. 不要假设用户清楚自己想要什么，动机或目标不清晰时，停下来讨论
2. 目标清晰但路径不是最短的，直接告诉用户并建议更好的办法
3. 遇到问题追根因，不打补丁，每个决策都要能回答"为什么"
4. 输出说重点，砍掉一切不改变决策的信息

---

## 强制规则

### 版本与安全
- **禁止随意升级依赖版本**：核心框架依赖版本应该避免进行升级，其他依赖项如何确需升级，升级后需完整回归测试
- **禁止随意添加外部依赖**：禁止随意添加新的外部依赖，如果确实需要添加，需要提供方案选型说明并进行二次确认
- 敏感配置使用 Jasypt + MKS 进行加密存储，包括但不限于数据库、Redis、Password、Key等敏感信息

### 架构与编码
- 严格遵循分层职责，禁止controller写业务逻辑、persistence层写业务规则
- 禁止使用JDK8以上新特性，禁止硬编码魔法值与业务文案，所有文案走国际化配置
- 单个方法控制在80行内，类职责单一，禁止重复造轮子，复用项目现有通用能力

### 数据与中间件
- SQL统一写在MyBatis XML中，禁止注解SQL；禁止无where条件的全表更新/删除、无排序的分页查询
- Redis Key必须加`afs:`业务前缀并设置过期时间，分布式锁必须用Redisson实现
- MQ消费必须做幂等处理，文件操作必须走项目统一封装，禁止直接调用底层SDK

### 接口与测试
- 接口遵循RESTful规范，入参必须做合法性校验，禁止修改已上线接口的入参出参结构，保证向下兼容
- 核心业务必须编写单元测试，单测必须隔离外部依赖、有明确断言，合入前必须全量通过

### 红线禁止行为
- 禁止编写越层、强耦合、无注释的冗余代码，禁止提交临时代码、注释掉的无效代码
- 禁止无回滚方案的破坏性变更、数据库表结构变更
- 禁止在代码中泄露密钥、密码、内部地址等敏感信息
- 禁止修改项目核心架构与目录结构，确需调整必须先完成方案评审
