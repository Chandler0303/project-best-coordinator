# Training Log: project-coordinator

## 2025-01-XX: 工作流执行问题

### 问题描述
**Issue**: 用户请求"为版本维护添加类型标识"时，AI 直接跳过了 feature-design 阶段，直接进入 project-standards 实现阶段。

**用户请求**: `/project-coordinator 为版本维护添加一个类型标识全量更新还是热更新`

**预期行为**: 
1. feature-design → 分析需求，设计数据库字段
2. project-standards → 实现 Entity 修改
3. testing → 编写测试（如需要）
4. code-review → 代码审查

**实际行为**: 
- 直接读取了 project-coordinator skill
- 直接开始实现（修改 Entity、更新文档）
- 完全跳过了 feature-design 阶段

### 根本原因分析

1. **Skill 描述问题**:
   - project-coordinator skill 定义了工作流，但缺少**强制执行**的指令
   - 没有明确的检查点要求 AI 必须按顺序执行
   - 缺少"MUST"、"REQUIRED"等强制性语言

2. **工作流协调问题**:
   - Skill 说明了工作流，但没有要求 AI 在执行前先调用 feature-design
   - 缺少明确的步骤验证机制

3. **触发机制问题**:
   - 虽然读取了 project-coordinator，但 AI 没有意识到需要先执行 feature-design
   - 缺少"先设计后实现"的强制要求

### 改进方案

1. **添加强制执行指令**:
   - 在 project-coordinator skill 中添加明确的"MUST"指令
   - 要求 AI 在执行任何实现前，必须先完成设计阶段

2. **添加工作流检查点**:
   - 在每个阶段完成后，要求验证是否完成
   - 添加"在进入下一阶段前，确认上一阶段已完成"的检查

3. **改进示例**:
   - 添加更明确的工作流执行示例
   - 展示如何正确按顺序执行多个 skills

### 测试场景

**场景**: 新功能开发请求
- 输入: "为版本维护添加类型标识"
- 预期: 先执行 feature-design，再执行 project-standards
- 验证: 检查是否先进行了需求分析和设计

### 改进状态
- [x] 已更新 project-coordinator skill
- [x] 已添加强制执行指令（MUST、REQUIRED）
- [x] 已添加工作流检查点
- [x] 已添加工作流执行检查清单
- [x] 已改进示例，展示正确和错误的执行方式
- [ ] 已测试改进效果（待下次使用验证）

### 改进内容总结

1. **添加强制执行指令**:
   - 在核心职责中添加了"MUST"和"REQUIRED"强制性语言
   - 明确要求强制执行工作流

2. **添加工作流检查点**:
   - 在每个阶段后添加了验证检查点
   - 要求确认上一阶段完成后再进入下一阶段

3. **改进示例**:
   - 添加了正确执行方式的详细示例
   - 添加了错误执行方式的对比（禁止的行为）

4. **添加检查清单**:
   - 创建了详细的工作流执行检查清单
   - 每个阶段都有明确的验证项

### 预期效果

改进后，当使用 `/project-coordinator` 时：
- AI 会先执行 feature-design，完成需求分析和设计
- AI 会验证设计完成后再进入实现阶段
- AI 不会跳过任何工作流阶段
- AI 会按照检查清单逐步执行
