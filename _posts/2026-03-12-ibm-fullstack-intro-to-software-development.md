---
layout: post
date: 2026-03-12
title: "IBM Full Stack Engineer - Introduction to Software Development 学习笔记"
categories: tech_coding
tags:
  - IBM
  - FullStack
  - SoftwareDevelopment
  - WebDevelopment
  - Learning
---

## 📚 核心知识点总结

### 1. Web 开发基础流程

Web 开发的核心是 **客户端（Client）与服务器（Server）的交互**：

1. **客户端发起请求** - 用户通过浏览器输入 URL，浏览器向服务器发送请求
2. **服务器返回响应** - 返回 **HTML**（结构）、**CSS**（样式）、**JavaScript**（交互逻辑）
3. **页面渲染** - 浏览器解析文件，将内容呈现给用户

**静态内容 vs 动态内容：**
- **静态内容** - 预先存储的固定资源（图片、CSS 文件）
- **动态内容** - 根据请求实时生成（用户信息、商品列表，涉及数据库交互）

---

### 2. 云应用（Cloud Applications）特点

云应用与传统网站的核心区别在于 **后端基础设施**：

- 依托 **云原生后端**（云计算、云存储、数据处理服务）
- 具备 **高可扩展性**（按需调整资源）和 **高韧性**（故障时自动切换）

---

### 3. 前后端与全栈开发

| 领域 | 职责 | 核心技术 |
|------|------|----------|
| **Front-end** | 客户端可见部分（页面布局、交互效果） | HTML, CSS, JavaScript, React, Vue |
| **Back-end** | 服务器端逻辑（业务规则、数据处理、安全认证） | Python, Java, Node.js, Django, Spring, 数据库 |
| **Full-stack** | 覆盖前后端全流程 | 两者皆掌握 |

---

### 4. 前端开发核心技术

**三大基础：**
- **HTML** - 定义网页骨架与内容（语义化标签提升 SEO）
- **CSS** - 控制视觉呈现（Flexbox/Grid 实现响应式布局）
- **JavaScript** - 添加动态功能（DOM 操作、AJAX/Fetch API）

**主流框架对比：**

| 框架 | 特点 | 适用场景 |
|------|------|----------|
| **React** | 组件化、虚拟 DOM、单向数据流 | 复杂交互应用（电商详情页） |
| **Vue.js** | 渐进式、轻量级、双向数据绑定 | 中小型项目（促销活动页） |
| **Angular** | 完整解决方案、强类型（TypeScript） | 企业级应用（后台管理系统） |

**响应式设计：**
- **媒体查询（Media Queries）** - 根据屏幕尺寸调整布局
- **弹性单位（Rem/Viewport）** - 实现自适应缩放

---

### 5. 后端开发核心职责

- **服务器资源管理** - 搭建与维护服务器环境
- **业务逻辑实现** - 订单生成、支付验证、库存扣减
- **数据安全与存储** - 加密敏感信息、HTTPS 传输
- **API 与路由管理** - 设计 API 接口，映射前端请求到后端服务

**常用后端框架：**
- **Node.js** - Express.js（轻量级）、NestJS（企业级）
- **Python** - Django（全栈框架）、FastAPI（高性能异步）
- **Java** - Spring Boot（企业级主流选择）

---

### 6. 团队合作与敏捷小队（Squad）

**团队合作的核心价值：**
- 技能互补与拓展
- 创造力激发（brainstorming）
- 赋能与积极结果

**敏捷小队（Squad）特征：**
- **小规模** - up to 10 人（5-7 开发 + 1-2 设计 + 1 squad leader）
- **跨职能** - 包含开发、测试、设计，能独立完成端到端交付
- **明确角色分工** - Squad Leader、软件工程师、UX/UI 设计师

---

### 7. 结对编程（Pair Programming）

**定义：** 两名开发者共同在同一工作站协作完成任务，核心是 **实时知识共享与反馈**。

**三种风格：**

| 风格 | 描述 | 适用场景 |
|------|------|----------|
| **Driver/Navigator** | 一人写代码（Driver），一人审查并提出建议（Navigator），定期轮换 | 常规功能开发 |
| **Ping-Pong** | 结合 TDD：A 写失败测试→B 写代码通过→B 写新测试→A 通过... | 复杂功能开发 |
| **Strong Style** | 经验者担任 Navigator，新手担任 Driver，"idea must go through someone else's hands" | 新手培养 |

**优势：**
- 缺陷率降低 15%-50%
- 促进知识共享，减少"信息孤岛"
- 增强团队协作与软技能

---

### 8. 云应用开发核心工具

**版本控制（Version Control）：**
- **Git** - 最流行的分布式版本控制系统
- **GitHub** - 代码托管平台，提供 Pull Request、代码审查功能

**代码库（Library）vs 框架（Framework）：**

| 特性 | Library | Framework |
|------|---------|-----------|
| **控制权** | 开发者调用库方法 | 框架调用开发者代码（控制反转 IoC） |
| **特点** | 按需调用、灵活 | Opinionated（有主见）、标准化 |
| **例子** | jQuery、email-validator | AngularJS、Vue.js、Django |

**构建工具链：**
- **CI/CD** - Jenkins、GitHub Actions、Argo CD（自动化流程）
- **构建工具** - Webpack、Babel、Turbopack（代码转换与打包）
- **包管理器** - npm、Maven、pip（依赖管理与分发）

---

### 9. 软件栈（Software Stack）

**定义：** 支持应用程序执行的相互关联的软件组件集合，以分层 hierarchy 组织。

**常见软件栈对比：**

| 软件栈 | 组件 | 优点 | 缺点 |
|--------|------|------|------|
| **LAMP** | Linux, Apache, MySQL, PHP/Python | 开源免费、稳定成熟 | 非实时应用性能一般 |
| **MEAN** | MongoDB, Express, Angular, Node.js | JavaScript 全栈、实时应用支持好 | Angular 学习曲线陡 |
| **MERN** | MongoDB, Express, React, Node.js | React 组件化、社区活跃、适合 SPA | 状态管理复杂、SEO 较弱 |
| **MEVN** | MongoDB, Express, Vue, Node.js | Vue 易学习、性能好、适合快速开发 | 生态系统不完善 |

---

## 🎯 关键术语（保持英文）

- **Front-end / Back-end / Full-stack**
- **HTML / CSS / JavaScript**
- **DOM（Document Object Model）**
- **AJAX / Fetch API**
- **API（Application Programming Interface）**
- **Route / Endpoint**
- **CI/CD（Continuous Integration/Continuous Deployment）**
- **Library / Framework**
- **Version Control / Git / GitHub**
- **Package Manager**
- **Software Stack / Tech Stack**
- **Squad / Pair Programming / Driver-Navigator**
- **TDD（Test-Driven Development）**
- **IoC（Inversion of Control）**

---

## 💡 核心结论

1. Web 开发本质是 **客户端与服务器的交互**
2. 云应用通过 **云原生后端** 提升可扩展性和韧性
3. 前后端有明确分工，但 **全栈开发** 因能满足全流程需求而日益普及
4. **团队合作** 是软件工程的基石，敏捷小队（Squad）提升灵活性与效率
5. **结对编程** 提升代码质量与知识共享（缺陷率降低 15%-50%）
6. 工具链（版本控制、库、框架、CI/CD、包管理器）是开发效率的关键支撑

---

*Published: 2026-03-12*  
*Course: IBM Full Stack Engineer Certificate - Module 1-2*
