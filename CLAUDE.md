# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Next Web Nav 是一个基于 Next.js 16 的现代化导航网站，支持可视化编辑导航数据、主题切换和本地持久化存储。

## 开发命令

```bash
# 开发服务器（使用 Turbopack 加速）
pnpm dev

# 生产构建
pnpm build

# 启动生产服务器
pnpm start

# 代码检查（Next.js 16 使用 ESLint 9 + 扁平配置）
pnpm lint          # 运行 ESLint 检查
# 注意：Next.js 16 已移除 `next lint` 命令

# 类型检查
pnpm typecheck

# 代码格式化
pnpm format:write  # 格式化代码
pnpm format:check  # 检查格式
```

## 技术栈

- **框架**: Next.js 16 (App Router)
- **UI**: shadcn/ui + Radix UI + Tailwind CSS
- **状态管理**: Zustand + persist 中间件
- **主题**: next-themes (支持明暗主题切换)
- **动画**: Framer Motion
- **类型**: TypeScript (strict mode)
- **代码质量**: ESLint 9 (扁平配置) + Prettier

## 架构说明

### 目录结构

- `app/` - Next.js App Router 页面
- `components/` - React 组件
  - `ui/` - shadcn/ui 基础组件
  - `site-header/` - 导航栏组件
  - `link-content.tsx` - 导航链接内容展示
  - `sidebar.tsx` - 侧边栏
- `config/site.ts` - 站点配置和初始导航数据
- `stores/` - Zustand 状态管理
- `lib/` - 工具函数

### 状态管理

使用 Zustand 管理导航数据，通过 `persist` 中间件持久化到 localStorage（key: `nav-config-store`）。

**核心 store**: `stores/useConfigStore.ts`

状态结构:
```typescript
interface NavLinkItem {
  icon: string    // 图标 URL
  title: string   // 标题
  desc: string    // 描述
  link: string    // 链接
}

interface NavCategory {
  title: string        // 分类名称
  items: NavLinkItem[] // 链接列表
}
```

可用操作:
- `setCategories(data)` - 设置所有分类
- `addCategory(category)` - 添加分类
- `updateCategory(index, category)` - 更新分类
- `removeCategory(index)` - 删除分类
- `addLink(categoryIndex, link)` - 添加链接
- `updateLink(categoryIndex, linkIndex, link)` - 更新链接
- `removeLink(categoryIndex, linkIndex)` - 删除链接

**初始数据重置**: 使用 `config/site.ts` 中导出的 `NavData` 数组作为默认数据。

### 路径别名

使用 `@/*` 指向项目根目录（在 `tsconfig.json` 中配置）。

### 主题系统

- 使用 `next-themes` 提供明暗主题切换
- 主题设置通过 `ThemeProvider` 在 `app/layout.tsx` 中注入
- 默认主题: `system`（跟随系统）

### 样式规范

- 使用 Tailwind CSS 实用类
- 遵循 shadcn/ui 设计规范
- 间距使用 4 的倍数
- 支持响应式设计

### Next.js 配置

- 启用 `reactStrictMode`
- 生产环境移除 console 日志 (`removeConsole: true`)
- 允许所有 HTTPS 域名的远程图片
- package.json 设置为 ES module (`"type": "module"`)

### ESLint 配置

Next.js 16 使用 ESLint 9 扁平配置格式 (`eslint.config.js`)。配置包括：
- TypeScript 支持 (typescript-eslint)
- React + React Hooks 规则
- Next.js 核心规则 (@next/eslint-plugin-next)
- 已移除旧的 `.eslintrc.json` 格式（不再支持）

## 编辑导航数据

可通过两种方式修改导航数据:

1. **可视化编辑** (推荐): 点击右上角设置图标，在设置对话框中编辑
2. **代码编辑**: 直接修改 `config/site.ts` 中的 `NavData` 数组

修改后需要清除 localStorage 中的 `nav-config-store` 或使用"重置为默认"功能以应用代码更改。
