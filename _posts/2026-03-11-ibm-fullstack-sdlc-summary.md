---
layout: post
date: 2026-03-11
title: "IBM Full Stack Certificate - Software Development Lifecycle 总结"
categories: tech_coding
tags:
  - SoftwareEngineering
  - SDLC
  - IBM 证书学习
  - ActiveLearning
---

# 1-1 Software Development Lifecycle 总结

> 学习 IBM Full Stack Cloud Developer Certificate 课程笔记 - Module 1-1

## 📚 核心概念

### Software Engineering 定义
运用**科学原理**（计算机科学、数学、工程学）设计、创建和维护软件的**系统性工程**，通过**规范化、可量化的方法**将用户需求转化为满足功能、性能及质量要求的软件产品。

### Software Engineer vs Developer
- **Software Engineer**：系统级思维，负责**整体架构设计、需求分析与项目规划**，覆盖"从 0 到 1"全流程
- **Developer**：聚焦**具体功能实现**，依据设计文档编写代码，关注"如何实现"

---

## 🔄 SDLC（Software Development Life Cycle）

### 定义与优势
**SDLC** 是开发高质量软件的**系统化过程**，目标是在**可预测的时间与预算内**产出符合客户业务需求的软件。

**核心优势：**
- 提供明确流程，减少风险
- 阶段定义清晰，提升效率
- 促进 stakeholder 沟通
- 支持迭代调整
- 早期解决问题（设计阶段而非编码阶段）
- 明确角色分工

### 六大阶段

| 阶段 | 核心目标 | 关键交付物 |
|------|---------|-----------|
| **Planning** | 明确目标、可行性、初始需求 | SRS（Software Requirement Specification） |
| **Design** | 将需求转化为可执行架构 | 设计文档（架构图、数据库设计、接口文档） |
| **Development** | 根据设计文档编写代码 | 可运行软件 + 用户指南 + 源代码注释 |
| **Testing** | 发现并修复缺陷 | 测试报告（测试结果、缺陷统计） |
| **Deployment** | 发布到生产环境 | 生产环境中的可运行软件 |
| **Maintenance** | 保持稳定运行并优化 | 持续更新（占生命周期 60%-70%） |

---

## 📋 需求工程

### 需求收集六步流程
1. **Identify stakeholders**（客户、用户、系统管理员、工程团队、供应商）
2. **Establish goals and objectives**（业务目标 + 技术目标）
3. **Elicit requirements**（访谈、问卷、原型测试）
4. **Document requirements**（整理为标准格式）
5. **Analyze & confirm**（评审一致性与完整性）
6. **Prioritize**（"必须有"/"迫切想要"/"有也很好"）

### 三类需求规格说明书

| 文档 | 全称 | 视角 | 用途 |
|------|------|------|------|
| **URS** | User Requirement Specification | 用户视角 | 业务需求，用户与开发团队的沟通桥梁 |
| **SRS** | Software Requirement Specification | 技术视角 | 功能与非功能需求，开发的核心依据 |
| **SysRS** | System Requirement Specification | 系统视角 | 整个系统（含硬件、软件、网络）的需求 |

---

## 🛠️ 开发方法论

| 方法论 | 特点 | 适用场景 | 优缺点 |
|--------|------|---------|--------|
| **Waterfall** | 线性顺序，阶段固定，文档驱动 | 需求明确、稳定（政府、银行系统） | ✅ 流程清晰 ✅ 文档齐全 ❌ 灵活性差 ❌ 风险滞后 |
| **V-Model** | 瀑布变种，测试与开发一一对应 | 需求明确、模块化程度高 | ✅ 测试覆盖全面 ❌ 刚性大 |
| **Agile** | 迭代式（1-4 周 sprints），客户协作，快速响应变化 | 需求多变、创新性（互联网产品、startups） | ✅ 快速交付 ✅ 适应性强 ❌ 文档简化 ❌ 范围易蔓延 |

**Agile Manifesto 核心价值观：**
- 个体和互动 > 流程和工具
- 工作软件 > 详尽文档
- 客户合作 > 合同谈判
- 响应变化 > 遵循计划

---

## 🧪 软件测试

### 三大测试类型

| 类型 | 关注点 | 测试内容 |
|------|--------|---------|
| **Functional Testing** | "有没有" | 输入→处理→输出逻辑（黑盒测试） |
| **Non-Functional Testing** | "好不好" | 性能、安全、兼容性、可用性 |
| **Regression Testing** | "变没变" | 变更后验证已有功能未受影响 |

### 四级测试级别

| 级别 | 执行者 | 测试范围 | 目标 |
|------|--------|---------|------|
| **Unit Testing** | 开发人员 | 最小单元（函数/模块） | 消除构造错误 |
| **Integration Testing** | 开发/测试 | 模块间接口与交互 | 发现协作错误 |
| **System Testing** | 测试工程师 | 完整系统（功能 + 非功能） | 确保系统整体正确 |
| **Acceptance Testing** | 用户/客户 | 实际业务需求 | 确认符合用户预期（Alpha/Beta 测试） |

---

## 📄 软件文档

### 两大分类

| 类型 | 聚焦 | 受众 | 包含内容 |
|------|------|------|---------|
| **产品文档** | 产品本身 | 开发团队 + 最终用户 | 需求文档、设计文档、技术文档、QA 文档、用户文档 |
| **过程文档** | 开发过程 | 项目管理者 + 开发团队 | SOP（标准操作程序）、流程规范 |

### 五类产品文档
1. **Requirements** - SRS、SysRS、UAS（用户故事、用例、功能列表）
2. **Design** - 概念设计（架构图）+ 技术设计（数据库 schema、API 文档）
3. **Technical** - 代码注释、技术白皮书、函数说明
4. **QA** - 测试计划、测试用例、测试报告、缺陷跟踪矩阵
5. **User** - 用户手册、安装指南、教程、FAQ、故障排除手册

---

## 👥 项目角色与职责

| 角色 | 核心定位 | 关键职责 |
|------|---------|---------|
| **Project Manager / Scrum Master** | 组织者与 facilitator | 项目规划、进度跟踪、风险管控、流程优化、障碍清除 |
| **Stakeholders** | 需求提出者与验证者 | 需求定义、反馈验证、决策参与 |
| **System/Software Architect** | 技术蓝图设计者 | 架构设计、技术栈选型、技术指导、标准制定 |
| **UX Designer** | 用户体验优化者 | 用户研究、原型设计、交互与视觉设计 |
| **Software Developer** | 功能实现者 | 编码实现、单元测试、代码优化 |
| **Tester / QA Engineer** | 质量把关者 | 测试用例设计、测试执行、缺陷跟踪 |
| **Site Reliability / Ops Engineer** | 系统稳定维护者 | CI/CD pipeline、监控与故障排查、基础设施管理 |
| **Product Manager / Owner** | 产品方向掌舵者 | 需求收集分析、产品路线图、团队协调 |
| **Technical Writer** | 知识传播者 | 用户文档、技术文档、文档维护 |

---

## 🏷️ 软件版本控制（SemVer）

**格式：** `MAJOR.MINOR.PATCH`（主版本。次版本。修订号）

| 版本号 | 递增时机 | 兼容性 | 示例 |
|--------|---------|--------|------|
| **MAJOR** | 不兼容变更（删除核心功能、修改 API） | ❌ 不兼容 | `1.x.x` → `2.0.0` |
| **MINOR** | 新增向后兼容功能 | ✅ 兼容 | `1.2.x` → `1.3.0` |
| **PATCH** | 向后兼容的 bug 修复 | ✅ 兼容 | `1.2.3` → `1.2.4` |

**预发布后缀：** `-alpha`、`-beta`、`-rc`（如 `2.0.0-beta.1`）

---

## ✨ 核心要点

1. **Software Engineering** = 工程化方法解决软件问题，平衡技术实现与用户需求
2. **SDLC** 是软件开发的"导航系统"，通过规范化阶段提高效率、降低风险
3. **需求收集** 是基础，SRS/URS/SysRS 构成需求基石
4. **方法论选择** 取决于项目需求（稳定→Waterfall，多变→Agile）
5. **测试分层验证**：Unit→Integration→System→Acceptance，确保质量
6. **文档是神经中枢**，连接需求、开发、测试与用户
7. **多角色协同** 以用户需求为核心，形成闭环

---

*Summary based on IBM Full Stack Engineer Certificate - Module 1-1*  
*2026-03-11*
