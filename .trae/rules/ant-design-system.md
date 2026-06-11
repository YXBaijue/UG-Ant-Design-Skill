---
description: 生成、审查或调整任何基于 Ant Design 的 B 端后台页面（列表页、详情页、表单弹窗、表格、管理页、原型图）时，必须应用本规范。默认输出 app-shell（Layout + 左侧 Menu + Header + Content）+ PAGE_REGISTRY 多页切换；仅当用户明确要求「仅内容页 / 不要菜单 / 独立页面」时才跳过应用壳。
globs:
alwaysApply: false
---

# Ant Design 设计系统（Trae 项目规则）

> 本文件与 `.cursor/skills/ant-design-system/SKILL.md` 等价，供 Trae IDE 使用。
> 真正的设计规范权威源是项目根目录的 `ant-design-system-prompt.md` 及 `templates/`、`rules/` 目录，本规则只负责「触发 + 指引读取」。

## 目录结构

```text
project-root/
├── ant-design-system-prompt.md      # 样式权威源（品牌/Token/布局/组件外观）
├── admin-shell.html                 # HTML 原型应用壳参考
├── templates/
│   ├── README.md
│   ├── app-shell.md                 # 默认应用壳（Layout + Menu + 注册表）
│   ├── list-table.md                # 查询表格页
│   ├── detail-basic.md              # 详情页
│   └── form-modal.md                # 表单弹窗（Modal + Form）
├── rules/
│   ├── components.md                # 组件实现规则（默认 React + TS + antd）
│   └── interaction.md               # 交互/技术规则
└── dist/
    └── ant-design-system.full.md    # 单文件合并版（无法读多文件时回退用）
```

## 用法（强约束）

生成、审查或调整任何基于 Ant Design 的 UI 前，**先读项目根目录的 `ant-design-system-prompt.md`**，将其视为样式权威来源，不得改写、概括、放宽或覆盖，除非用户明确要求更新规范。

### 默认生成模式（强约束）

生成 **B 端后台页面** 时，**默认必须使用应用壳模板**，除非用户明确要求「仅内容页 / 不要菜单 / 独立页面 / no sidebar」。

1. **始终先读**：`templates/app-shell.md` —— `Layout` + 左侧 `Sider` + `Menu` + `Header` + `Content`，用 `PAGE_REGISTRY` 维护菜单切换。
2. **再读**对应业务页模板（`list-table.md` / `detail-basic.md` / `form-modal.md`）。
3. 新页面必须写入 `PAGE_REGISTRY` 并挂上 `Menu` 项；点击菜单必须真实切换内容区，禁止死菜单。
4. HTML 原型：基于 `admin-shell.html` 扩展，或使用单文件内「组件注册表」模式（CDN 顺序：`react` → `react-dom` → `dayjs` → `antd` → `babel`）。
5. **仅当**用户明确说「仅内容页 / 不要菜单 / 独立页面 / no sidebar」时，才跳过应用壳。

### 业务页模板选择

先读 `templates/README.md`，再按页面类型读对应模板：

- **默认 —— 后台应用壳**：`templates/app-shell.md`（Layout + Menu + 页面注册表）。
- 查询表格页、列表页、表格 + 查询：`templates/list-table.md`。
- 详情页、基础详情、Profile：`templates/detail-basic.md`。
- 表单弹窗、新增/编辑对话框、基础表单：`templates/form-modal.md`（必须用 `Modal` + `Form`）。

### 每个页面都要遵循

- `rules/components.md`：使用真实 Ant Design 组件；默认 **React + TypeScript + antd**；用户要 HTML 时通过 CDN 引入 React + antd 用真实组件（禁止用 div 模拟）；仅当用户要求时才用 Vue。
- `rules/interaction.md`：页面必须可交互、可点击（非静态）；Tailwind 仅用于布局，不得替代 antd 组件。图标优先 Lucide（HTML CDN：`https://unpkg.com/lucide@1.17.0/dist/umd/lucide.js`），其次 Ant Design Icons。

## 规范应用优先级

1. 品牌配置
2. 全局 Design Token
3. B 端页面布局指南
4. 组件级外观参数
5. **默认应用壳**（Layout + Menu）见 `templates/app-shell.md`（除非用户主动放弃）
6. 组件使用规则（默认 React + antd）见 `rules/components.md`
7. 业务页模板见 `templates/`（list-table / detail-basic / form-modal）
8. 查询表格页约束
9. 页面生成技术规则（可交互 + Tailwind 辅助）见 `rules/interaction.md`

## 回退方案

- 若 Trae 当前模式无法读取多个项目文件，改为读取并遵循单文件合并版 `dist/ant-design-system.full.md`（已合并样式 + 模板 + 规则）。
- 若上述规范文件均不可用，先向用户索取当前 Ant Design 设计规范，再继续，不得自由发挥布局。
