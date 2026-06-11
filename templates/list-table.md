# 查询表格页模板：TableList

当用户要求生成「列表页」「查询表格」「表格+查询」「管理列表」「TableList」等 B 端列表页面时，必须严格按照 Ant Design Pro 的 TableList 页面形态生成。

参考：https://preview.pro.ant.design/list/table-list

## 1. 总体结构

查询表格页面必须由以下区域组成，顺序不得改变：

1. 页面标题区：显示页面标题，例如「查询表格」。
2. 查询表单卡片：位于表格上方，白色背景。
3. 表格卡片：包含表格标题、工具栏、数据表格和分页。

页面背景使用 `colorBgLayout`，卡片背景使用 `colorBgContainer`。主题色必须使用品牌配置中的 `colorPrimary`，不得为了还原示例而硬编码 Ant Design Pro 默认蓝色。

## 2. 查询表单卡片

查询区必须严格使用横向表单布局：

| 属性 | 要求 |
| ---- | ---- |
| 布局 | 单行横向排列，空间不足时按栅格换行 |
| 背景 | 白色卡片背景 |
| 内边距 | **`20px`** 上下左右（强约束，见 §2.5） |
| 控件高度 | `32px` |
| 控件宽度 | **固定 `260px`**（强约束，见 §2.2） |
| 标签位置 | 标签在控件左侧，右对齐 |
| 标签宽度 | 同一行标签视觉宽度保持一致 |
| 表单项间距 | 横向 **`50px`**（§2.3）；展开后行间距 **`20px`**（§2.3） |
| 操作区 | 固定在查询行右侧 |

查询区操作按钮必须按以下顺序排列（靠右对齐）：

1. `重置`：默认按钮
2. `查询` 或 `搜索`：主按钮，使用 `colorPrimary`
3. `展开` / `收起`：链接按钮（`type="link"`），带箭头图标

### 2.1 展开 / 收起查询（强约束）

当查询条件较多、超过首屏默认可容纳数量时，**必须**实现与 [Ant Design Pro TableList](https://preview.pro.ant.design/list/table-list) 一致的展开/收起交互，不得把所有筛选项无折叠地堆满多行。

| 规则 | 要求 |
| ---- | ---- |
| 触发条件 | **自适应**：仅当容器宽度内**排不下**全部筛选项时才显示「展开/收起」；**禁止**写死固定展示 3 个或按字段序号硬编码隐藏 |
| 默认状态 | **收起**：在首行尽可能多展示筛选项（能放几个放几个），放不下的隐藏 |
| 展开状态 | 展示**全部**筛选项，按行自动换行 |
| 无需折叠 | 若首行能容纳全部筛选项 + 操作按钮，**不显示**「展开/收起」，只保留「重置」「查询」 |
| 按钮文案 | 收起态显示 `展开` + 向下箭头；展开态显示 `收起` + 向上箭头 |
| 按钮类型 | `Button type="link"`，与「重置」「查询」同一操作区 |
| 按钮顺序 | `重置` → `查询` → `展开`/`收起`（三者始终相邻，靠右） |
| 图标 | 优先 Lucide：`chevron-down`（展开）、`chevron-up`（收起）；次选 `@ant-design/icons` 的 `DownOutlined` / `UpOutlined` |
| 布局 | 使用 `Form` + `Row` + `Col`；筛选项 `Col` 按内容宽度（`flex="none"`），**禁止** `span={8}` 等比例分栏（见 §2.4） |
| 操作区对齐 | 收起态：操作按钮与首行筛选项**同一行**、**垂直居中**（`Row align="middle"`），**禁止**下沉到筛选项下方（见 §2.4） |
| 交互 | 点击「展开/收起」须真实切换表单项显示/隐藏，不得为死按钮 |

布局示意（与 Pro TableList 一致）：

```
收起态（默认）：
┌──────────┬──────────┬──────────┬─────────────────────┐
│ 字段1    │ 字段2    │ 字段3    │ [重置][查询][展开 ▼] │
└──────────┴──────────┴──────────┴─────────────────────┘

展开态：
┌──────────┬──────────┬──────────┐
│ 字段1    │ 字段2    │ 字段3    │
├──────────┼──────────┼──────────┤
│ 字段4    │ 字段5    │          │
├──────────────────────────────┴─────────────────────┤
│                        [重置][查询][收起 ▲]         │
└────────────────────────────────────────────────────┘
```

收起时操作按钮与首行筛选项同一行靠右；展开时操作按钮位于所有筛选项最后一行靠右。

是否显示「展开/收起」由**容器宽度实时计算**，不得按字段个数写死。窗口缩放须重新计算（`ResizeObserver`）。

### 2.2 查询控件宽度（强约束）

查询表单卡片内，**所有**筛选控件的宽度必须统一为 **`260px`**，不得因字段类型或使用场景而使用不同宽度。

| 规则 | 要求 |
| ---- | ---- |
| 统一宽度 | `Input`、`Select`、`DatePicker`、`RangePicker`、`InputNumber`、`Cascader` 等查询区控件均为 **`260px`** |
| 实现方式 | React：`style={{ width: 260 }}` 或 `className` 统一变量；Vue：`style="width: 260px"` |
| RangePicker | 区间选择器宽度同为 **`260px`**，禁止 `width: 100%` 撑满列宽 |
| 多选 Select | 多选下拉宽度仍为 **`260px`**，不得用 `minWidth` 替代 |
| 适用范围 | 仅查询表单卡片内的筛选条件；弹窗表单、全页编辑表单不受此条约束 |
| 禁止 | 禁止 `100%`、`auto`、不设宽度由列栅格撑开、各字段分别设置 `220px`/`160px` 等不一致宽度 |

```tsx
// 查询区控件宽度常量（推荐）
const FILTER_CONTROL_WIDTH = 260;

<Form.Item label="状态" name="status">
  <Select style={{ width: FILTER_CONTROL_WIDTH }} placeholder="请选择" />
</Form.Item>
<Form.Item label="创建时间" name="createTimeRange">
  <DatePicker.RangePicker style={{ width: FILTER_CONTROL_WIDTH }} />
</Form.Item>
```

### 2.3 查询表单项间距（强约束）

查询表单卡片内，**同一行**相邻筛选项横向间距 **50px**；**展开后**多行筛选项之间纵向行间距 **20px**（写死，不得与横向混用 50px）。

| 规则 | 要求 |
| ---- | ---- |
| 横向间距 | 同一行相邻筛选项之间 **50px**（如「补货平台」与「补货站点」之间） |
| 纵向行间距 | 展开后第 1 行与第 2 行、第 2 行与操作按钮行之间均为 **20px**（写死） |
| 横向实现 | `Row gutter={[50, 0]}`；Vue：`a-row :gutter="[50, 0]"` |
| 纵向实现 | 展开态多 `Row` 时：`style={{ marginTop: 20 }}`（首行 `0`）；常量 `FILTER_ROW_GAP = 20` |
| 常量 | `FILTER_GUTTER = 50`（横向）；`FILTER_ROW_GAP = 20`（展开纵向行距） |
| 配合栅格 | `Col flex="none"`；表单项 `marginBottom: 0`；**禁止**用 `gutter` 纵向分量代替行间距 |
| 适用范围 | 仅查询表单卡片内筛选项；弹窗表单不受此条约束 |
| 禁止 | 禁止展开后行间距使用 `50px` 或与 `FILTER_GUTTER` 混用；禁止 `gutter={[50, 50]}` 撑大行距 |

```tsx
const FILTER_CONTROL_WIDTH = 260;
const FILTER_GUTTER = 50;
const FILTER_ROW_GAP = 20;

<Row gutter={[FILTER_GUTTER, 0]} align="middle" wrap={false}>
  <Col flex="none">
    <Form.Item label="补货平台" name="platforms" style={{ marginBottom: 0 }}>
      <Select style={{ width: FILTER_CONTROL_WIDTH }} />
    </Form.Item>
  </Col>
  <Col flex="none">
    <Form.Item label="补货站点" name="sites" style={{ marginBottom: 0 }}>
      <Select style={{ width: FILTER_CONTROL_WIDTH }} />
    </Form.Item>
  </Col>
</Row>
```

### 2.4 查询区栅格与操作区对齐（强约束）

查询区常见布局错误：使用 `Col span={8}` 三等分整行，控件仅 `260px` 宽，列内大量留白，导致筛选项之间**视觉间距远大于 50px**；操作区 `Col` 换行后**重置/查询/展开**下沉到筛选项下方。

| 规则 | 要求 |
| ---- | ---- |
| 筛选项列宽 | `Col flex="none"`（或不设 `span`），列宽 = 标签宽度 + 控件 `260px`，**禁止** `span={8}` / `span={12}` 按比例撑满 |
| 横向净间距 | 仅由 `Row gutter={[50, 0]}` 控制，相邻两列「标签+控件」外缘之间为 **50px** |
| 展开行间距 | 多行筛选项之间、末行筛选项与操作按钮行之间均为 **`marginTop: 20`** |
| 行对齐 | `Row align="middle"`，保证操作按钮与 `32px` 高输入框垂直居中 |
| 收起态操作区 | 与首行筛选项**同一 `Row`**；`Col flex="auto"` + `style={{ textAlign: 'right' }}` 靠右；**禁止** `span={24}` 独占下一行 |
| 展开态操作区 | 位于**最后一行**筛选项之后、单独一行靠右；可与末行筛选项同 `Row` 或下一 `Row`，但须 `textAlign: 'right'` |
| 操作区结构 | 优先在 `Col` 内直接放 `Space` 包裹按钮；避免空 `label=" "` 的 `Form.Item` 把按钮挤到下方 |
| 标签列 | `labelCol={{ flex: '0 0 80px' }}`，保证各筛选项标签对齐 |

```tsx
// 收起态：筛选项 + 操作区同一行
<Row gutter={[FILTER_GUTTER, 0]} align="middle" wrap={false}>
  <Col flex="none"><Form.Item label="字段1" style={{ marginBottom: 0 }}>...</Form.Item></Col>
  <Col flex="none"><Form.Item label="字段2" style={{ marginBottom: 0 }}>...</Form.Item></Col>
  <Col flex="none"><Form.Item label="字段3" style={{ marginBottom: 0 }}>...</Form.Item></Col>
  <Col flex="auto" style={{ textAlign: 'right' }}>
    <Space>
      <Button>重置</Button>
      <Button type="primary">查询</Button>
      <Button type="link">展开</Button>
    </Space>
  </Col>
</Row>
```

### 2.5 查询区内边距与自适应折叠（强约束）

#### 内边距

查询表单卡片内容区（`Card` body / 表单外层）**上下左右**内边距均为 **`20px`**：

| 规则 | 要求 |
| ---- | ---- |
| 数值 | `padding: 20px`（四边一致） |
| 实现 | React：`Card bodyStyle={{ padding: 20 }}`；Vue：`a-card` `:body-style="{ padding: '20px' }"` |
| 禁止 | 禁止 `paddingBottom: 0`、禁止贴边无内边距、禁止仅设左右不设上下 |

#### 自适应展开/收起

必须与 [Ant Design Pro TableList](https://preview.pro.ant.design/list/table-list) 一致：**排不下才折叠**，能放下的筛选项在收起态全部显示在首行。

| 规则 | 要求 |
| ---- | ---- |
| 计算依据 | 容器可用宽度 = 容器宽 − `20px × 2`；单项占用 ≈ 标签宽 `80px` + 控件 `260px` + 项间 `50px` |
| 操作区预留 | 收起且需折叠时预留「重置 + 查询 + 展开」宽度（约 `240px`）；全部放得下时预留「重置 + 查询」（约 `168px`） |
| 收起可见数 | 在满足操作区预留前提下，首行**尽可能多**显示筛选项；仅多余项隐藏 |
| 展开按钮 | **仅当** `收起可见数 < 筛选项总数` 时渲染「展开/收起」 |
| 响应式 | 监听容器 `resize`（`ResizeObserver`），宽度变化时重新计算可见数 |
| 禁止 | 禁止写死 `slice(0, 3)`、禁止「≥4 个才展开」、禁止有空间仍隐藏可显示的筛选项 |

```tsx
const FILTER_PADDING = 20;
const FILTER_LABEL_WIDTH = 80;
const FILTER_ITEM_WIDTH = FILTER_LABEL_WIDTH + FILTER_CONTROL_WIDTH; // 340
const FILTER_ACTION_WITH_EXPAND = 240;
const FILTER_ACTION_NO_EXPAND = 168;

function calcFilterLayout(containerWidth: number, fieldCount: number) {
  const inner = containerWidth - FILTER_PADDING * 2;
  const fieldsWidth = (n: number) =>
    n * FILTER_ITEM_WIDTH + Math.max(0, n - 1) * FILTER_GUTTER;

  if (fieldsWidth(fieldCount) + FILTER_ACTION_NO_EXPAND <= inner) {
    return { collapsedCount: fieldCount, showExpand: false };
  }
  let collapsedCount = 1;
  for (let k = fieldCount - 1; k >= 1; k--) {
    if (fieldsWidth(k) + FILTER_ACTION_WITH_EXPAND <= inner) {
      collapsedCount = k;
      break;
    }
  }
  return { collapsedCount, showExpand: true };
}
```

## 3. 查询控件类型映射

筛选条件必须按字段语义选择控件，不得随意替换：

| 字段语义 | 控件类型 | 占位与规则 |
| ---- | ---- | ---- |
| （所有查询控件） | — | 宽 **`260px`**（§2.2）；横向 **`50px`**、展开行距 **`20px`**（§2.3） |
| 日期、时间、创建时间、入职日期等区间 | RangePicker | 使用区间选择，占位为「开始日期」和「结束日期」 |
| 普通文本，如名称、姓名、编号、单号 | Input | 占位为「请输入」 |
| 支持多值批量输入的文本 | TextArea 或项目批量输入组件 | 占位为「多条请换行」 |
| 枚举字段，如状态、类型、岗位、部门 | Select | 占位为「请选择」，选项来自接口或字典 |
| 多选枚举 | Select multiple | 占位为「请选择」，允许清空 |
| 数值范围 | InputNumber 组合或范围输入组件 | 保持同一行内对齐 |

如果用户提供截图中的筛选项形态，应优先按截图字段类型生成；没有截图时，按上表生成。

## 4. 表格卡片

表格区必须符合 Ant Design Pro TableList 的结构：

| 区域 | 要求 |
| ---- | ---- |
| 表格标题 | 位于表格卡片左上，例如「查询表格」 |
| 工具栏 | 位于表格卡片右上 |
| 主操作按钮 | `+ 新建` 放在工具栏最左侧，使用主按钮；点击打开 `Modal` + `Form` 弹窗（见 `form-modal.md`） |
| 次级工具 | 密度、刷新、列设置等图标按钮放在主操作按钮右侧 |
| 表头 | 使用浅灰背景 `#FAFAFA` 或主题表头背景 |
| 行高 | 保持 Ant Design 默认中等密度，不得过度压缩 |
| 操作列 | 固定在最右侧，使用文字链接按钮 |
| 分页 | 位于表格右下方 |

表格上方不得额外添加与 Ant Design Pro TableList 不一致的大面积说明区、统计卡片或自定义装饰，除非用户明确要求。

## 5. 表格列规范

表格列生成必须遵循以下规则：

- 第一列可按业务需要使用复选框选择列。
- 主名称字段使用链接样式，但颜色必须来自主题主色。
- 状态字段使用圆点 + 文案，颜色按语义色区分。
- 时间字段保持完整日期时间格式。
- 操作列只放高频操作，超过 3 个操作时使用「更多」收纳。
- 列宽根据内容设置，避免所有列平均分配造成可读性差。

## 6. 禁止项

生成查询表格页面时禁止出现以下情况：

- 禁止把查询区做成左侧筛选栏。
- 禁止把查询按钮放在表单下方居中。
- 禁止用卡片、统计块、图表替代表格主体。
- 禁止把所有筛选项无折叠地堆满多行；**仅当宽度排不下**时才折叠，且须自适应计算可见数。
- 禁止写死收起态只显示前 N 个筛选项（如固定 3 个）；禁止有空间仍提前隐藏筛选项。
- 禁止查询区内边距非 **`20px`** 四边一致。
- 禁止查询区筛选控件宽度不统一；所有查询条件控件必须固定 **`260px`**。
- 禁止查询区横向间距非 **`50px`**；禁止展开后行间距非 **`20px`**。
- 禁止查询区使用 `span={8}` 等比例分栏导致间距虚高、操作按钮下沉到筛选项下方。
- 禁止硬编码与品牌配置冲突的主题色。
- 禁止将 Ant Design Pro TableList 的工具栏图标省略到只剩普通按钮，除非用户明确要求简化。
