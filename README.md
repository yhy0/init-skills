# init-skills

为当前项目生成项目级 Claude Code skills，防止后续修改时重复实现、风格不一致。

## 功能

当项目开发到 demo 阶段后，运行此插件可以：

1. **自动探测**项目的技术栈、框架、目录结构
2. **推断约定**：命名风格、API 格式、分页参数等
3. **生成两个项目级 skills**（存放在 `.claude/skills/` 目录）：
   - `before-modify` - 修改前检查流程
   - `project-structure` - 项目结构索引
4. **支持更新**：项目变化后可以增量更新 skills

## 安装

```bash
# 1. 添加 marketplace
/plugin marketplace add yhy0/init-skills

# 2. 安装插件
/plugin install init-skills@init-skills
```

## 使用

### 初始化 Skills

首次为项目生成 skills：

```
/init-skills:init
```

或者让 Claude 执行：

```
帮我生成项目级 skills
```

### 更新 Skills

项目结构变化后，更新已有的 skills：

```
/init-skills:update
```

或者：

```
帮我更新项目 skills
```

## 生成的 Skills

生成的 skills 存放在项目的 `.claude/skills/` 目录下：

```
.claude/skills/
├── before-modify/
│   └── SKILL.md
└── project-structure/
    └── SKILL.md
```

### before-modify

修改代码前的检查流程，强制先读取源文件再修改，避免：
- 重复实现已有功能
- 字段命名不一致
- API 参数格式混乱

### project-structure

项目结构索引，记录：
- 关键文件位置
- 技术栈和框架
- 项目约定
- 运行命令

## 设计原则

**Skills 记录"规则和流程"，不记录"数据本身"**

- 记录：去哪里查看、遵循什么规则、执行什么流程
- 不记录：具体字段列表、详细 API 参数

这样设计的好处：
- 项目代码变化时，skills 不需要频繁同步更新
- 强制开发者先读源文件，避免重复实现
- 保持 skills 简洁稳定

## 支持的技术栈

| 类型 | 支持 |
|------|------|
| 后端 | Python (FastAPI/Flask/Django), Node.js (Express/NestJS/Koa), Go (Gin/Echo), Rust, Java |
| 前端 | Vue, React, Svelte |
| ORM | SQLAlchemy, Prisma, TypeORM, GORM |

## 适用场景

- 项目开发到 demo 阶段，架构基本稳定
- 需要多人协作或长期维护
- 希望 Claude Code 遵循现有设计模式

## 关于 Skill 标记

- `(user)` - 用户级 skill，需要用户主动调用
- `(project)` - 项目级 skill，仅在当前项目生效，Claude 会自动参考

## License

MIT
