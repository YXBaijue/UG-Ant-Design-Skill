# 通用规则索引

本目录用于逐步拆分全局设计规则。拆分时必须保持根目录 `ant-design-system-prompt.md` 中的原始规范语义不变。

建议拆分方向：

| 文件 | 内容 |
| ---- | ---- |
| `components.md` | 组件使用规范：默认 React + antd、增删改查组件映射、禁止手写模拟组件（已就绪，强约束） |
| `interaction.md` | 页面生成技术规范：交互要求 + Tailwind 辅助样式（已就绪，强约束） |
| `tokens.md` | 品牌色、语义色、字体、间距、圆角、控件高度、阴影、动效、断点 |
| `layout.md` | B 端页面布局、画布、Header、Sider、内容区、卡片/区块 |

## 维护规则

- 根目录 `ant-design-system-prompt.md` 作为当前单文件完整规范，继续兼容 Gemini raw 文件导入。
- `rules/` 和 `templates/` 作为长期维护的结构化来源。
- 当规则稳定后，可以同步生成 `dist/ant-design-system.full.md` 作为完整合并版。
- 不得在拆分过程中改写已有规范含义。
