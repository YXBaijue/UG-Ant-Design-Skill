# 页面生成技术规范（强约束）

本规范适用于所有由 AI 生成的页面，优先级高于具体组件样式，但不得违反品牌配置、全局 Design Token 与 `rules/components.md` 的组件选用规则。

**默认技术栈**：React + TypeScript + `antd`（见 `rules/components.md`）。用户要求 HTML 页面时，通过 CDN 在线引入 React + antd 使用真实组件，不得默认 Vue 或原生 JS 模拟组件。

## 1. 交互要求

生成的页面必须是「可交互、可点击」的真实原型，禁止只输出静态展示页面。以下交互必须真实生效：

| 元素 | 必须具备的交互 |
| ---- | ---- |
| 下拉选择（Select） | 可点击展开/收起；单选可选中切换；多选可多选并以 Tag 展示；可清除已选 |
| 复选框（Checkbox） | 可勾选/取消；表头全选可联动所有行；半选态正确 |
| 表格排序列 | 点击在 升序 / 降序 / 无序 间切换，数据真实重排 |
| 分页（Pagination） | 可点击页码、上一页/下一页切换；可切换每页条数；当前页数据真实变化 |
| 查询 / 重置 | 查询按筛选条件过滤数据；重置清空筛选并恢复列表 |
| 展开 / 收起 | 可切换显示更多筛选条件 |
| 主操作按钮 | 点击有明确反馈（如打开弹窗/抽屉、提示、跳转），不得是死按钮 |
| 行操作（编辑/日志/删除等） | 点击有反馈；危险操作需二次确认 |
| 弹窗 / 抽屉 | 可打开、可关闭、可取消/确认；表单弹窗须 `Modal` + `Form`，确定时校验并提交 |
| hover / active | 按钮、链接、行需有明确视觉反馈 |

实现要求：

- React 页面（默认，`.tsx`）：用 `useState` / hooks 管理状态，`antd` 组件驱动视图；列表筛选、排序、分页作用于同一份数据源。
- HTML 页面（用户明确要求时）：通过 CDN 引入 React + antd，用 `type="text/babel"` 或 ESM 编写 JSX，挂载真实 antd 组件；交互逻辑同上。
- Vue 页面（用户指定时）：用 `ref` / `reactive` + `ant-design-vue` 组件实现，规则同上。
- 原生 JS 模拟（最后兜底）：仅当 CDN 完全不可用时；须在文件头部注释标明降级方案，不得冒充已使用 antd 组件。

## 2. 样式技术写法：Tailwind CSS（辅助）

Tailwind 用于辅助布局与间距，**不得替代 antd 组件**：

- React/Vue 项目：优先用 antd 组件自带样式 + `ConfigProvider` 主题；Tailwind 仅用于页面级布局（如 `flex`、`gap`、`p-6`）。
- HTML + CDN React + antd 原型：Tailwind 可选 CDN 作外层布局；antd 样式与主题以 `ConfigProvider` + antd CSS 为准。
- 禁止用 Tailwind 手写一套仿 antd 的 Select、Table、Modal 来替代真实 antd 组件。
- 禁止大面积内联 `style`；仅在 Tailwind 与 antd 均无法表达的个别场景下使用少量自定义样式。
- 设计 Token 必须映射为 Tailwind 主题变量，禁止散落硬编码色值：

| Token | Tailwind 映射建议 |
| ---- | ---- |
| colorPrimary | `colors.primary`（如 `bg-primary` `text-primary` `border-primary`） |
| colorBgLayout | `colors.layout` 或 `bg-[#f5f5f5]` 统一变量 |
| colorBgContainer | 容器白底 |
| 表头背景 #FAFAFA | 统一表头底色变量 |
| 间距 4px 体系 | Tailwind spacing（`p-4`=16px、`p-6`=24px 等），保持 4px 网格 |
| 圆角 6px/8px | `rounded-md` / `rounded-lg`（必要时自定义） |
| 控件高度 32px | `h-8` |

- 主色及派生色以品牌配置为准；与示例稿主色不一致时以品牌配置为准，不得为还原示例硬编码冲突色值。

## 3. 图标渲染（强约束）

所有 UI 图标必须使用可靠的矢量方案，禁止用 Unicode 字形符号充当图标。

### 优先级

1. **Lucide Icons（首选）**
2. **Ant Design Icons（次选）**：`@ant-design/icons` / `@ant-design/icons-vue`
3. 仅当用户明确要求其他图标库、自定义图标或品牌专属图标时，才偏离上述默认。

### Lucide 在线引入（HTML / CDN 场景）

HTML 单文件或需 CDN 直引时，必须在线引入 Lucide UMD：

```html
<script src="https://unpkg.com/lucide@1.17.0/dist/umd/lucide.js"></script>
```

引入后通过全局 `lucide` 使用图标，例如 `lucide.icons.plus`、`lucide.icons.refresh-cw`，或配合 `data-lucide` + `lucide.createIcons()`。禁止手写 SVG path 或使用 Unicode 字形替代。

### 常用语义映射（Lucide → Ant Design 备选）

| 语义 | Lucide | Ant Design（次选） |
| ---- | ---- | ---- |
| 下拉箭头 | `chevron-down` | `DownOutlined` |
| 排序上/下 | `chevron-up` / `chevron-down` | `CaretUpOutlined` / `CaretDownOutlined` |
| 关闭 | `x` | `CloseOutlined` |
| 新增 | `plus` | `PlusOutlined` |
| 刷新 | `refresh-cw` | `ReloadOutlined` |
| 密度 | `rows-3` | `ColumnHeightOutlined` |
| 列设置 | `settings` | `SettingOutlined` |
| 上一页/下一页 | `chevron-left` / `chevron-right` | `LeftOutlined` / `RightOutlined` |
| 搜索 | `search` | `SearchOutlined` |

### 工程化项目

- React `.tsx` 默认优先 `lucide-react`；其次 `@ant-design/icons`。
- Vue `.vue` 默认优先 `lucide-vue-next`；其次 `@ant-design/icons-vue`。
- 不要自行定义图标 SVG path，除非用户有特殊要求。

### 禁止项

- 禁止使用：`▾` `▲` `▼` `✓` `×` `⟳` `≡` `⚙` 等 Unicode 字形符号作为 UI 图标。
- 图标颜色用 `currentColor` 或 Lucide/antd 标准方式控制；默认图标建议 `colorTextTertiary`（`rgba(0,0,0,0.45)`）级别，保证可见。

## 4. 与页面模板、组件规范的关系

- 页面结构遵循 `templates/` 下对应页面模板。
- 组件选用遵循 `rules/components.md`：增删改查必须用 antd 标准组件。
- 本规范约束「交互行为」和「样式辅助写法」，不改变模板规定的页面结构与区域顺序。
