# Ant Design Admin Skill

> 面向 B 端后台管理系统的 **Ant Design 设计规范 + AI Skill 模板库**。让 Cursor、Claude、Trae、Gemini 等 AI 工具按统一规范 **生成页面、走查 UI、沉淀组件模板**，产出风格一致、可交互的管理后台原型。

[![Ant Design](https://img.shields.io/badge/Ant%20Design-5.x-1677ff)](https://ant.design/)
[![Cursor Skill](https://img.shields.io/badge/Cursor-Skill-000000)](https://cursor.com/docs/context/skills)
[![React](https://img.shields.io/badge/React-18%2F19-61dafb)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178c6)](https://www.typescriptlang.org/)

---

## 这是什么

本项目将 Ant Design B 端设计规范拆分为 **可维护的结构化文档 + 页面模板 + AI Skill 配置**，解决以下问题：

- 每次让 AI 写页面都要重复粘贴大段 Prompt，且输出不稳定
- 不同人 / 不同会话生成的页面风格、间距、组件用法不一致
- 团队「家规」散落在文档里，AI 无法自动遵守

通过 Skill 机制，AI 会在生成、审查、调整 UI 时 **自动读取规范**，默认输出带左侧菜单的完整后台应用壳，并使用真实 `antd` 组件（非 div 模拟）。

---

## 核心能力

| 能力 | 说明 |
| ---- | ---- |
| **页面生成** | 按模板生成列表页、详情页、表单弹窗、应用壳等多页后台 |
| **UI 走查** | 对照 Design Token、布局、组件规范审查现有页面并给出修改建议 |
| **规范沉淀** | 规范 / 模板 / 规则分层维护，可扩展为内部组件库 Skill |
| **多工具支持** | Cursor Skill、Claude Skill、Trae Rules、Gemini 单文件导入 |

---

## 快速开始

### 1. Clone 仓库

```bash
git clone https://github.com/YXBaijue/UG-Ant-Design-Skill.git
cd UG-Ant-Design-Skill
```

### 2. 选择你的 AI 工具

#### Cursor（推荐）

Skill 已内置在 `.cursor/skills/ant-design-system/SKILL.md`，打开本项目后 Cursor 会自动识别。

在 Agent 对话中直接描述需求，例如：

```
帮我生成一个「站点映射」查询表格页，挂到后台菜单里
```

AI 会自动读取 `ant-design-system-prompt.md` → `templates/app-shell.md` → `templates/list-table.md` 并按规范生成。

#### Claude Code

Skill 位于 `.claude/skills/ant-design-system/SKILL.md`，用法与 Cursor 相同。

#### Trae IDE

项目规则位于 `.trae/rules/ant-design-system.md`，生成 B 端页面时会自动触发。

#### Gemini / 仅支持单文件导入的工具

请导入合并版规范：

```
dist/ant-design-system.full.md
```

该文件已合并样式规范、页面模板与组件 / 交互规则，适合无法读取多文件的 AI 场景。

### 3. 预览 HTML 原型

直接在浏览器打开（支持 `file://` 协议）：

| 文件 | 说明 |
| ---- | ---- |
| [`admin-shell.html`](./admin-shell.html) | 后台应用壳参考（Layout + Menu + iframe 多页切换） |
| [`template-list-page.html`](./template-list-page.html) | 查询表格页模板示例 |
| [`站点映射.html`](./站点映射.html) | 独立内容页列表示例（无侧栏） |
| [`国际备货站点列表.html`](./国际备货站点列表.html) | 国际备货业务列表示例 |

---

## 使用说明

### 默认生成模式

生成 **B 端后台页面** 时，默认必须带完整应用壳，除非明确要求「仅内容页 / 不要菜单 / 独立页面」：

```
Layout + Sider(208px) + Menu + Header(48px) + Content
```

**读取顺序（强约束）：**

1. [`ant-design-system-prompt.md`](./ant-design-system-prompt.md) — 品牌配置、Design Token、布局与组件外观
2. [`templates/app-shell.md`](./templates/app-shell.md) — 应用壳 + `PAGE_REGISTRY` 页面注册表
3. 业务页模板（按类型选择）：
   - 查询表格 / 列表页 → [`templates/list-table.md`](./templates/list-table.md)
   - 详情页 → [`templates/detail-basic.md`](./templates/detail-basic.md)
   - 表单弹窗 → [`templates/form-modal.md`](./templates/form-modal.md)
4. [`rules/components.md`](./rules/components.md) — 组件实现规则
5. [`rules/interaction.md`](./rules/interaction.md) — 交互与技术规则

完整索引见 [`templates/README.md`](./templates/README.md)。

### 技术栈优先级

| 优先级 | 场景 | 技术栈 |
| ---- | ---- | ---- |
| 1（默认） | 工程代码 | React + TypeScript + `antd` |
| 2 | HTML 原型 | React UMD + `antd` UMD（CDN 引入，真实组件） |
| 3 | 用户指定 | Vue 3 + `ant-design-vue` |

禁止用原生 `div` 模拟 Select / Table / Modal 等 Ant Design 组件。

### UI 走查

在 Cursor / Claude 中选中页面文件或粘贴代码，提示 AI：

```
按 ant-design-system 规范走查这个页面，检查间距、颜色 Token、组件用法和布局问题
```

AI 会读取规范并输出走查清单与修改建议。

### 自定义品牌

编辑 [`ant-design-system-prompt.md`](./ant-design-system-prompt.md) 中的 **品牌配置** 章节，修改主色、品牌名、Logo 等 Seed Token，派生色由算法自动生成。

修改后建议同步更新 `dist/ant-design-system.full.md`（合并版）。

### 新增业务页（HTML 原型）

1. 基于 [`admin-shell.html`](./admin-shell.html) 扩展
2. 在 `PAGE_REGISTRY` 中注册新页面 key、标题、组件
3. 将菜单项挂到 `Menu`，确保点击可切换内容区
4. 业务组件只渲染 `Content` 内部，不重复包 `Layout` / `Sider`

---

## 项目结构

```text
Ant-Design-Admin-Skill/
├── README.md                          # 本文件
├── ant-design-system-prompt.md        # 设计规范权威源（品牌 / Token / 布局 / 组件外观）
├── admin-shell.html                   # HTML 应用壳参考实现
├── template-list-page.html            # 列表页 HTML 示例
├── templates/                         # 页面模板
│   ├── README.md                      # 模板索引与读取规则
│   ├── app-shell.md                   # 后台应用壳（默认必读）
│   ├── list-table.md                  # 查询表格页
│   ├── detail-basic.md                # 详情页
│   └── form-modal.md                  # 表单弹窗（Modal + Form）
├── rules/                             # 通用规则
│   ├── README.md
│   ├── components.md                  # 组件使用规范（React + antd 默认）
│   └── interaction.md                 # 交互与技术规范
├── dist/
│   └── ant-design-system.full.md      # 单文件合并版（Gemini 等使用）
├── .cursor/skills/ant-design-system/  # Cursor Skill
│   └── SKILL.md
├── .claude/skills/ant-design-system/  # Claude Skill
│   └── SKILL.md
└── .trae/rules/                       # Trae IDE 项目规则
    └── ant-design-system.md
```

---

## 规范优先级

1. 品牌配置
2. 全局 Design Token
3. B 端页面布局指南
4. 组件级设计参数
5. 默认应用壳（`templates/app-shell.md`）
6. 组件使用规则（`rules/components.md`）
7. 业务页模板（`templates/`）
8. 查询表格页约束
9. 页面生成技术规则（`rules/interaction.md`）

---

## 示例 Prompt

**生成带菜单的列表页：**

```
在这个项目里，按 ant-design-system 规范生成「商品购物列表」查询表格页，
挂到 admin-shell 菜单里，字段包括商品名称、SKU、价格、库存、状态。
```

**生成独立内容页（无侧栏）：**

```
只要页面内容，不要左侧菜单。按 list-table 模板生成「站点映射」列表页，输出 HTML 单文件。
```

**UI 走查：**

```
对照 ant-design-system-prompt.md 走查 template-list-page.html，
列出间距、颜色、组件用法不符合规范的地方，并给出修改建议。
```

---

## 如何扩展为自己的 Skill

1. Fork 本仓库
2. 修改 `ant-design-system-prompt.md` 中的品牌配置与设计 Token
3. 在 `templates/` 下新增或调整业务页模板
4. 更新 `.cursor/skills/ant-design-system/SKILL.md` 的 `description`（触发关键词）
5. 重新生成 `dist/ant-design-system.full.md` 供单文件工具使用

---

## 关键词 / GitHub Topics

为便于在 GitHub 搜索，建议在仓库 Settings → Topics 中添加：

```
ant-design
ant-design-vue
admin-dashboard
b-end
back-office
cursor
cursor-skill
claude-skill
ai-prompt
design-system
design-tokens
react
typescript
html-prototype
ui-review
page-template
admin-template
management-system
prototype
figma-to-code
frontend
```

**检索关键词说明：**

| 关键词 | 关联内容 |
| ---- | ---- |
| `ant-design` / `antd` | Ant Design 5 组件与设计规范 |
| `cursor-skill` / `claude-skill` | AI Agent Skill 配置 |
| `admin-dashboard` / `b-end` | B 端后台管理系统 |
| `design-system` / `design-tokens` | 设计系统与 Token 规范 |
| `page-template` / `admin-template` | 列表 / 详情 / 表单页面模板 |
| `html-prototype` | CDN 直开 HTML 可交互原型 |
| `ui-review` | UI 规范走查 |
| `react` / `typescript` | 默认技术栈 |

---

## 相关链接

- [Ant Design 官方文档](https://ant.design/)
- [Ant Design Pro 布局参考](https://preview.pro.ant.design/)
- [Cursor Skills 文档](https://cursor.com/docs/context/skills)

---

## License

MIT
