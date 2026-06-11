# 组件使用规范（强约束）

生成页面时，必须优先使用 Ant Design 官方组件库实现 UI，禁止用原生 HTML + 自定义 JS 模拟 Ant Design 组件行为。

## 0. 默认输出形态（强约束）

生成 **B 端后台页面** 时，**默认输出带左侧菜单的完整后台**，而非孤立的内容单页：

| 默认 | 要求 |
| ---- | ---- |
| 应用壳 | `Layout` + `Sider` + `Menu` + `Header` + `Content`（见 `templates/app-shell.md`） |
| 生成顺序 | 先壳层与 `PAGE_REGISTRY`，再业务页（`list-table.md` 等） |
| 菜单切换 | 点击菜单须真实切换 `Content` 内页面 |
| HTML 参考 | 扩展 `admin-shell.html` |
| 例外 | 仅当用户明确「不要菜单 / 仅内容页 / 独立页面」时，才可省略应用壳 |

## 1. 技术栈优先级

| 优先级 | 技术栈 | 组件库 | 说明 |
| ---- | ---- | ---- | ---- |
| 1（默认） | React + TypeScript（`.tsx`） | `antd` + `lucide-react`（图标首选） | 用户未指定框架时，一律默认 React 写法；图标次选 `@ant-design/icons` |
| 2 | HTML 单文件 + CDN 在线引入 | React UMD + `antd` UMD + Lucide UMD | 用户明确要求「HTML 页面」时使用；图标 CDN：`unpkg.com/lucide@1.17.0/dist/umd/lucide.js` |
| 3 | Vue 3 + TypeScript（`.vue`） | `ant-design-vue` + `lucide-vue-next`（图标首选） | 仅当用户明确要求 Vue 时使用；图标次选 `@ant-design/icons-vue` |
| 4（最后兜底） | 纯 HTML + 原生 JS 模拟 | 不推荐 | 仅当无法使用 CDN（离线、内网限制）时；须注释说明为降级方案 |

禁止在未获用户要求时默认输出 Vue；禁止在有条件使用 CDN 时仍用手写 div 模拟 Select/Table/Modal。

## 2. HTML 页面也必须用真实 antd 组件

`antd` 是 React 组件库，但 **HTML 单文件可通过 CDN 在线引入 React 与 antd**，照样使用 `<Select>`、`<Table>`、`<Modal>` 等真实组件。

用户要求生成 HTML 页面时，必须按以下方式组织：

```html
<!-- HTML 单文件 CDN（推荐 jsDelivr，支持 file:// 直开；禁止加 crossorigin） -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/antd@5/dist/reset.css" />
<!-- React 19 已移除 UMD 包，HTML 场景用 React 18.3.1 UMD（antd 5 兼容 React>=18）；工程 .tsx 仍默认 React 19 -->
<script src="https://cdn.jsdelivr.net/npm/react@18.3.1/umd/react.production.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/react-dom@18.3.1/umd/react-dom.production.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/dayjs@1/dayjs.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/antd@5/dist/antd-with-locales.min.js"></script>
<script src="https://unpkg.com/lucide@1.17.0/dist/umd/lucide.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@babel/standalone@7/babel.min.js"></script>
<!-- 用 type="text/babel" data-presets="react" 编写 JSX，ReactDOM.createRoot 挂载 -->
```

要求：

- HTML 页面内必须使用 `antd` 的 `Form`、`Select`、`Table`、`Button`、`Modal`、`Card`、`ConfigProvider` 等标准组件。
- 主题通过 `ConfigProvider` 注入品牌 `colorPrimary` 等 Seed Token。
- **图标优先**在线引入 Lucide：`https://unpkg.com/lucide@1.17.0/dist/umd/lucide.js`；其次才用 `@ant-design/icons`（CDN UMD）。禁止 Unicode 字形替代。
- Tailwind 仅作外层布局辅助（可选 CDN），不得替代 antd 组件本身。
- 仅当 CDN 完全不可用时，才允许原生 JS 模拟，并须在文件头部注释标明「降级原型，非 antd 组件」。

项目工程页面仍优先输出 **React `.tsx`**；HTML + CDN 适用于快速原型预览、Skill 效果验证等场景。

## 3. 增删改查组件映射（必须使用）

生成 B 端增删改查页面时，必须按下表选用 Ant Design 组件，不得自行发明等价控件：

| 场景 | React（antd） | Vue（ant-design-vue） |
| ---- | ---- | ---- |
| 查询表单容器 | `Form` + `Row` + `Col` | `a-form` + `a-row` + `a-col` |
| 文本输入 | `Input` | `a-input` |
| 多行/批量文本 | `Input.TextArea` | `a-textarea` |
| 单选下拉 | `Select` | `a-select` |
| 多选下拉 | `Select mode="multiple"` | `a-select mode="multiple"` |
| 日期区间 | `DatePicker.RangePicker` | `a-range-picker` |
| 查询/重置按钮 | `Button` default / `Button type="primary"` | `a-button` / `a-button type="primary"` |
| 展开更多筛选 | `Button type="link"` + 图标 | `a-button type="link"` + 图标 |
| 数据表格 | `Table`（含 `rowSelection`、排序、分页） | `a-table` |
| 表格工具栏 | `Button`、`Space`、`Dropdown` | `a-button`、`a-space`、`a-dropdown` |
| 分页 | `Table` 内置 `pagination` 或 `Pagination` | `a-table` 的 `pagination` |
| 状态展示 | `Badge` 或 `Tag` | `a-badge` / `a-tag` |
| 新增/编辑表单页 | `Form` + `Card` | `a-form` + `a-card` |
| 新增/编辑弹窗 | `Modal` + `Form`（结构见 `templates/form-modal.md`） | `a-modal` + `a-form` |
| 新增/编辑抽屉 | `Drawer` + `Form` | `a-drawer` + `a-form` |
| 详情展示 | `Descriptions` + `Card` | `a-descriptions` + `a-card` |
| 操作确认 | `Modal.confirm` / `Popconfirm` | `Modal.confirm` / `a-popconfirm` |
| 操作反馈 | `message` / `notification` | `message` / `notification` |
| 日志/记录 | `Table` 或 `Timeline` | `a-table` / `a-timeline` |

## 4. 后台应用壳（Layout + Menu）必选组件组合

多页后台系统必须参考 `templates/app-shell.md`，使用 antd `Layout` + `Menu` 维护页面注册表与菜单切换。

```tsx
// 应用壳 + 页面注册（不得用 div 模拟侧栏）
const PAGE_REGISTRY = {
  'page-key': { label: '页面名', breadcrumb: ['模块', '页面名'], component: PageComponent },
};

<Layout style={{ minHeight: '100vh' }}>
  <Sider width={208} theme="light" collapsible>
    <Menu mode="inline" selectedKeys={[activeKey]} items={menuItems} onClick={({ key }) => setActiveKey(key)} />
  </Sider>
  <Layout>
    <Header style={{ height: 48, background: '#fff' }}>{/* 面包屑 */}</Header>
    <Content style={{ background: '#f5f5f5' }}>
      <ActivePage />
    </Content>
  </Layout>
</Layout>
```

- 新增业务页须同时更新 **页面注册表**、**Menu 项** 与 **业务组件**。
- 业务页只渲染 `Content` 内部，遵循 `list-table.md` 等模板，禁止内嵌第二套 `Sider`。
- HTML 原型示例：`admin-shell.html`（菜单切换 + iframe 挂载独立业务页）。

## 5. 列表页（TableList）必选组件组合

查询表格页必须使用以下组件组合，结构参考 `templates/list-table.md`：

```tsx
// React 默认示例结构（须按此思路组织，不得换成 div 模拟）
<PageContainer title="页面标题">
  <Card>
    <Form layout="inline">
      <Form.Item label="字段"><Select /></Form.Item>
      <Form.Item><Button>重置</Button><Button type="primary">查询</Button></Form.Item>
    </Form>
  </Card>
  <Card title="列表标题" extra={<Space><Button type="primary" icon={<PlusOutlined />}>新增</Button>...</Space>}>
    <Table rowSelection columns pagination />
  </Card>
</PageContainer>
```

- 查询区：`Form` + `Row` + `Col flex="none"` + `Select` / `Input` / `RangePicker`；**内边距 `20px`**（§2.5）、**控件宽 `260px`**（§2.2）、**横向间距 `50px`、展开行距 `20px`**（§2.3）、**操作区同行居中**（§2.4）、**展开/收起自适应**（§2.5）；见 `templates/list-table.md`。
- 表格区：`Table` + `rowSelection` + `pagination`，禁止手写 `<table>` 模拟 antd Table。
- 工具栏：`Button`、`Dropdown`、`Tooltip` 搭配 Lucide 图标（首选）或 `@ant-design/icons`（次选）。
- 主题：通过 `ConfigProvider` 注入品牌 `colorPrimary` 等 Seed Token。

## 6. 表单弹窗（Modal + Form）必选组件组合

新增/编辑弹窗、基础表单必须参考 `templates/form-modal.md`，严格使用 `Modal` + `Form`，禁止自定义 div 弹层或裸 HTML 表单。

```tsx
// React 默认示例（须按此思路组织，不得换成 div 模拟）
<Modal
  title={isEdit ? '编辑{对象}' : '新建{对象}'}
  open={open}
  width={520}
  destroyOnClose
  maskClosable={false}
  onCancel={() => setOpen(false)}
  onOk={async () => {
    const values = await form.validateFields();
    await handleSubmit(values);
  }}
>
  <Form form={form} layout="horizontal" labelCol={{ span: 6 }} wrapperCol={{ span: 16 }}>
    <Form.Item label="字段" name="field" rules={[{ required: true, message: '请输入' }]}>
      <Input placeholder="请输入" />
    </Form.Item>
  </Form>
</Modal>
```

- 弹窗：**必须** `Modal`；表单：**必须** `Form`；字段：**必须** antd 标准控件。
- `onOk` 内**必须** `validateFields()`，校验失败不得关闭弹窗。
- 编辑须 `setFieldsValue` 回显；新建须 `resetFields`；关闭建议 `destroyOnClose`。
- 全页基础表单用 `Card` + `Form`，布局对齐 [Pro Basic Form](https://preview.pro.ant.design/form/basic-form)。

## 7. 无对应组件时的兜底规则

仅当 Ant Design 没有直接对应组件，或业务明确要求自定义控件时，才允许自行实现：

- 视觉必须遵循 `ant-design-system-prompt.md` 中的 Design Token 与组件级参数。
- 交互必须遵循 `rules/interaction.md`。
- 布局间距优先用 Tailwind 辅助，但不得替代 antd 组件本身。
- 自定义实现须在代码注释中说明「为何未使用 antd 标准组件」。

## 8. 禁止项

- 禁止用原生 `<select>`、自定义 `div` 下拉、手写 `<table>` 替代 antd 的 `Select`、`Table`；HTML 页面应通过 CDN 引入 React + antd 使用真实组件。
- 禁止用自定义 `div` 遮罩弹层替代 `Modal`；禁止在弹窗内不用 `Form` 直接堆表单控件。
- 禁止用 Tailwind 重写一套「仿 antd」组件库替代真实 antd 组件。
- 禁止在 React/Vue 项目中混用「手写 UI + 少量 antd」；应用尽用 antd 标准组件。
- 禁止默认输出 Vue；无要求时一律 React。
