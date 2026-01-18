---
name: project-coordinator
description: 项目总指挥，协调所有 skills 的使用。适配 Midway.js + TypeScript 后端项目（PM_Back）。当不确定使用哪个 skill 时，或需要协调多个 skills 时使用此 skill。
---

# Project Coordinator (项目总指挥)

PM_Back 项目的总指挥 skill，用于协调所有 skills 的使用，确保开发工作流程的顺畅执行。

## 项目背景

**项目名称**: PM_Back  
**项目类型**: Node.js 后端服务  
**技术栈**: Midway.js 3.x + TypeScript 4.8 + MySQL + TypeORM + Jest  

## 核心职责

1. **任务路由**: 根据任务类型路由到合适的 skill
2. **工作流程协调**: **MUST** 确保 skills 按正确顺序使用，**REQUIRED** 强制执行工作流
3. **技能选择指导**: 帮助选择最合适的 skill
4. **工作流验证**: **MUST** 在每个阶段完成后验证，**REQUIRED** 确认上一阶段完成后再进入下一阶段

## 技能体系

### 1. feature-design (功能设计)
**使用场景**: 需求分析、功能设计、架构设计  
**典型任务**:
- 分析新的业务需求
- 设计 API 接口
- 设计数据库结构
- 规划 Controller/Service/Entity 分层

**路由触发词**: "需求"、"设计"、"方案"、"架构"、"接口设计"

### 2. project-standards (编码规范)
**使用场景**: 编写代码、遵循规范、代码实现  
**典型任务**:
- 实现 Controller、Service、Entity
- 遵循 Midway.js 规范
- 遵循 TypeScript 编码标准
- 数据库操作（TypeORM）

**路由触发词**: "实现"、"编写"、"代码"、"Controller"、"Service"、"Entity"

### 3. testing (测试指南)
**使用场景**: 编写测试、测试覆盖、测试验证  
**典型任务**:
- 编写单元测试（Jest）
- 测试 Controller 接口
- 测试 Service 业务逻辑
- 测试 Entity 映射

**路由触发词**: "测试"、"单元测试"、"Jest"、"覆盖率"

### 4. code-review (代码审查)
**使用场景**: 代码审查、质量检查、规范检查  
**典型任务**:
- 审查代码质量
- 检查是否符合 Midway.js 规范
- 检查安全性
- 检查性能问题

**路由触发词**: "审查"、"检查"、"Review"、"质量"

### 5. debugging (问题排查)
**使用场景**: 调试问题、定位 Bug、性能优化  
**典型任务**:
- 调试 API 接口问题
- 调试数据库查询问题
- 调试 TypeORM 映射问题
- 性能问题排查

**路由触发词**: "错误"、"Bug"、"调试"、"问题"、"排查"、"性能"

## 任务路由规则

**⚠️ 重要：工作流强制执行规则**

当使用 `/project-coordinator` 时，**MUST** 严格按照以下工作流执行，**禁止跳过任何阶段**。

### 新功能开发流程

**执行顺序（MUST 按此顺序，不可跳过）**:

```
1. feature-design → 分析需求，设计 API 和数据库
   ✅ 检查点: 确认需求分析完成、数据库设计完成
   
2. project-standards → 实现 Controller/Service/Entity
   ✅ 检查点: 确认 feature-design 阶段已完成
   
3. testing → 编写测试用例
   ✅ 检查点: 确认代码实现完成
   
4. code-review → 代码审查
   ✅ 检查点: 确认测试通过
   
5. (如有问题) debugging → 问题排查
```

**强制执行要求**:
- **MUST** 先执行 feature-design，完成需求分析和设计
- **REQUIRED** 在进入 project-standards 前，确认 feature-design 已完成
- **禁止** 跳过设计阶段直接实现代码
- **MUST** 在每个阶段完成后进行验证检查点

### Bug 修复流程

```
1. debugging → 定位问题原因
2. project-standards → 修复代码
3. testing → 验证修复
4. code-review → 审查修复
```

### 代码审查流程

```
1. code-review → 审查代码
2. (如有问题) project-standards → 修复
3. testing → 验证
4. code-review → 再次审查
```

## 项目特定指南

### 技术栈
- **框架**: Midway.js 3.x
- **语言**: TypeScript 4.8
- **数据库**: MySQL (TypeORM 0.3.25)
- **测试**: Jest 29.2.2
- **构建**: mwtsc
- **代码检查**: mwts

### 项目结构
```
src/
├── controller/    # 控制器（路由处理）
├── service/       # 业务逻辑层
├── entity/        # 数据库实体（TypeORM）
├── config/        # 配置文件
├── middleware/    # 中间件
├── filter/        # 过滤器
└── dict/          # 字典定义
```

### 常用模式

**Controller 模式**:
```typescript
@Controller('/resource')
export class ResourceController {
  @Inject()
  resourceService: ResourceService;
  
  @Get('/list')
  async list() { }
}
```

**Service 模式**:
```typescript
@Provide()
export class ResourceService {
  @InjectEntityModel(Resource)
  resourceRepository: Repository<Resource>;
  
  async find() { }
}
```

**Entity 模式**:
```typescript
@Entity()
export class Resource {
  @PrimaryGeneratedColumn()
  id: number;
}
```

## 使用示例

### 示例 1: 新功能开发（正确执行方式）

**用户请求**: "为版本维护添加一个类型标识全量更新还是热更新"

**正确执行流程**:
```
1. ✅ 识别任务类型: 新功能开发（添加字段）
2. ✅ 执行 feature-design:
   - 分析需求: 需要添加更新类型字段（全量/热更新）
   - 设计数据库: 在 version 表中添加 updateType 字段
   - 设计字段类型: number 类型，1-全量更新，2-热更新
   - 设计默认值: 考虑向后兼容性
3. ✅ 验证检查点: 确认设计完成
4. ✅ 执行 project-standards:
   - 修改 Version Entity，添加 updateType 字段
   - 更新数据库 schema 文档
   - 创建数据库迁移 SQL
5. ✅ 验证检查点: 确认实现完成
6. ✅ 执行 code-review: 审查代码质量
```

**错误执行方式（禁止）**:
```
❌ 直接修改 Entity，跳过 feature-design
❌ 不进行需求分析，直接实现
❌ 不验证设计完成就进入实现阶段
```

### 示例 2: Bug 修复
```
用户: API 接口返回 500 错误
→ /project-coordinator 如何排查这个问题？
→ 路由到: debugging (先排查) → project-standards (修复)
```

### 示例 3: 代码审查
```
用户: 帮我审查这段代码
→ /project-coordinator 如何审查代码？
→ 路由到: code-review
```

## 最佳实践

1. **不确定时先问总指挥**: 使用 `/project-coordinator` 确认应该使用哪个 skill
2. **MUST 按流程顺序执行**: 新功能开发**必须**遵循 feature-design → project-standards → testing → code-review，**禁止跳过任何阶段**
3. **工作流检查点**: 在每个阶段完成后，**MUST** 验证是否完成，**REQUIRED** 确认后再进入下一阶段
4. **充分利用项目文档**: 参考 `ARCHITECTURE.md` 和 `AI_FEATURES.md`
5. **遵循 Midway.js 规范**: 所有代码实现都要符合框架规范

## 工作流执行检查清单

当使用 `/project-coordinator` 时，**MUST** 执行以下检查：

### 新功能开发检查清单

- [ ] **阶段 1 - feature-design**:
  - [ ] 已分析业务需求
  - [ ] 已设计数据库结构（Entity 字段）
  - [ ] 已设计 API 接口（如需要）
  - [ ] 已考虑向后兼容性
  - [ ] **验证**: 设计文档或设计说明已生成

- [ ] **阶段 2 - project-standards**:
  - [ ] **验证**: feature-design 阶段已完成
  - [ ] 已实现 Entity 修改
  - [ ] 已更新相关文档
  - [ ] 已创建数据库迁移 SQL（如需要）
  - [ ] **验证**: 代码实现完成

- [ ] **阶段 3 - testing**（如需要）:
  - [ ] **验证**: 代码实现完成
  - [ ] 已编写测试用例
  - [ ] 测试通过

- [ ] **阶段 4 - code-review**:
  - [ ] **验证**: 测试通过（如有测试）
  - [ ] 已审查代码质量
  - [ ] 已检查是否符合规范

## 相关资源

- **项目架构文档**: `ARCHITECTURE.md`
- **AI 功能文档**: `AI_FEATURES.md`
- **全局工作流模板**: `~/.cursor/skills/workflow-template/`
