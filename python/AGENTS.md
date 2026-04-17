# AGENTS.md

本文件是AI Coding在本仓库开发的唯一最高准则，优先级高于所有通用开发规范与行业惯例，未明确约定的内容优先遵循 `rules/` 目录下的细分规范。

---

## 技术栈

### 后端技术栈
- **Python**: 3.12+
- **Web框架**: Tornado 6.3+
- **数据库**: MySQL 5.6+ + SQLAlchemy 2.0+ + Alembic
- **缓存**: Redis + redis-py 5.0+
- **数据验证**: Pydantic 2.0+
- **监控**: Prometheus + Grafana + structlog
- **加密**: cryptography
- **测试**: pytest + pytest-mock + pytest-cov + pytest-tornado

### 前端技术栈
- **框架**: Vue 3 + Composition API
- **UI组件库**: Vant 4
- **状态管理**: Pinia
- **语言**: TypeScript
- **构建工具**: Vite
- **包管理**: pnpm
- **说明**: 源代码按分类存放于 `src/web/` 目录中

---

## 规范索引

细分规范通过glob路径自动按需加载，无需手动触发：
- `MEMORY.md`：全场景始终加载，包含项目历史决策、业务背景、线上踩坑记录
- `rules/项目编码规范.md`：匹配 `**/*.py`、`**/*.yml`、`**/*.yaml`、`**/*.toml`、`**/*.ini`，全项目基础约束
- `rules/前端编码规范.md`：匹配 `**/*.ts`、`**/*.vue`、`**/*.yaml`、`**/*.toml`、`**/*.ini`，全项目基础约束
- `rules/单元测试规范.md`：匹配 `**/test_*.py`、`**/*_test.py`，单测编写全规则
- `rules/代码提交规范.md`：按需加载，定义提交格式、分支管理、合入流程
- `rules/代码安全规范.md`：按需加载，包含安全开发、敏感信息处理、权限校验规则
- `rules/代码性能规范.md`：按需加载，包含数据库、缓存、接口性能优化标准

---

## 项目结构

```
src/                                # 项目源代码根目录
├── app/                            # 后端应用目录
│   ├── __init__.py
│   ├── main.py                     # Tornado应用启动入口
│   ├── handlers/                   # Tornado请求处理器
│   │   ├── __init__.py
│   │   ├── base.py                 # 基础处理器
│   │   ├── v1/                     # API版本1
│   │   │   ├── __init__.py
│   │   │   ├── users.py            # 用户相关处理器
│   │   │   └── ...
│   │   └── ...
│   ├── core/                       # 核心配置
│   │   ├── __init__.py
│   │   ├── config.py               # 配置管理
│   │   ├── security.py             # 安全配置
│   │   └── logging.py              # 日志配置
│   ├── domain/                     # 业务领域层
│   │   ├── __init__.py
│   │   ├── users/                  # 用户领域
│   │   │   ├── __init__.py
│   │   │   ├── model.py            # 领域模型
│   │   │   └── repository.py       # 数据库读写
│   │   │   ├── schemas.py          # Pydantic模式
│   │   │   ├── service.py          # 领域服务
│   │   └── ...
│   ├── infrastructure/             # 基础设施层
│   │   ├── __init__.py
│   │   ├── database/               # 数据库
│   │   │   ├── __init__.py
│   │   │   ├── base.py             # 基类
│   │   │   ├── models.py           # SQLAlchemy模型
│   │   │   ├── repositories.py     # 数据库读写
│   │   │   └── session.py          # 会话管理
│   │   ├── cache/                  # 缓存
│   │   │   ├── __init__.py
│   │   │   └── redis_client.py
│   │   └── external/               # 外部服务
│   │       ├── __init__.py
│   │       └── ...
│   ├── utils/                      # 工具类
│   │   ├── __init__.py
│   │   ├── crypto.py               # 加密工具
│   │   └── validators.py           # 验证器
│   └── middleware/                 # 中间件
│       ├── __init__.py
│       ├── auth.py                 # 认证中间件
│       └── logging.py              # 日志中间件
├── tests/                          # 测试目录
│   ├── __init__.py
│   ├── conftest.py                 # pytest配置
│   ├── unit/                       # 单元测试
│   │   ├── __init__.py
│   │   ├── handlers/
│   │   ├── domain/
│   │   └── ...
│   └── integration/                # 集成测试
│       ├── __init__.py
│       └── ...
├── alembic/                        # 数据库迁移
│   ├── versions/
│   ├── env.py
│   └── script.py.mako
├── requirements/                   # 依赖管理
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── web/                            # 前端项目目录
│   ├── main.ts                     # 应用入口
│   ├── App.vue                     # 根组件
│   ├── api/                        # API接口层
│   │   ├── index.ts
│   │   ├── users.ts                # 用户相关API
│   │   └── ...
│   ├── components/                 # 公共组件
│   │   ├── common/                 # 通用组件
│   │   └── business/               # 业务组件
│   ├── views/                      # 页面组件
│   │   ├── Index.vue
│   │   ├── Login.vue
│   │   └── ...
│   ├── stores/                     # Pinia状态管理
│   │   ├── index.ts
│   │   ├── user.ts                 # 用户状态
│   │   └── ...
│   ├── router/                     # 路由配置
│   │   └── index.ts
│   ├── utils/                      # 工具函数
│   │   ├── request.ts              # 请求封装
│   │   ├── validate.ts             # 验证工具
│   │   └── ...
│   ├── types/                      # TypeScript类型定义
│   │   ├── rest.d.ts
│   │   ├── user.d.ts
│   │   └── ...
│   ├── assets/                     # 静态资源
│   │   ├── css/
│   │   ├── images/
│   │   └── ...
│   ├── public/                     # 公共资源
│   ├── index.html                  # HTML入口
│   ├── package.json                # 项目配置
│   ├── tsconfig.json               # TypeScript配置
│   ├── vite.config.ts              # Vite配置
│   └── .env.example                # 环境变量示例
└── .env.example                    # 环境变量示例
```

---

## 核心分层与职责边界

### 后端分层
严格遵循分层架构，**禁止越层编写代码**：
- **handlers层**：仅处理HTTP请求、参数校验、结果封装，禁止编写业务逻辑，业务处理统一委托domain层
- **domain层**：项目唯一业务逻辑存放处，包含业务规则与流程编排
- **infrastructure层**：仅负责数据读写和外部服务调用，禁止编写业务逻辑，保证数据操作与业务规则解耦
- **utils层**：仅存放无状态、无业务耦合的通用工具，禁止编写业务逻辑
- **禁止跨层调用**：所有的Repository代码只能在对应的Service里进行调用，禁止跨Service调用其他领域的Repository代码

### 前端分层
- **views层**：页面组件，负责UI展示和用户交互
- **components层**：可复用组件，禁止包含业务逻辑
- **stores层**：状态管理，使用Pinia管理应用状态
- **api层**：接口封装，统一处理HTTP请求
- **utils层**：工具函数，禁止包含业务逻辑

---

## 构建命令

### 后端构建命令
```
# 安装依赖
pip install -r src/requirements/dev.txt

# 代码规范检查
flake8 src/

# 运行单元测试
pytest src/tests/unit/

# 运行所有测试
pytest src/tests/

# 生成覆盖率报告
pytest --cov=src/app --cov-report=html

# 启动开发服务器
python src/app/main.py --port=8000 --debug
```

### 前端构建命令
```
# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 代码规范检查
pnpm lint

# 类型检查
pnpm type-check

# 构建生产版本
pnpm build

# 预览生产版本
pnpm preview
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
- 敏感配置使用环境变量或加密存储，包括但不限于数据库、Redis、Password、Key等敏感信息

### 架构与编码
- 严格遵循分层职责，禁止handlers写业务逻辑、infrastructure层写业务规则
- 禁止使用Python 3.12以下语法，禁止硬编码魔法值与业务文案，所有文案走配置
- 单个方法控制在80行内，类职责单一，禁止重复造轮子，复用项目现有通用能力
- 前端使用Vue 3 Composition API，禁止使用Options API
- 状态管理必须使用Pinia，禁止使用Vuex或其他状态管理库

### 数据与中间件
- SQL统一使用SQLAlchemy ORM，禁止原生SQL拼接；禁止无where条件的全表更新/删除、无排序的分页查询
- Redis Key必须加业务前缀并设置过期时间，分布式锁必须用redis-py实现
- MQ消费必须做幂等处理，文件操作必须走项目统一封装，禁止直接调用底层SDK

### 接口与测试
- 接口遵循RESTful规范，入参必须做合法性校验，禁止修改已上线接口的入参出参结构，保证向下兼容
- 核心业务必须编写单元测试，单测必须隔离外部依赖、有明确断言，合入前必须全量通过
- 前端组件必须编写单元测试，使用Vitest进行测试
- TypeScript必须开启严格模式，禁止使用any类型

### 红线禁止行为
- 禁止编写越层、强耦合、无注释的冗余代码，禁止提交临时代码、注释掉的无效代码
- 禁止无回滚方案的破坏性变更、数据库表结构变更
- 禁止在代码中泄露密钥、密码、内部地址等敏感信息
- 禁止修改项目核心架构与目录结构，确需调整必须先完成方案评审
