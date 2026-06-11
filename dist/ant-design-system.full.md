# Ant Design 设计系统规范（完整合并版）

> 本文件供 Gemini 等单文件导入。样式权威源为 `ant-design-system-prompt.md`；各扩展规则以 `templates/`、`rules/` 为准。
# Ant Design 设计系统规范（通用版）


## 使用说明

本规范用于指导 AI 生成基于 Ant Design 的前端页面原型图。

### 文件定位

本文件仅包含 **视觉样式约定**（第一～四部分：品牌配置、Design Token、布局指南、组件外观参数）。**这部分内容保持不动**，作为样式权威来源。

页面模板、交互、组件实现等规则已拆分到独立文件，勿再写入本文件以免重复膨胀。

### 仓库结构

对于支持读取仓库文件结构的 AI 工具，生成页面时须按以下结构读取补充规则：

```text
project-root/
├── ant-design-system-prompt.md
├── rules/
│   ├── README.md
│   ├── interaction.md
│   └── components.md
├── templates/
│   ├── README.md
│   ├── list-table.md
│   └── detail-basic.md
└── dist/
    └── ant-design-system.full.md   ← Gemini 等单文件导入用（样式 + 模板 + 规则合并版）
```

**Gemini 等只能导入单个 raw 文件时**，请使用 `dist/ant-design-system.full.md`，不要只导入本文件。

生成页面时除本文件外，还必须遵循：

- `rules/components.md`：默认 React + `antd`；HTML 页面通过 CDN 引入 React + antd 使用真实组件；不得默认 Vue 或原生 JS 模拟组件。
- `rules/interaction.md`：页面必须可交互、可点击（非静态）；Tailwind 仅作布局辅助，不得替代 antd 组件。

### 默认生成模式（强约束）

生成 **B 端后台页面** 时，**默认必须带左侧菜单应用壳**（`Layout` + `Menu`），除非用户明确要求「仅内容页」「不要菜单」「独立单页」。

默认读取顺序：

1. **`templates/app-shell.md`**（应用壳 + 页面注册表 + 菜单切换）— 始终先读
2. 业务页模板（按类型二选一或多选）：
   - 查询表格页 / 列表页 / TableList → `templates/list-table.md`
   - 详情页 / Basic Profile → `templates/detail-basic.md`
   - 弹窗表单 → `templates/form-modal.md`

HTML 原型默认扩展 `admin-shell.html`；新业务页须写入 `PAGE_REGISTRY` 并挂入菜单。

### 页面模板索引

完整索引见 `templates/README.md`。

页面模板约束优先级高于通用组件参数；品牌配置和全局 Design Token 仍是最高层的视觉约束。

### 工作流程

1. 读取下方「品牌配置」章节，获取当前项目的品牌参数
2. **默认读取 `templates/app-shell.md` 搭建后台壳**（用户明确不要菜单时跳过）
3. 品牌配置中的值覆盖 Design Token 默认值
4. 间距、圆角、字号等使用「全局 Design Token」中的标准值
5. 具体组件样式参考「组件级设计参数」
6. 页面整体布局参考「B 端页面布局指南」
7. 读取业务页面对应模板（`list-table.md` 等）生成 `Content` 区内内容
8. 组件实现必须遵循 `rules/components.md`：优先 React + antd，其次 Vue + ant-design-vue

### 关键约束

- 所有间距必须是 4px 的倍数（sizeUnit = 4px）
- 圆角使用标准梯度：2px / 4px / 6px / 8px
- 控件高度使用标准梯度：24px / 32px / 40px
- 字号使用标准梯度：12px / 14px / 16px / 20px / 24px / 30px / 38px
- 颜色优先使用语义化 Token（colorPrimary、colorSuccess 等），不直接使用色值
- 主色相关的派生色（hover、active、背景等）由算法自动生成，只需配置 Seed Token

### 覆盖优先级

```
品牌配置中的值 > Design Token 默认值
```

---

# 第一部分：品牌配置

> ⚠️ 修改此部分以适配你的项目品牌。未配置的项使用 Ant Design 默认值。

## 1. 品牌基础信息

| 配置项       | 值                  | 说明                       |
| --------- | ------------------ | ------------------------ |
| brandName | `你的品牌名`            | 品牌名称，用于页面标题、侧边栏 Logo 旁文字 |
| logoPath  | `/public/logo.svg` | Logo 文件路径（相对项目根目录）       |
| logoSize  | `32px`             | Logo 显示尺寸（侧边栏/顶栏）        |

## 2. 主题色配置

> 只需配置 Seed Token，派生色由 Ant Design 算法自动生成。

| Token        | 值         | 说明              |
| ------------ | --------- | --------------- |
| colorPrimary | `#1677FF` | 品牌主色（按钮/链接/选中态） |
| colorSuccess | `#52C41A` | 成功状态            |
| colorWarning | `#FAAD14` | 警告状态            |
| colorError   | `#FF4D4F` | 错误/危险状态         |
| colorInfo    | `#1677FF` | 信息提示（通常与主色一致）   |

### 主色派生色（由算法自动生成，无需手动配置）

| 派生 Token                | 用途                  |
| ----------------------- | ------------------- |
| colorPrimaryBg          | 主色浅背景（选中态背景、Tag 背景） |
| colorPrimaryBgHover     | 主色浅背景 hover         |
| colorPrimaryBorder      | 主色边框                |
| colorPrimaryBorderHover | 主色边框 hover          |
| colorPrimaryHover       | 主色 hover（按钮 hover）  |
| colorPrimaryActive      | 主色 active（按钮按下）     |
| colorPrimaryText        | 主色文字                |
| colorPrimaryTextHover   | 主色文字 hover          |
| colorPrimaryTextActive  | 主色文字 active         |

## 3. 布局配置

> 以下为 Pro 风格推荐值。antd 原生默认值：headerHeight=64px, siderWidth=200px, collapsedWidth=80px。

| 配置项                 | 值       | 说明                     |
| ------------------- | ------- | ---------------------- |
| siderWidth          | `208px` | 侧边栏展开宽度（antd 默认 200px） |
| siderCollapsedWidth | `48px`  | 侧边栏收起宽度（antd 默认 80px）  |
| headerHeight        | `48px`  | 顶栏高度（antd 默认 64px）     |
| contentPadding      | `24px`  | 内容区内边距                 |
| siderTheme          | `light` | 侧边栏主题：`light` / `dark` |
| headerTheme         | `light` | 顶栏主题：`light` / `dark`  |

## 4. 字体配置

| 配置项            | 值        | 说明     |
| -------------- | -------- | ------ |
| fontFamily     | （留空使用默认） | 品牌正文字体 |
| fontFamilyCode | （留空使用默认） | 代码字体   |

> 默认字体栈：`-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, 'Noto Sans', sans-serif`

---

# 第二部分：全局 Design Token

## 一、色彩体系

### 1.1 品牌色与语义色（Seed Token）

| Token        | 默认值       | 用途                       |
| ------------ | --------- | ------------------------ |
| colorPrimary | `#1677FF` | 品牌主色，按钮/链接/选中态           |
| colorSuccess | `#52C41A` | 成功状态（Result/Tag/Alert）   |
| colorWarning | `#FAAD14` | 警告状态（Alert/Notification） |
| colorError   | `#FF4D4F` | 错误/危险状态（按钮/校验）           |
| colorInfo    | `#1677FF` | 信息提示（同主色）                |
| colorLink    | `#1677FF` | 超链接默认色                   |

### 1.2 预设调色板

| Token    | 值         | Token   | 值         |
| -------- | --------- | ------- | --------- |
| red      | `#F5222D` | blue    | `#1677FF` |
| orange   | `#FA8C16` | purple  | `#722ED1` |
| yellow   | `#FADB14` | cyan    | `#13C2C2` |
| gold     | `#FAAD14` | green   | `#52C41A` |
| volcano  | `#FA541C` | magenta | `#EB2F96` |
| geekblue | `#2F54EB` | lime    | `#A0D911` |
| pink     | `#EB2F96` | —       | —         |

### 1.3 中性色（文字）

| Token                | 值                  | 用途           |
| -------------------- | ------------------ | ------------ |
| colorText            | `rgba(0,0,0,0.88)` | 主文字（标题/正文）   |
| colorTextSecondary   | `rgba(0,0,0,0.65)` | 次要文字（标签/描述）  |
| colorTextTertiary    | `rgba(0,0,0,0.45)` | 辅助文字（说明/占位符） |
| colorTextQuaternary  | `rgba(0,0,0,0.25)` | 最弱文字（禁用/提示）  |
| colorTextHeading     | `rgba(0,0,0,0.88)` | 标题文字         |
| colorTextDisabled    | `rgba(0,0,0,0.25)` | 禁用态文字        |
| colorTextPlaceholder | `rgba(0,0,0,0.25)` | 占位符文字        |
| colorTextLabel       | `rgba(0,0,0,0.65)` | 表单标签文字       |

### 1.4 背景色

| Token              | 值                  | 用途              |
| ------------------ | ------------------ | --------------- |
| colorBgContainer   | `#FFFFFF`          | 容器背景（卡片/输入框/按钮） |
| colorBgElevated    | `#FFFFFF`          | 浮层背景（弹窗/下拉/菜单）  |
| colorBgLayout      | `#F5F5F5`          | 页面整体布局背景        |
| colorBgMask        | `rgba(0,0,0,0.45)` | 遮罩层背景           |
| colorBgSpotlight   | `rgba(0,0,0,0.85)` | Tooltip 背景      |
| colorBgSolid       | `rgb(0,0,0)`       | 实色背景（v6 实色按钮）   |
| colorBgSolidHover  | `rgba(0,0,0,0.75)` | 实色背景 hover      |
| colorBgSolidActive | `rgba(0,0,0,0.95)` | 实色背景 active     |
| colorBgBlur        | `transparent`      | 毛玻璃容器背景         |

### 1.5 填充色

| Token               | 值                  | 用途   |
| ------------------- | ------------------ | ---- |
| colorFill           | `rgba(0,0,0,0.15)` | 最深填充 |
| colorFillSecondary  | `rgba(0,0,0,0.06)` | 二级填充 |
| colorFillTertiary   | `rgba(0,0,0,0.04)` | 三级填充 |
| colorFillQuaternary | `rgba(0,0,0,0.02)` | 最弱填充 |

### 1.6 边框色

| Token                | 值                  | 用途    |
| -------------------- | ------------------ | ----- |
| colorBorder          | `#D9D9D9`          | 默认边框  |
| colorBorderSecondary | `#F0F0F0`          | 次级边框  |
| colorBorderDisabled  | `#D9D9D9`          | 禁用态边框 |
| colorSplit           | `rgba(5,5,5,0.06)` | 分割线   |


---

## 二、字体体系

| Token            | 值        | 用途        |
| ---------------- | -------- | --------- |
| fontSize         | `14px`   | 基准字号（正文）  |
| fontSizeSM       | `12px`   | 小字号       |
| fontSizeLG       | `16px`   | 大字号       |
| fontSizeXL       | `20px`   | 超大字号（h4）  |
| fontSizeHeading3 | `24px`   | h3 标题     |
| fontSizeHeading2 | `30px`   | h2 标题     |
| fontSizeHeading1 | `38px`   | h1 标题     |
| lineHeight       | `1.5714` | 14px 正文行高 |
| fontWeightStrong | `600`    | 标题加粗      |

---

## 三、间距体系

基础单位：`sizeUnit = 4px`

| Token     | 值      | Token      | 值      |
| --------- | ------ | ---------- | ------ |
| marginXXS | `4px`  | paddingXXS | `4px`  |
| marginXS  | `8px`  | paddingXS  | `8px`  |
| marginSM  | `12px` | paddingSM  | `12px` |
| margin    | `16px` | padding    | `16px` |
| marginMD  | `20px` | paddingMD  | `20px` |
| marginLG  | `24px` | paddingLG  | `24px` |
| marginXL  | `32px` | paddingXL  | `32px` |
| marginXXL | `48px` | —          | —      |

---

## 四、圆角体系

| Token          | 值     | 用途           |
| -------------- | ----- | ------------ |
| borderRadius   | `6px` | 基础圆角（按钮/输入框） |
| borderRadiusSM | `4px` | 小圆角          |
| borderRadiusLG | `8px` | 大圆角（卡片/弹窗）   |
| borderRadiusXS | `2px` | 极小圆角         |

---

## 五、控件高度

| Token           | 值      | 用途   |
| --------------- | ------ | ---- |
| controlHeightSM | `24px` | 小控件  |
| controlHeight   | `32px` | 标准控件 |
| controlHeightLG | `40px` | 大控件  |

---

## 六、阴影体系

| Token             | 值                                                                                                 | 用途   |
| ----------------- | ------------------------------------------------------------------------------------------------- | ---- |
| boxShadow         | `0 6px 16px 0 rgba(0,0,0,0.08), 0 3px 6px -4px rgba(0,0,0,0.12), 0 9px 28px 8px rgba(0,0,0,0.05)` | 一级阴影 |
| boxShadowTertiary | `0 1px 2px 0 rgba(0,0,0,0.03), 0 1px 6px -1px rgba(0,0,0,0.02), 0 2px 4px 0 rgba(0,0,0,0.02)`     | 三级阴影 |

---

## 七、动效体系

| Token              | 值                                      | 用途     |
| ------------------ | -------------------------------------- | ------ |
| motionDurationFast | `0.1s`                                 | 快速交互   |
| motionDurationMid  | `0.2s`                                 | 中速交互   |
| motionDurationSlow | `0.3s`                                 | 慢速/页面级 |
| motionEaseInOut    | `cubic-bezier(0.645, 0.045, 0.355, 1)` | 标准进出   |
| motionEaseOut      | `cubic-bezier(0.215, 0.61, 0.355, 1)`  | 标准退出   |

---

## 八、响应式断点

| Token      | 值        | Min      | Max      |
| ---------- | -------- | -------- | -------- |
| screenXS   | `480px`  | `480px`  | `575px`  |
| screenSM   | `576px`  | `576px`  | `767px`  |
| screenMD   | `768px`  | `768px`  | `991px`  |
| screenLG   | `992px`  | `992px`  | `1199px` |
| screenXL   | `1200px` | `1200px` | `1599px` |
| screenXXL  | `1600px` | `1600px` | `1919px` |
| screenXXXL | `1920px` | `1920px` | —        |

---

## 九、交互态

| Token                    | 值                  | 用途           |
| ------------------------ | ------------------ | ------------ |
| controlItemBgHover       | `rgba(0,0,0,0.04)` | 列表项 hover    |
| controlItemBgActive      | `#E6F4FF`          | 列表项选中        |
| controlItemBgActiveHover | `#BAE0FF`          | 列表项选中+hover  |
| colorBgContainerDisabled | `rgba(0,0,0,0.04)` | 禁用态背景        |
| colorFillContentHover    | `rgba(0,0,0,0.15)` | 内容区 hover 背景 |

---

## 十、v6 新增 Token

| Token               | 值                  | 用途          |
| ------------------- | ------------------ | ----------- |
| colorBgSolid        | `rgb(0,0,0)`       | 实色按钮背景      |
| colorBgSolidHover   | `rgba(0,0,0,0.75)` | 实色按钮 hover  |
| colorBgSolidActive  | `rgba(0,0,0,0.95)` | 实色按钮 active |
| colorBgBlur         | `transparent`      | 毛玻璃背景       |
| colorBorderDisabled | `#D9D9D9`          | 禁用态边框       |
| colorErrorAffix     | `#FF4D4F`          | 错误态前/后缀     |
| colorWarningAffix   | `#FAAD14`          | 警告态前/后缀     |
| linkDecoration      | `none`             | 链接装饰        |
| linkHoverDecoration | `none`             | 链接 hover 装饰 |

### 语义色完整派生梯度

每个语义色（Success/Warning/Error/Info）均有以下派生 Token（由算法自动生成）：

| 后缀          | 用途        | Success 示例值 |
| ----------- | --------- | ----------- |
| Bg          | 浅背景       | `#F6FFED`   |
| BgHover     | 浅背景 hover | `#D9F7BE`   |
| Border      | 边框        | `#B7EB8F`   |
| BorderHover | 边框 hover  | `#95DE64`   |
| Hover       | 色值 hover  | `#95DE64`   |
| Active      | 色值 active | `#389E0D`   |
| Text        | 文字        | `#52C41A`   |
| TextHover   | 文字 hover  | `#73D13D`   |
| TextActive  | 文字 active | `#389E0D`   |


---

# 第三部分：B 端页面布局指南

## 设计画布

| 属性     | 推荐值      | 说明                |
| ------ | -------- | ----------------- |
| 标准画布宽度 | `1440px` | Ant Design 团队统一标准 |
| 大屏画布   | `1920px` | 大屏/数据看板           |
| 笔记本画布  | `1280px` | 小屏适配              |

## antd 原生 Layout 默认值

| Token                | 默认值         | 说明            |
| -------------------- | ----------- | ------------- |
| headerHeight         | `64px`      | Header 高度     |
| headerBg             | `#001529`   | Header 背景（深色） |
| headerPadding        | `0 50px`    | Header 内边距    |
| Sider width          | `200px`     | Sider 展开宽度    |
| Sider collapsedWidth | `80px`      | Sider 收起宽度    |
| siderBg              | `#001529`   | Sider 背景（深色）  |
| lightSiderBg         | `#FFFFFF`   | Sider 背景（亮色）  |
| bodyBg / footerBg    | `#F5F5F5`   | 内容区/Footer 背景 |
| footerPadding        | `24px 50px` | Footer 内边距    |
| triggerHeight        | `48px`      | 触发器高度         |

### 导航高度计算公式

| 场景        | 值/公式       |
| --------- | ---------- |
| 顶部导航（一级）  | `64px`     |
| 顶部导航（二级）  | `48px`     |
| 落地页导航（一级） | `80px`     |
| 顶部导航通用公式  | `48 + 8n`  |
| 侧边导航通用公式  | `200 + 8n` |

## B 端推荐配置（Pro 风格覆盖值）

> 以下为 Ant Design Pro 风格的 B 端管理系统推荐值，覆盖 antd 原生默认值。

```
┌─────────────────────────────────────────────────┐
│                  Header (48px)                    │
├──────────┬──────────────────────────────────────┤
│  Sider   │           Content                     │
│ (208px)  │        (padding: 24px)                │
└──────────┴──────────────────────────────────────┘
```

| 区域          | Pro 推荐值   | antd 默认值  | 说明            |
| ----------- | --------- | --------- | ------------- |
| 顶部栏高度       | `48px`    | `64px`    | Pro 更紧凑       |
| 顶部栏内边距      | `0 24px`  | `0 50px`  | —             |
| 顶部栏背景       | `#FFFFFF` | `#001529` | Pro 亮色主题      |
| 侧边栏宽度（展开）   | `208px`   | `200px`   | Pro 略宽        |
| 侧边栏宽度（收起）   | `48px`    | `80px`    | Pro 更紧凑       |
| 侧边栏背景       | `#FFFFFF` | `#001529` | Pro 亮色主题      |
| 内容区 padding | `24px`    | —         | 四周统一          |
| 内容区背景色      | `#F5F5F5` | `#F5F5F5` | colorBgLayout |
| 卡片间距        | `16px`    | —         | margin        |

## 栅格系统

| 属性            | 值               | 说明               |
| ------------- | --------------- | ---------------- |
| 布局网格基础单位      | `8px`           | 官方推荐，布局尺寸为 8 的倍数 |
| 栅格列数          | `24`            | —                |
| 间距（gutter）    | `16px` 或 `24px` | 根据内容密度           |
| 内容区宽度（1440画布） | `1168px`        | 24 栅格分配          |

> 注意：布局级用 8px 网格，组件级用 4px（sizeUnit）。

## 卡片/区块

| 属性     | 推荐值               |
| ------ | ----------------- |
| 卡片内边距  | `24px`            |
| 卡片圆角   | `8px`             |
| 卡片阴影   | boxShadowTertiary |
| 卡片背景   | `#FFFFFF`         |
| 区块标题字号 | `16px`            |
| 区块标题字重 | `600`             |
| 区块间距   | `24px`            |

## 表格参数

| 属性         | 推荐值       |
| ---------- | --------- |
| 表头背景       | `#FAFAFA` |
| 单元格内边距     | `16px`    |
| 行 hover 背景 | `#FAFAFA` |
| 行分割线       | `#F0F0F0` |

## 表单参数

| 属性    | 推荐值     |
| ----- | ------- |
| 输入框高度 | `32px`  |
| 输入框圆角 | `6px`   |
| 表单项间距 | `24px`  |
| 标签对齐  | `right` |
| 标签栅格  | `6~8`   |

## 页面间距规范

| 层级  | 间距     | 用途      |
| --- | ------ | ------- |
| 页面级 | `24px` | 内容区与边界  |
| 区块级 | `24px` | 卡片之间    |
| 组件级 | `16px` | 卡片内元素之间 |
| 元素级 | `8px`  | 紧凑元素之间  |
| 最小级 | `4px`  | 图标与文字   |

> 布局级间距遵循 8px 网格，组件级间距遵循 4px 基础单位。

---

# 第四部分：组件级设计参数

## Button 按钮

| 尺寸     | 高度     | 圆角    | 字号     | 水平内边距  |
| ------ | ------ | ----- | ------ | ------ |
| small  | `24px` | `4px` | `14px` | `7px`  |
| middle | `32px` | `6px` | `14px` | `15px` |
| large  | `40px` | `8px` | `16px` | `15px` |

| 属性     | 值         |
| ------ | --------- |
| 主按钮文字色 | `#FFF`    |
| 默认按钮边框 | `#D9D9D9` |
| 危险按钮色  | `#FF4D4F` |

## Input 输入框

| 属性        | 值                        |
| --------- | ------------------------ |
| 字号        | `14px`                   |
| 横向内边距     | `11px`                   |
| 激活态边框     | 主色                       |
| 激活态阴影     | `0 0 0 2px rgba(主色,0.1)` |
| hover 边框色 | 主色浅                      |

## Table 表格

| 属性         | 值                |
| ---------- | ---------------- |
| 单元格字号      | `14px`           |
| 单元格内边距     | `16px`           |
| 表头背景       | `#FAFAFA`        |
| 表头圆角       | `8px`            |
| 行 hover 背景 | `#FAFAFA`        |
| 行选中背景      | `#E6F4FF`（随主色变化） |
| 选择列宽度      | `32px`           |

## Modal 对话框

| 属性   | 值                  |
| ---- | ------------------ |
| 默认宽度 | `520px`            |
| 圆角   | `8px`              |
| 内边距  | `24px`             |
| 标题字号 | `16px`             |
| 标题字重 | `600`              |
| 遮罩背景 | `rgba(0,0,0,0.45)` |
| 阴影   | boxShadow（一级）      |

## Form 表单

| 属性    | 值         |
| ----- | --------- |
| 标签对齐  | `right`   |
| 标签栅格  | `6~8`     |
| 控件栅格  | `16~18`   |
| 表单项间距 | `24px`    |
| 必填星号色 | `#FF4D4F` |

## Tag 标签

| 语义         | 背景        | 文字                 | 边框        |
| ---------- | --------- | ------------------ | --------- |
| success    | `#F6FFED` | `#52C41A`          | `#B7EB8F` |
| processing | `#E6F4FF` | `#1677FF`          | `#91CAFF` |
| warning    | `#FFFBE6` | `#FAAD14`          | `#FFE58F` |
| error      | `#FFF2F0` | `#FF4D4F`          | `#FFCCC7` |
| default    | `#FAFAFA` | `rgba(0,0,0,0.88)` | `#D9D9D9` |

Tag 基础参数：字号 `12px`，圆角 `4px`，内边距 `0 7px`，行高 `20px`

## Select 选择器

| 属性     | 值                                |
| ------ | -------------------------------- |
| 高度     | `32px`（标准）/ `40px`（大）/ `24px`（小） |
| 圆角     | `6px`                            |
| 下拉面板圆角 | `8px`                            |
| 选项选中背景 | `#E6F4FF`（随主色变化）                 |

## Tabs 标签页

| 属性    | 值      |
| ----- | ------ |
| 标签字号  | `14px` |
| 标签间距  | `32px` |
| 选中字色  | 主色     |
| 下划线宽度 | `2px`  |
| 下划线色  | 主色     |

## Menu 导航菜单

| 属性    | 值      |
| ----- | ------ |
| 菜单项高度 | `40px` |
| 选中背景  | 主色浅背景  |
| 选中文字  | 主色     |
| 子菜单缩进 | `24px` |

## Drawer 抽屉

| 属性    | 值       |
| ----- | ------- |
| 默认宽度  | `378px` |
| 大宽度   | `736px` |
| 内容内边距 | `24px`  |
| 标题字号  | `16px`  |
| 标题字重  | `600`   |

## Pagination 分页

| 属性   | 值      |
| ---- | ------ |
| 按钮尺寸 | `32px` |
| 圆角   | `6px`  |
| 选中背景 | 主色     |
| 选中文字 | `#FFF` |

## Alert 警告提示

| 语义      | 背景        | 边框        |
| ------- | --------- | --------- |
| success | `#F6FFED` | `#B7EB8F` |
| info    | `#E6F4FF` | `#91CAFF` |
| warning | `#FFFBE6` | `#FFE58F` |
| error   | `#FFF2F0` | `#FFCCC7` |

圆角 `8px`，字号 `14px`

## 其他常用组件速查

| 组件           | 关键参数                                           |
| ------------ | ---------------------------------------------- |
| Card         | 内边距 `24px`，圆角 `8px`，阴影 boxShadowTertiary       |
| Tooltip      | 背景 `rgba(0,0,0,0.85)`，文字 `#FFF`，圆角 `6px`       |
| Popover      | 背景 `#FFF`，圆角 `8px`，阴影 boxShadow                |
| Dropdown     | 背景 `#FFF`，圆角 `8px`，选项 hover `rgba(0,0,0,0.04)` |
| Badge        | 背景 `#FF4D4F`，文字 `#FFF`，字号 `12px`               |
| Avatar       | 默认 `32px`，小 `24px`，大 `40px`                    |
| Progress     | 高度 `8px`，全圆角，默认色=主色                            |
| Switch       | 高度 `22px`，宽 `44px`，开启=主色                       |
| Checkbox     | 尺寸 `16px`，圆角 `2px`，选中=主色                       |
| Radio        | 尺寸 `16px`，圆点 `8px`，选中=主色                       |
| Steps        | 圆点 `32px`，完成色=主色，等待色=`#F0F0F0`                 |
| Spin         | 默认 `20px`，大 `48px`，颜色=主色                       |
| Skeleton     | 背景 `rgba(0,0,0,0.06)`，圆角 `4px`                 |
| Divider      | 颜色 `rgba(5,5,5,0.06)`，上下间距 `24px`              |
| Tree         | 节点高 `28px`，选中背景 `#E6F4FF`，缩进 `24px`            |
| Timeline     | 圆点 `10px`，连接线 `#F0F0F0`，宽 `2px`                |
| Message      | 圆角 `8px`，阴影 boxShadow，z-index `1010`           |
| Notification | 宽度 `384px`，z-index `2050`                      |
| Upload       | 拖拽区圆角 `8px`，虚线边框 `#D9D9D9`，hover=主色            |

---

# 附录：扩展规范索引

以下规则已从本文件拆出，维护时请改对应文件，勿回写到第一～四部分。

| 类型 | 文件 | 说明 |
| ---- | ---- | ---- |
| 后台应用壳模板 | `templates/app-shell.md` | Layout + Menu 多页切换与页面注册表 |
| 查询表格页模板 | `templates/list-table.md` | TableList 页面结构与强约束 |
| 详情页模板 | `templates/detail-basic.md` | Basic Profile 页面结构与强约束 |
| 表单弹窗模板 | `templates/form-modal.md` | Modal + Form（Basic Form）结构与强约束 |
| 页面模板索引 | `templates/README.md` | 按页面类型选择模板 |
| 交互与技术 | `rules/interaction.md` | 可交互、Tailwind 辅助、图标 |
| 组件实现 | `rules/components.md` | React + antd 默认、增删改查组件映射 |
| 完整合并版 | `dist/ant-design-system.full.md` | 单文件导入（样式 + 全部扩展规则） |

读取顺序：本文件（样式）→ 判断页面类型 → `templates/` + `rules/`。

---

## 扩展：templates/list-table.md
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
| 主操作按钮 | `+ 新建` 放在工具栏最左侧，使用主按钮 |
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

---

## 扩展：templates/detail-basic.md
# 详情页模板：Basic Profile

当用户要求生成「详情页」「基础详情」「查看详情」「Profile」「Basic Profile」等 B 端详情页面时，必须严格按照 Ant Design Pro 的 Basic Profile 页面形态生成。

参考：https://preview.pro.ant.design/profile/basic

## 1. 总体结构

详情页必须由以下区域组成，顺序不得随意改变：

1. 页面标题区：显示详情页标题，可带返回入口。
2. 顶部概要卡片：展示主对象名称、关键描述、状态、核心指标或基础摘要。
3. 基础信息区：使用 `Descriptions` 风格展示主要字段。
4. 详情信息区：按业务分组展示补充字段。
5. 关联信息区：如有关联明细、成员、订单、日志等，使用表格或列表展示。
6. 操作记录区：如存在流程、审批、变更记录，使用时间线或表格展示。

页面背景使用 `colorBgLayout`，内容区使用白色卡片或分区块。主题色必须使用品牌配置中的 `colorPrimary`，不得为了还原示例而硬编码 Ant Design Pro 默认蓝色。

## 2. 顶部概要卡片

顶部概要卡片用于快速识别当前详情对象，必须保持 Ant Design Pro Basic Profile 的信息密度：

| 区域 | 要求 |
| ---- | ---- |
| 主标题 | 使用对象名称、单号、项目名等核心标识 |
| 描述信息 | 放置一行辅助说明，不得堆叠大量字段 |
| 状态 | 使用 Badge 或 Tag，颜色按语义色 |
| 核心信息 | 可在右侧或下方展示 2-4 个关键指标 |

如果业务没有指标，不得强行生成无意义统计卡片。

## 3. 基础信息区

基础信息必须使用 `Descriptions` 风格，而不是编辑表单风格：

| 属性 | 要求 |
| ---- | ---- |
| 布局 | 多列信息展示，优先 3 列 |
| 标签 | 标签在左，内容在右 |
| 字号 | 遵循全局字体规范 |
| 间距 | 分组间距优先 `24px` |
| 空值 | 使用 `-` 展示 |

字段较多时必须按业务语义分组，例如「基础信息」「联系信息」「业务信息」「系统信息」。不得将所有字段无分组地纵向堆叠。

## 4. 详情信息区

详情信息区用于承载较长文本、说明、地址、备注等内容：

- 长文本必须保持可读行高，不得压缩成表格单元格。
- 备注、描述类内容应独立成块展示。
- 文件、图片、附件等内容应使用列表或上传预览样式展示。
- 金额、数量、时间等字段需保持统一格式。

## 5. 关联信息区

关联明细必须按数据类型选择展示方式：

| 数据类型 | 展示方式 |
| ---- | ---- |
| 明细行、子订单、关联记录 | Table |
| 成员、联系人、参与人 | List 或 Table |
| 流程、审批、变更历史 | Timeline 或 Table |
| 附件、图片 | Upload preview 或 List |

如果存在明细表格，表格结构必须遵循查询表格模板中的表格列规范，但不需要重复查询表单。

## 6. 页面操作

详情页操作必须放在符合 Ant Design Pro 风格的位置：

- 返回入口放在页面标题区或浏览器导航语义位置。
- 主要操作可放在顶部右侧，例如「编辑」「删除」「导出」。
- 危险操作必须使用危险色，并避免与主操作混淆。
- 超过 3 个操作时使用「更多」收纳。

## 7. 禁止项

生成详情页时禁止出现以下情况：

- 禁止把详情页做成新增/编辑表单。
- 禁止所有字段单列纵向堆叠。
- 禁止使用大面积自定义装饰背景。
- 禁止用统计卡片替代基础信息展示。
- 禁止硬编码与品牌配置冲突的主题色。
- 禁止省略基础信息区，除非用户明确说明是极简详情。

---

## 扩展：templates/form-modal.md
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

---

## 扩展：templates/app-shell.md
# 后台应用壳模板：Layout + Menu（多页切换）

## 0. 默认模式（强约束）

本模板是 **B 端后台页面的默认输出形态**。生成任何后台列表页、详情页、管理页时，**默认必须**先按本模板搭建应用壳，再将业务页挂入左侧菜单。

| 规则 | 要求 |
| ---- | ---- |
| 默认启用 | 用户未明确说「不要菜单」「仅内容页」「独立单页」时，**一律**带左侧 `Menu` |
| 生成顺序 | ① `app-shell.md`（壳 + 注册表 + 菜单）→ ② 业务模板（`list-table.md` 等） |
| 交付物 | 可运行的多页后台（菜单可切换），而非多个互不关联的孤立 HTML |
| HTML 参考 | 扩展 `admin-shell.html`；CDN 顺序：`react` → `react-dom` → `dayjs` → `antd` → `babel` |
| 例外 | 仅当用户明确要求独立内容页时，才可省略应用壳 |

当用户要求生成「后台系统」「管理后台」「列表页」「在某某菜单下生成页面」等场景时，必须先搭建 **antd `Layout` + `Menu` 应用壳**，再将业务页注册到菜单中，通过点击菜单切换内容区页面。

参考 Ant Design Pro 后台布局：https://preview.pro.ant.design/

## 1. 总体结构（强约束）

后台系统必须由以下层级组成，顺序不得改变：

```
┌─────────────────────────────────────────────────────┐
│  Layout (minHeight: 100vh)                           │
│  ├─ Sider (208px, theme="light")                     │
│  │    ├─ Logo + brandName                            │
│  │    └─ Menu (mode="inline", 菜单项 → 页面 key)      │
│  └─ Layout                                           │
│       ├─ Header (48px, 白底, 面包屑/当前页标题)         │
│       └─ Content (colorBgLayout, 业务页渲染区)        │
└─────────────────────────────────────────────────────┘
```

| 区域 | 组件 | 要求 |
| ---- | ---- | ---- |
| 应用壳 | `Layout` | 根容器，`minHeight: '100vh'` |
| 侧边栏 | `Layout.Sider` | 宽 `208px`，收起 `48px`；`theme="light"`；可 `collapsible` |
| 导航 | `Menu` | `mode="inline"`；`selectedKeys` 与当前页 `key` 同步 |
| 顶栏 | `Layout.Header` | 高 `48px`；背景 `#FFFFFF`；展示面包屑或当前页标题 |
| 内容区 | `Layout.Content` | 背景 `colorBgLayout`（`#F5F5F5`）；**仅渲染当前选中菜单对应业务页** |

禁止：

- 禁止每个业务页各自再包一层完整 `Layout` + `Sider`（避免重复侧栏）
- 禁止用自定义 `div` 模拟侧栏菜单
- 禁止业务页与菜单 `key` 无关联的静态多 HTML 文件割裂（原型除外，见 §4）

## 2. 页面注册表（强约束）

所有可切换业务页必须通过 **页面注册表** 维护，不得硬编码散落切换逻辑。

```tsx
const PAGE_REGISTRY: Record<string, {
  label: string;
  breadcrumb: string[];
  group?: string;
  component: React.ComponentType;
}> = {
  'stock-site-list': {
    label: '国际备货站点列表',
    breadcrumb: ['备货管理', '国际备货站点列表'],
    group: '备货管理',
    component: StockSiteListPage,
  },
  'site-mapping': {
    label: '站点映射',
    breadcrumb: ['基础数据', '站点映射'],
    group: '基础数据',
    component: SiteMappingPage,
  },
};

const DEFAULT_PAGE_KEY = 'stock-site-list';
```

| 规则 | 要求 |
| ---- | ---- |
| 菜单 `key` | 与注册表 key **完全一致** |
| 新增页面 | 新增注册项 + 对应 `Menu` 项 + 业务组件文件 |
| 默认页 | 指定 `DEFAULT_PAGE_KEY`，首次加载显示 |
| 面包屑 | 从注册表 `breadcrumb` 读取，写在 `Header` |

## 3. Menu 与切换交互（强约束）

```tsx
const [activeKey, setActiveKey] = useState(DEFAULT_PAGE_KEY);
const ActivePage = PAGE_REGISTRY[activeKey].component;

<Menu
  theme="light"
  mode="inline"
  selectedKeys={[activeKey]}
  items={buildMenuItems(PAGE_REGISTRY)}
  onClick={({ key }) => setActiveKey(key)}
/>

<Layout.Content>
  <ActivePage />
</Layout.Content>
```

| 交互 | 要求 |
| ---- | ---- |
| 点击菜单 | `activeKey` 更新，内容区切换对应组件，**必须真实切换** |
| 选中态 | `Menu` 的 `selectedKeys` 与 `activeKey` 同步 |
| 侧栏折叠 | `Sider` `collapsible` 时菜单图标模式正常 |
| 业务页规范 | 内容区组件遵循 `list-table.md` / `detail-basic.md` / `form-modal.md` |

## 4. 业务页内容区约定

业务页组件**只负责 Content 内部**，不再包含 `Sider` / 全局 `Layout`。

生成新页面时描述方式示例：

> 在后台菜单「基础数据 / 站点映射」下生成 TableList 页面，注册 key 为 `site-mapping`。

AI 须：1）更新 `PAGE_REGISTRY`；2）添加 `Menu` 项；3）生成/更新业务组件。

### 4.1 HTML 原型挂载方式

| 方式 | 说明 |
| ---- | ---- |
| **组件注册（推荐）** | 单 HTML 内 `PAGE_REGISTRY` + `{<ActivePage />}` |
| **iframe 嵌入（演示）** | `admin-shell.html` 示例，iframe 加载独立业务 HTML |

工程化项目改用 `react-router` + `Layout` + `<Outlet />`。

## 5. 布局参数

| 参数 | 值 |
| ---- | ---- |
| Sider 宽度 | `208px` |
| Header 高度 | `48px` |
| Content 背景 | `#F5F5F5` |

## 6. 禁止项

- 禁止生成多个互无关联网页而不提供菜单切换
- 禁止业务页内嵌第二套 `Layout.Sider`
- 禁止 `Menu` 点击无响应
- 禁止新增页面不更新注册表

---

## 扩展：rules/interaction.md
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

---

## 扩展：rules/components.md
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

多页后台系统必须参考 `templates/app-shell.md`，使用 antd `Layout` + `Menu` 维护页面注册表与菜单切换。HTML 原型示例：`admin-shell.html`。

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
