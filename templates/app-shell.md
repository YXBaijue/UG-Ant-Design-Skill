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
// 页面注册表：菜单 key → 页面元信息 + 组件
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

### 3.1 菜单项构建

支持分组（与业务模块一致）：

```tsx
function buildMenuItems(registry: typeof PAGE_REGISTRY) {
  const groups = new Map<string, { key: string; label: string }[]>();
  Object.entries(registry).forEach(([key, page]) => {
    const g = page.group || '默认';
    if (!groups.has(g)) groups.set(g, []);
    groups.get(g)!.push({ key, label: page.label });
  });
  return [...groups.entries()].map(([group, children]) => ({
    type: 'group' as const,
    label: group,
    children,
  }));
}
```

## 4. 业务页内容区约定

业务页组件**只负责 Content 内部**，不再包含 `Sider` / 全局 `Layout`：

| 包含 | 不包含 |
| ---- | ------ |
| 页面标题区（面包屑下的 h1，或仅表格卡片） | 侧边栏 `Menu` |
| 查询卡片 + 表格卡片（TableList） | 重复的全屏 `minHeight: 100vh` 外层壳 |
| 弹窗 `Modal` + `Form` | 另一套顶栏 Header |

生成新页面时描述方式示例：

> 在后台菜单「基础数据 / 站点映射」下生成 TableList 页面，注册 key 为 `site-mapping`。

AI 须：1）更新 `PAGE_REGISTRY`；2）添加 `Menu` 项；3）生成/更新 `SiteMappingPage` 组件。

### 4.1 HTML 原型（CDN）两种挂载方式

| 方式 | 适用 | 说明 |
| ---- | ---- | ---- |
| **组件注册（推荐）** | 单 HTML 多页原型 | 同一文件内多个页面组件 + `PAGE_REGISTRY`，`Content` 内 `{<ActivePage />}` |
| **iframe 嵌入（快速演示）** | 已有独立 HTML 页 | `Content` 内 `<iframe src="./${encodeURI('页面.html')}" />`；壳层 CDN 须含 **dayjs → antd** 顺序 |

HTML 壳层 CDN 顺序（与业务页一致）：`react` → `react-dom` → **`dayjs`** → `antd` → `babel`；缺少 dayjs 会导致 `antd is not defined`。

工程化 React 项目改用 `react-router` + `Layout` + `<Outlet />`，注册表改为路由配置，本模板语义不变。

## 5. 布局参数（对齐品牌配置）

| 参数 | 值 |
| ---- | ---- |
| Sider 宽度 | `208px` |
| Sider 收起 | `48px` |
| Header 高度 | `48px` |
| Header 内边距 | `0 24px` |
| Content 背景 | `#F5F5F5` |
| Sider / Menu 主题 | `light`（Pro 亮色侧栏） |

## 6. 与现有模板关系

| 层级 | 模板 |
| ---- | ---- |
| 应用壳 | **本文件 `app-shell.md`** |
| 列表页内容 | `list-table.md` + `form-modal.md` |
| 详情页内容 | `detail-basic.md` |

读取顺序：判断是否需要后台壳 → 读 `app-shell.md` → 再读业务页模板。

## 7. 禁止项

- 禁止生成多个互无关联网页文件而不提供菜单切换入口
- 禁止业务页内嵌第二套 `Layout.Sider`
- 禁止 `Menu` 点击无响应（死菜单）
- 禁止新增页面不更新注册表与 `Menu` 项
- 禁止用 `PageContainer`（`@ant-design/pro-components`）替代标准 `Layout` 除非工程已引入 Pro 组件库
