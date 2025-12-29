---
name: init
description: 扫描当前项目的技术栈、目录结构和代码约定，自动生成 before-modify（修改前检查清单）和 project-structure（项目结构索引）两个 skills，帮助 Claude 在后续开发中保持代码风格一致、避免重复实现 (user)
---

# 初始化项目 Skills

为当前项目创建定制化的 Claude Code skills，帮助后续开发保持一致性。

## 执行流程

### 第一步：探测项目信息

自动分析以下内容：

**1. 技术栈探测**

检查文件判断技术栈：
- `pyproject.toml` / `requirements.txt` → Python
- `package.json` → Node.js/前端
- `go.mod` → Go
- `Cargo.toml` → Rust
- `pom.xml` / `build.gradle` → Java

**2. 框架探测**

后端框架：
- FastAPI / Flask / Django (Python)
- Express / NestJS / Koa (Node.js)
- Gin / Echo (Go)

前端框架：
- Vue / React / Svelte
- 检查 `package.json` 的 dependencies

ORM/数据库：
- SQLAlchemy / Prisma / TypeORM / GORM

**3. 目录结构分析**

识别关键目录：
- `src/` / `app/` / `lib/` → 源代码
- `api/` / `routes/` / `routers/` → API 路由
- `models/` / `entities/` / `schemas/` → 数据模型
- `services/` / `repositories/` → 业务逻辑
- `components/` / `views/` / `pages/` → 前端组件
- `tests/` / `__tests__/` → 测试

**4. 约定推断**

从现有代码推断：
- 命名风格：snake_case / camelCase / PascalCase
- API 风格：RESTful / GraphQL
- 分页参数名：page/page_size, offset/limit, cursor
- 响应格式：`{ items, total }` / `{ data, meta }`

**5. 特殊情况处理**

如果遇到以下情况，需要询问用户：
- Monorepo 结构：询问要为哪个子项目生成 skills
- 非标准目录结构：请用户指出关键文件位置
- 多技术栈混合：确认主要技术栈

### 第二步：确认发现

向用户展示探测结果，询问是否准确，格式如下：

```
## 项目探测结果

**技术栈**: Python + Vue 3
**后端框架**: FastAPI + SQLAlchemy
**前端框架**: Vue 3 + TypeScript
**包管理**: uv (后端), pnpm (前端)

**关键文件位置**:
| 功能 | 路径 |
|------|------|
| 数据模型 | src/database/models.py |
| API 路由 | src/api/routers/ |
| 前端类型 | web/src/types/index.ts |

**发现的约定**:
- 表名前缀: gt_
- 分页参数: page, page_size
- 日期格式: YYYY-MM-DD

以上信息是否准确？需要补充或修正吗？
```

### 第三步：生成 Skills

用户确认后，在项目的 `.claude/skills/` 目录下生成两个 skill：

#### 1. `before-modify/SKILL.md` - 修改前检查流程

```markdown
---
name: before-modify
description: 修改 {项目名} 项目代码前必须执行的检查流程，防止重复实现和风格不一致 (project)
---

# {项目名} 修改前检查

## 核心原则

修改任何代码前，必须先读取相关源文件，不要凭记忆或猜测。

## 检查清单

### 项目结构变化检查
如果发现以下情况，提醒用户运行 `/init-skills:update` 更新 skills：
- 新增了重要目录（如 `services/`、`utils/`、`components/` 等）
- 删除了目录或重要文件
- 引入或移除了框架/依赖
- 重构了代码结构（如文件位置变动）
- 本 skill 中记录的路径已不存在

### 添加/修改数据库字段时
1. 先读 `{数据模型路径}` 确认字段是否已存在
2. 检查现有命名风格（如：snake_case）
3. 如果涉及 API，同步检查类型定义文件

### 添加/修改 API 接口时
1. 先读 `{API路由路径}` 下对应路由文件
2. 检查前端 API 封装是否已有类似接口
3. 遵循现有分页和响应格式

### 添加工具函数时
先搜索项目是否已有类似实现：
| 功能类型 | 查找位置 |
|----------|----------|
| 通用工具 | {工具函数路径} |
| 类型定义 | {类型定义路径} |

## 常见错误示例

根据项目实际情况填写，例如：
- 不要新建日期格式化函数，项目已有 `{已有的日期工具路径}`
- 不要自定义分页逻辑，使用 `{已有的分页工具路径}`
- 不要重复定义 `{已有的类型}` 类型，已在 `{类型定义路径}` 中定义
```

#### 2. `project-structure/SKILL.md` - 项目结构索引

```markdown
---
name: project-structure
description: {项目名} 项目结构索引，指明各功能模块的源文件位置和项目约定 (project)
---

# {项目名} 项目结构

## 项目概述
{简要描述项目功能}

## 技术栈
- 后端: {后端技术栈}
- 前端: {前端技术栈}
- 数据库: {数据库}

## 文件位置索引

| 功能 | 路径 | 说明 |
|------|------|------|
| 数据模型 | {路径} | {说明} |
| API 路由 | {路径} | {说明} |
| 业务逻辑 | {路径} | {说明} |
| 前端组件 | {路径} | {说明} |
| 类型定义 | {路径} | {说明} |
| 工具函数 | {路径} | {说明} |
| 测试文件 | {路径} | {说明} |

## 项目约定

### 命名规范
- 文件命名: {规范}
- 变量命名: {规范}
- 数据库字段: {规范}

### API 规范
- 风格: {RESTful/GraphQL}
- 分页参数: {参数名}
- 响应格式: {格式}

## 运行命令

| 命令 | 说明 |
|------|------|
| {命令} | 启动开发服务器 |
| {命令} | 运行测试 |
| {命令} | 构建生产版本 |
```

### 第四步：输出确认

告知用户生成结果：

```
已生成项目级 skills：

.claude/skills/
├── before-modify/
│   └── SKILL.md      # 修改前检查流程
└── project-structure/
    └── SKILL.md      # 项目结构索引

重启 Claude Code 后，这些 skills 会自动生效。
```

## 设计原则

### Skills 应该记录什么

**记录**：
- 去哪里查看（文件路径）
- 遵循什么规则（约定）
- 执行什么流程（检查清单）

**不记录**：
- 具体的字段列表
- 详细的 API 参数
- 会频繁变化的内容

### 为什么这样设计

Skills 记录 "规则和流程"，不记录 "数据本身"

好处：
- 项目代码变化时，skills 不需要同步更新
- 强制开发者先读源文件，避免重复实现
- 保持 skills 简洁稳定

## 适用场景

- 项目开发到 demo 阶段，架构基本稳定
- 需要多人协作或长期维护
- 希望 Claude Code 遵循现有设计模式

## 注意事项

- 生成的 skills 中 `(project)` 标记表示这是项目级 skill，仅在当前项目生效
- 如果项目结构发生重大变化，可以运行 `/init-skills:update` 更新
- 生成的模板中 `{占位符}` 需要根据实际探测结果替换为具体值
