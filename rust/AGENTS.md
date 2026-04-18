# AGENTS.md

本文件是AI Coding在本仓库开发的唯一最高准则，优先级高于所有通用开发规范与行业惯例，未明确约定的内容优先遵循 `rules/` 目录下的细分规范。

---

## 技术栈

### 后端技术栈
- **Rust**: 1.75+ (稳定版)
- **Web框架**: Axum 0.7+ (基于Tokio的异步Web框架)
- **异步运行时**: Tokio 1.35+ (带完整功能)
- **数据库**: PostgreSQL 15+ + SQLx 0.7+ (异步，编译时检查)
- **ORM**: SeaORM 0.12+ (异步ORM，基于SQLx)
- **序列化**: Serde 1.0+ + serde_json 1.0+
- **配置管理**: config-rs 0.14+ + dotenvy 0.15+
- **日志**: tracing 0.1+ + tracing-subscriber 0.3+
- **验证**: validator 0.18+ (基于Serde)
- **加密**: ring 0.17+ 或 RustCrypto生态系统
- **缓存**: Redis + redis-rs 0.23+
- **测试**: 内置test框架 + criterion 0.5+ (基准测试) + mockall 0.12+
- **HTTP客户端**: reqwest 0.11+ (带tls和json支持)
- **API文档**: utoipa 4.0+ (OpenAPI生成)

### 前端技术栈 (如项目需要)
- **框架**: Leptos 0.6+ (Rust全栈框架) 或 传统前端技术栈
- **说明**: 如使用Leptos，前后端代码可统一在Rust中编写

---

## 规范索引

细分规范通过glob路径自动按需加载，无需手动触发：
- `MEMORY.md`：全场景始终加载，包含项目历史决策、业务背景、线上踩坑记录
- `rules/项目编码规范.md`：匹配 `**/*.rs`、`**/*.toml`、`**/*.json`，全项目基础约束
- `rules/单元测试规范.md`：匹配 `**/*.rs` 中的测试模块，单测编写全规则
- `rules/代码提交规范.md`：按需加载，定义提交格式、分支管理、合入流程
- `rules/代码安全规范.md`：按需加载，包含安全开发、敏感信息处理、权限校验规则
- `rules/代码性能规范.md`：按需加载，包含数据库、缓存、接口性能优化标准
- `rules/异步编程规范.md`：按需加载，包含Rust异步编程最佳实践

---

## 项目结构

```
src/
├── main.rs                          # 应用启动入口
├── lib.rs                           # 库定义
├── config/                          # 配置管理
│   ├── mod.rs
│   ├── settings.rs                  # 应用配置
│   └── database.rs                  # 数据库配置
├── api/                             # API层 (handlers)
│   ├── mod.rs
│   ├── v1/                          # API版本1
│   │   ├── mod.rs
│   │   ├── users.rs                 # 用户相关处理器
│   │   └── ...
│   └── middleware/                  # 中间件
│       ├── mod.rs
│       ├── auth.rs                  # 认证中间件
│       ├── logging.rs               # 日志中间件
│       └── error.rs                 # 错误处理中间件
├── domain/                          # 业务领域层
│   ├── mod.rs
│   ├── users/                       # 用户领域
│   │   ├── mod.rs
│   │   ├── model.rs                 # 领域模型
│   │   ├── repository.rs            # 仓储接口
│   │   ├── service.rs               # 领域服务
│   │   └── dto.rs                   # 数据传输对象
│   └── ...
├── infrastructure/                  # 基础设施层
│   ├── mod.rs
│   ├── database/                    # 数据库
│   │   ├── mod.rs
│   │   ├── connection.rs            # 数据库连接
│   │   ├── migrations/              # 数据库迁移
│   │   └── repository/              # 仓储实现
│   │       ├── mod.rs
│   │       ├── users.rs             # 用户仓储实现
│   │       └── ...
│   ├── cache/                       # 缓存
│   │   ├── mod.rs
│   │   └── redis_client.rs
│   └── external/                    # 外部服务
│       ├── mod.rs
│       └── ...
├── application/                     # 应用服务层
│   ├── mod.rs
│   ├── error.rs                     # 应用错误定义
│   └── state.rs                     # 应用状态
├── utils/                           # 工具类
│   ├── mod.rs
│   ├── crypto.rs                    # 加密工具
│   ├── validation.rs                # 验证工具
│   └── logging.rs                   # 日志工具
└── shared/                          # 共享模块
    ├── mod.rs
    ├── types.rs                     # 共享类型
    └── constants.rs                 # 共享常量

tests/                               # 测试目录
├── integration/                     # 集成测试
│   ├── mod.rs
│   ├── api/
│   └── ...
└── unit/                            # 单元测试
    ├── mod.rs
    ├── domain/
    └── ...

migrations/                          # 数据库迁移 (SeaORM)
├── README.md
└── *.sql

Cargo.toml                           # 项目配置
Cargo.lock                           # 依赖锁文件
.env.example                         # 环境变量示例
.dockerignore                        # Docker忽略文件
Dockerfile                           # Docker构建文件
docker-compose.yml                   # Docker Compose配置
```

---

## 核心分层与职责边界

严格遵循分层架构，**禁止越层编写代码**：

### 后端分层
- **api层 (handlers)**：仅处理HTTP请求、参数校验、结果封装，禁止编写业务逻辑，业务处理统一委托application层
- **application层**：应用服务层，协调领域服务和基础设施，处理用例和事务
- **domain层**：项目核心业务逻辑存放处，包含业务实体、值对象、领域服务、仓储接口
- **infrastructure层**：仅负责数据读写和外部服务调用实现，禁止编写业务逻辑
- **utils层**：仅存放无状态、无业务耦合的通用工具，禁止编写业务逻辑
- **禁止跨层调用**：所有的仓储实现只能在对应的应用服务里进行调用，禁止跨领域调用其他仓储

### 架构原则
1. **依赖倒置原则**：高层模块不依赖低层模块，两者都依赖抽象
2. **单一职责原则**：每个模块、结构体、函数只负责一个功能
3. **开闭原则**：对扩展开放，对修改关闭
4. **明确错误处理**：使用Result类型明确处理所有可能错误

---

## 构建命令

### 开发构建命令
```
# 安装依赖
cargo build

# 代码规范检查
cargo clippy -- -D warnings

# 代码格式化
cargo fmt --all

# 运行单元测试
cargo test

# 运行所有测试（包括集成测试）
cargo test --all

# 生成文档
cargo doc --open

# 启动开发服务器（带热重载）
cargo watch -x run

# 启动开发服务器
cargo run
```

### 生产构建命令
```
# 发布构建（优化）
cargo build --release

# 检查未使用依赖
cargo udeps

# 安全检查
cargo audit

# 基准测试
cargo bench
```

### 数据库相关
```
# 运行数据库迁移
cargo run -- migrate

# 回滚迁移
cargo run -- migrate down

# 生成实体（SeaORM）
sea-orm-cli generate entity -o src/entities
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
- 敏感配置使用环境变量或加密存储，包括但不限于数据库、Redis、密码、密钥等敏感信息
- 必须启用Rust的安全特性，如`#![forbid(unsafe_code)]`除非有充分理由

### 架构与编码
- 严格遵循分层职责，禁止api层写业务逻辑、infrastructure层写业务规则
- 禁止使用不稳定的Rust特性，禁止硬编码魔法值与业务文案
- 单个函数控制在80行内，模块职责单一，禁止重复造轮子，复用项目现有通用能力
- 必须使用Rust 2018或更高版本，充分利用新特性
- 错误处理必须使用Result类型，禁止使用unwrap()或expect()在生产代码中

### 数据与中间件
- 数据库操作统一使用SeaORM或SQLx，禁止原生SQL拼接；禁止无where条件的全表更新/删除
- Redis Key必须加业务前缀并设置过期时间，分布式锁必须用Redlock算法实现
- 消息队列消费必须做幂等处理，文件操作必须走项目统一封装

### 接口与测试
- 接口遵循RESTful规范，入参必须做合法性校验，禁止修改已上线接口的入参出参结构
- 核心业务必须编写单元测试，单测必须隔离外部依赖、有明确断言，合入前必须全量通过
- 必须编写集成测试覆盖API端点
- 必须使用Mock进行外部依赖测试

### 异步编程
- 必须合理使用async/await，避免阻塞运行时
- 必须正确处理竞态条件和死锁
- 必须使用适当的并发原语（Arc, Mutex, RwLock, Channel等）

### 红线禁止行为
- 禁止编写越层、强耦合、无文档注释的冗余代码，禁止提交临时代码、注释掉的无效代码
- 禁止无回滚方案的破坏性变更、数据库表结构变更
- 禁止在代码中泄露密钥、密码、内部地址等敏感信息
- 禁止修改项目核心架构与目录结构，确需调整必须先完成方案评审
- 禁止使用`unsafe`代码除非有充分理由和安全审查