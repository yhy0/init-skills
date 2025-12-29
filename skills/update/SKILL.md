---
name: update
description: 当项目结构发生变化（如新增目录、引入新框架、重构代码）后，重新扫描项目并增量更新已有的 before-modify 和 project-structure skills，保留用户自定义的规则 (user)
---

# 更新项目 Skills

当项目结构发生变化后，更新已有的 `.claude/skills/` 中的 skills。

## 前置条件

项目必须已经存在 `.claude/skills/before-modify/SKILL.md` 和 `.claude/skills/project-structure/SKILL.md`。

如果不存在，请先运行 `/init-skills:init` 初始化。

## 执行流程

### 第一步：读取现有 Skills

读取项目中已有的 skills：
- `.claude/skills/before-modify/SKILL.md`
- `.claude/skills/project-structure/SKILL.md`

### 第二步：重新探测项目

执行与 `/init-skills:init` 相同的探测流程：
1. 技术栈探测
2. 框架探测
3. 目录结构分析
4. 约定推断

### 第三步：对比变化

将新探测结果与现有 skills 对比，识别变化：

```
## 发现以下变化

**新增**:
- 新增目录: src/utils/
- 新增框架: Redis 缓存

**移除**:
- 移除目录: lib/ (已重命名为 src/)

**修改**:
- API 路由路径: api/ → src/api/

是否更新这些变化？
```

### 第四步：选择性更新

用户可以选择：
1. **全部更新** - 应用所有检测到的变化
2. **选择性更新** - 只更新部分内容
3. **取消** - 保持现有 skills 不变

### 第五步：执行更新

根据用户选择，更新对应的 skill 文件：
- 保留用户手动添加的自定义内容
- 只更新自动探测的部分

### 第六步：输出确认

```
已更新项目级 skills：

更新内容：
- [before-modify] 更新了 API 路由路径
- [project-structure] 新增了 utils 目录索引

保留内容：
- [before-modify] 自定义的检查规则
- [project-structure] 手动添加的约定说明
```

## 更新策略

### 保守更新原则

- **只更新路径和技术栈信息**，不覆盖用户自定义的规则
- **新增内容追加**，不删除现有内容
- **有冲突时询问用户**

### 识别自定义内容

以下内容视为用户自定义，更新时保留：
- `## 自定义规则` 章节下的内容
- 用户手动添加的检查项
- 非模板生成的约定说明

## 使用场景

- 项目重构后目录结构变化
- 引入新的框架或工具
- 命名约定发生变化
- 定期同步项目状态
