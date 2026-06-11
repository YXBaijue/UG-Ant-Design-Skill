# 表单弹窗模板：Modal + Form（Basic Form）

当用户要求生成「新增弹窗」「编辑弹窗」「表单弹窗」「基础表单」「Basic Form」等 B 端新增/编辑交互时，必须严格按照 Ant Design Pro 的 [Basic Form](https://preview.pro.ant.design/form/basic-form) 表单形态，并**强制**使用 `antd` 的 `Modal` + `Form` 组合实现。

参考：https://preview.pro.ant.design/form/basic-form

## 1. 总体原则（强约束）

| 规则 | 要求 |
| ---- | ---- |
| 弹窗容器 | **必须**使用 `Modal`（React）/ `a-modal`（Vue），禁止 `div` 遮罩、自定义 Popup、Drawer 冒充 Modal（用户明确要求抽屉时除外） |
| 表单容器 | **必须**使用 `Form`（React）/ `a-form`（Vue），禁止在 Modal 内用手写 `label` + `input` 堆表单 |
| 表单控件 | 字段必须使用 antd 标准控件（`Input`、`Select`、`DatePicker` 等），禁止原生 HTML 控件替代 |
| 布局参考 | 字段排版、标签对齐、间距、校验提示均参考 Pro Basic Form |
| 交互 | 打开、关闭、校验、提交必须真实生效，不得为静态展示 |

列表页中的「新增」「编辑」默认使用 **Modal + Form** 弹窗形态；独立「基础表单页」使用 **Card + Form** 全页形态（见 §5）。

## 2. Modal 弹窗结构

弹窗必须由以下部分组成，顺序不得改变：

1. **标题区**：`Modal` 的 `title`，文案为「新建{业务对象}」或「编辑{业务对象}」
2. **表单区**：`Modal` 内容区内的 `Form`，承载全部可编辑字段
3. **底部操作区**：`Modal` 默认页脚，「取消」+「确定/提交」

### 2.1 Modal 属性（强约束）

| 属性 | 要求 |
| ---- | ---- |
| `open` / `visible` | 受控开关，由 `useState` / `ref` 管理 |
| `title` | 新建/编辑语义明确，如「新建备货站点」「编辑备货站点」 |
| `width` | 默认 `520`；字段较多或含 TextArea 时用 `640`～`720` |
| `destroyOnClose` | **必须**为 `true`，关闭后销毁表单状态 |
| `maskClosable` | 新增/编辑表单建议 `false`，避免误关丢失数据 |
| `onCancel` | 关闭弹窗；配合 `destroyOnClose` 或手动 `resetFields` |
| `onOk` | 触发表单 `validateFields()`，校验通过后再提交；禁止不经校验直接关闭 |
| `okText` | 默认「确定」或「提交」 |
| `cancelText` | 默认「取消」 |
| `confirmLoading` | 异步提交时展示加载态 |

禁止：

- 禁止用自定义 `position: fixed` 的 `div` 模拟 Modal 标题栏与遮罩
- 禁止把提交按钮放在 Modal 内容区底部而省略 Modal 页脚（除非用户明确要求自定义 footer，且仍须 `Form` + 校验）
- 禁止 Modal 内嵌第二个 Modal 作为表单容器

### 2.2 标准结构示例

```tsx
const [open, setOpen] = useState(false);
const [form] = Form.useForm();

<Modal
  title={editingId ? '编辑备货站点' : '新建备货站点'}
  open={open}
  width={520}
  destroyOnClose
  maskClosable={false}
  onCancel={() => setOpen(false)}
  onOk={async () => {
    const values = await form.validateFields();
    await submit(values);
    setOpen(false);
  }}
>
  <Form form={form} layout="horizontal" labelCol={{ span: 6 }} wrapperCol={{ span: 16 }}>
    <Form.Item label="补货平台" name="platform" rules={[{ required: true, message: '请选择补货平台' }]}>
      <Select placeholder="请选择" options={platformOptions} />
    </Form.Item>
    <Form.Item label="补货站点" name="site" rules={[{ required: true, message: '请输入补货站点' }]}>
      <Input placeholder="请输入" />
    </Form.Item>
    <Form.Item label="备注" name="remark">
      <Input.TextArea placeholder="请输入" rows={3} />
    </Form.Item>
  </Form>
</Modal>
```

## 3. Form 表单布局（对齐 Basic Form）

表单区必须严格使用 Pro Basic Form 的横向表单规范：

| 属性 | 要求 |
| ---- | ---- |
| 布局 | `layout="horizontal"`（默认）；弹窗内禁止 `layout="inline"` |
| 标签位置 | 标签在控件左侧 |
| `labelCol` | 弹窗内推荐 `{ span: 6 }`；全页表单推荐 `{ span: 4 }` 或 `{ span: 5 }` |
| `wrapperCol` | 弹窗内推荐 `{ span: 16 }` 或 `{ span: 18 }`；全页表单推荐 `{ span: 14 }`～`{ span: 16 }` |
| 字段排列 | 单列纵向堆叠，一项一行；禁止多列表单挤在同一行（除非用户明确要求双列） |
| 表单项间距 | 遵循 antd 默认 `marginBottom`（通常 `24px`） |
| 占位符 | 文本「请输入」；枚举「请选择」；与 `list-table.md` 查询区一致 |
| 必填标识 | 使用 `Form.Item` 的 `rules` 声明 `required`，由 antd 自动展示红星 |
| 校验时机 | 提交时校验；`onOk` 内 `await form.validateFields()` |

### 3.1 字段控件映射

与查询表格、Basic Form 保持一致，按语义选控件：

| 字段语义 | 控件 | 规则 |
| ---- | ---- | ---- |
| 普通文本 | `Input` | `placeholder="请输入"` |
| 长文本、备注 | `Input.TextArea` | `rows={3}` 或 `4` |
| 枚举单选 | `Select` | `placeholder="请选择"` |
| 枚举多选 | `Select mode="multiple"` | 允许 `allowClear` |
| 日期 | `DatePicker` | 单日期 |
| 日期区间 | `DatePicker.RangePicker` | 开始/结束占位 |
| 数值 | `InputNumber` | 宽度 `100%` 或固定 |
| 开关 | `Switch` | 用于启用/禁用类字段 |

编辑回显：打开编辑弹窗前，须 `form.setFieldsValue(record)`；新建前须 `form.resetFields()`。

## 4. 提交流程（强约束）

新增/编辑弹窗的提交流程必须如下，不得省略：

1. 用户点击「确定」→ 触发 `onOk`
2. `await form.validateFields()` → 失败则停留弹窗并展示字段错误
3. 校验通过 → 调用新增/更新逻辑（接口或本地 state）
4. 成功 → `message.success` 反馈 → 关闭弹窗 → 刷新列表数据
5. 失败 → `message.error` 反馈 → 保持弹窗打开

取消流程：

1. 用户点击「取消」或关闭图标 → `onCancel` → 关闭弹窗
2. 配合 `destroyOnClose` 或 `form.resetFields()` 清空脏数据

危险操作（删除等）使用 `Modal.confirm` / `Popconfirm`，不混用在编辑表单 Modal 内。

## 5. 全页基础表单（Card + Form）

当用户要求独立「基础表单页」（非弹窗）时，仍须参考 [Basic Form](https://preview.pro.ant.design/form/basic-form)：

| 区域 | 要求 |
| ---- | ---- |
| 页面标题区 | 显示表单页标题，如「基础表单」 |
| 表单卡片 | 白色 `Card` 包裹 `Form` |
| 布局 | `layout="horizontal"`，`labelCol={{ span: 4 }}`，`wrapperCol={{ span: 14 }}` |
| 提交区 | 表单底部左侧：`Button type="primary"` 提交 + `Button` 重置，间距 `8px`～`16px` |
| 禁止 | 禁止无 Card 裸 Form 铺满页面；禁止提交按钮居中或放右上角 |

全页表单与弹窗表单共用 §3 字段控件映射与校验规则；仅容器从 `Modal` 换为 `Card`。

## 6. 与列表页的配合

查询表格页（`list-table.md`）中的新增/编辑：

- 工具栏「新增」→ 打开新建 `Modal` + `Form`
- 行操作「编辑」→ 打开编辑 `Modal` + `Form` 并回显
- 列表查询区用 `Form` + `Row` + `Col`；**弹窗内**用 `Form` 单列横向布局，二者不得混用同一 `form` 实例

## 7. 禁止项

生成表单弹窗时禁止出现以下情况：

- 禁止用 `div` + CSS 自定义弹层替代 `Modal`
- 禁止在 Modal 内不用 `Form`，直接堆 `Input` / `label`
- 禁止用原生 `<input>`、`<select>`、`<textarea>` 替代 antd 控件
- 禁止 `onOk` 不校验直接提交或关闭
- 禁止编辑弹窗不回显、新建弹窗不清空上次数据
- 禁止弹窗内 `layout="inline"` 横向挤多个字段
- 禁止省略 `destroyOnClose` 导致关闭后脏数据残留
- 禁止硬编码与品牌配置冲突的主题色
