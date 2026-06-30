# CLAUDE.md

本文件为 Claude Code（claude.ai/code）在此仓库中工作提供指引。

## 仓库概述

**TikTok自动化任务** 需求与原型仓库，包含：
- `index.html` — 独立的单文件 UI 原型（HTML + 内联 CSS + 内联 JS），实现「新建TikTok自动化任务」表单。所有 mock 数据硬编码，浏览器直接打开即可预览。

本仓库 **无构建系统、无测试、无包管理器、无后端代码**。

## 提交规范

```
feat: <中文描述>
fix: <中文描述>
docs: <中文描述>
chore: <中文描述>
```

## 文档格式

PRD/需求 `.md` 文件采用紧凑格式：标题、正文、表格、列表之间 **不留空行**，紧密排列。

## index.html 架构

整个原型约 2050 行的单文件，分为三部分：
1. **HTML** — 双层 Tab 导航 + 任务表单模态框 + 两个侧边栏（定向包选择、商品库选择）
2. **CSS** — 内联 `<style>` 块，无外部依赖
3. **JS** — 内联 `<script>` 块，包含 mock 数据、表单逻辑、素材预览、商品库关联

### 推广目标

任务表单支持两种推广目标，通过 `#campaignObjective` 切换：
- **应用推广** (`app_promotion`) — 显示 `#appPromotionConfig`：推广系列类型、优化目标、竞价策略、版位、CTA、深度链接等
- **销量** (`product_sales`) — 显示 `#salesConfig`：优化目标、优化事件、竞价策略、版位、行动号召等

### 关键 UI 模式

- **`bindBtnGroup(sel)`** — 工具函数，为指定选择器的 `.btn-option` 按钮组绑定点击事件：切换 `.active` 类 + 调用 `resetMaterialPreview()`。部分按钮组有额外自定义 handler（如 `#campaignType`、`#campaignObjective`），在 `bindBtnGroup` 之后单独追加，读取 `e.target.dataset.value` 避免依赖已被更新的 `.active`。
- **条件可见性** — 通过 `style.display` 切换显示/隐藏，如素材数据筛选为「自定义」时显示业务类型/筛选媒体等字段；推广系列类型切换时联动优化目标可选值。
- **侧边栏（Drawer）** — `.drawer-overlay`（z-index:1001）+ `.drawer` 从右侧滑入，用于定向包选择和商品库选择。模态框 `.modal-overlay`（z-index:1000）居中显示。

### 商品库选择侧边栏

三栏级联结构（Account → Product Library → Product），支持两种模式：
- **统一选择** — 选中商品后所有账户均标记 ✓，切换账户仅过滤商品库视图
- **分账户选择** — 各账户独立选择，仅当前账户标记 ✓

核心数据结构：
- `mockProductAccounts` — 账户数组，每个账户含 `libraries[]`，每个库含 `products[]`
- `allProductLibraries` — IIFE 计算，合并去重所有账户的商品库并附加 `accountId`/`accountName`
- `selectedProducts` — 单选数组（最多 1 个元素），每项含 `{accountId, libId, id, name, tag, episodes}`
- `currentAccountId` / `currentLibId` / `currentLibProducts` — 当前选中状态

### 其他 Mock 数据

- `mockMaterialData` — 素材对象数组（name, bookId, bookName, drama, lang, cost, doroi, d0AdRoi, d0Iaa, compliance, oldCompliance, created, businessType, media）
- `mockAccountPackages` / `mockTargetingPackages` — 账户包/定向包选项
- `autoTasks` / `campaigns` / `adGroups` / `adsList` — 各页面的表格数据

竞价策略使用 TikTok 术语：最大转化量 / 成本上限 / 最大投放量（销量模式无最大转化量）。
