# 前端体系 · 四大核心模块

**模块化**决定代码怎么组织 · **工程化**决定怎么构建交付 · **架构化**决定整体怎么设计 · **规范化**作为横切约束贯穿其余三者

## 前言：四个模块的关系

| 模块       | 回答的问题                 | 核心手段                       | 一句话定位                         |
| ---------- | -------------------------- | ------------------------------ | ---------------------------------- |
| **工程化** | 代码怎么构建、测试、交付   | 工具链、自动化、CI/CD          | "怎么把代码变成可上线的产品"       |
| **模块化** | 代码怎么组织、依赖怎么管理 | 模块规范、打包器、复用方式     | "怎么把代码切成块、块之间怎么引用" |
| **规范化** | 团队怎么统一协作标准       | 风格约束、类型、Git 约定       | "怎么让多人写得像一个人"           |
| **架构化** | 应用整体怎么设计           | 分层、状态、渲染、微前端等模式 | "系统能长多大、多稳、多易维护"     |

::: warning 团队开发与维护

- 工程化（构建/测试/发布）
- 模块化（依赖治理）
- 规范化（协作标准）
- 架构化（整体设计）

:::

## 学习地图：从入门到精通

- **学习视角（个人知识构建，自下而上）**：框架 → 模块化 → 工程化 → 规范化 → 架构化。
- **设计视角（构建系统的决策，自上而下）**：架构化 → 框架 → 模块化 → 工程化 → 规范化。

### 入门阶段

- **模块化**：会用 ESM 的 `import/export`；看得懂 `package.json`
- **工程化**：会用 Vite 跑起一个项目；`npm install` 装依赖
- **规范化**：会用 `ESLint + Prettier`；知道 `Conventional Commits` 前缀
- **架构化**：会用 `组件 + 路由` 搭一个 CRUD 页面
- **标志性产出**：一个能本地运行的小项目

### 进阶阶段

- **模块化**：理解 `Tree Shaking`、`动态 import`、`循环依赖`
- **工程化**：配置 `Webpack/Vite/Rollup`；写 CI 流水线；做拆包与缓存
- **规范化**：落地 `husky + lint-staged + 分支模型 + PR 评审`
- **架构化**：用状态管理库、理解 CSR/SSR 差异、组件分层
- **标志性产出**：可自动化测试与发布的仓库

### 精通阶段

- **模块化**：设计依赖治理策略、Module Federation 落地
- **工程化**：搭建 Monorepo、构建性能优化、可观测体系
- **规范化**：沉淀团队规范文档、设计系统、`a11y/i18n` 落地
- **架构化**：微前端、BFF、Clean Architecture、并发渲染
- **标志性产出**：支撑多团队、大规模、高可用的前端体系

## 工程化（入门到精通）

::: tip 工具链与流程：构建 · 测试 · 交付 · 监控。— 用工具与自动化流程保障开发、构建、质量与交付
:::

### 构建工具链

| 工具      | 定位                      | 说明                                                                       |
| --------- | ------------------------- | -------------------------------------------------------------------------- |
| Rolldown  | Rust 编写                 | Rust+Oxc，兼容 Rollup 插件，取代 Rollup，Vite 团队（尤雨溪等）主导         |
| Webpack   | 最成熟通用打包器          | 模块图 + Loader（转译资源）+ Plugin（扩展流程）；支持 HMR / Code Splitting |
| Vite      | 开发体验标杆              | Vite 8.x（ESM -> Rollup），旧版（ESM -> esbuild -> Rollup）                |
| Rollup    | ESM 库打包首选            | 精细 Tree Shaking，产出干净无运行时，适合 npm 库                           |
| esbuild   | 高速转译引擎              | Go 多线程，转译/压缩/打包极快                                              |
| Parcel    | 零配置开箱即用            | 自动推断依赖与转译，适合快速原型/小项目                                    |
| Turbopack | Rust 编写的**增量打包器** | 只编译被请求的模块+增量缓存，当前主要服务 Next.js 开发态，生态成长中       |

> [Rolldown 路线图](https://github.com/rolldown/rolldown/discussions/153)

### 构建工具链详解

```sh
├── Vite："极致开发体验"著称的新一代构建工具
|   ├── 开发服务器：原生ESM
|   ├── 旧版
|   |    ├── 依赖预构建：esbuild
|   |    └── 生产构建：Rollup
|   ├── Vite 8.x
|   |    ├── 依赖预构建：Rolldown
|   |    └── 生产构建：Rolldown
├── Rolldown：用 Rust 重写 Rollup，替代 Rollup。
├── Rollup：库打包首选，面向 ESM 库的打包器，产出干净、体积小。
├── Turbopack：Rust 编写，底层也借鉴 esbuild 级思路，主打增量按需编译的下一代打包器。
├── Webpack：自成体系（JS 实现），历史上最主流、生态最完整的通用打包器。
├── Parcel：自成体系（零配置，内置多语言支持），零配置、开箱即用的打包器。
└── esbuild：被当作"速度引擎"嵌入 Vite / Turbopack 等上层工具
```

### 包管理与仓库

| 工具 | 依赖安装               | 作用                 |
| ---- | ---------------------- | -------------------- |
| npm  | 从嵌套到扁平化         | 依赖安装、管理、解析 |
| yarn | 从“扁平结构”到“无结构” | 依赖安装、管理、解析 |
| pnpm | 软链接 + 硬链接        | 依赖安装、管理、解析 |

::: danger Monorepo 解决了什么问题？什么场景不该用？

1️⃣ Monorepo 解决了什么问题？

- **① 原子变更** — 避免“先发包 A 等发布再改包 B”的多仓库 chore 流程；
- **② 统一工具链** — lint/TS/CI 配置和版本策略只有一份；
- **③ 重构友好** — 配合 Nx 的依赖图可以做受影响分析，改一处就知道要测哪些包；
- **④ 依赖统一去重** — 避免多仓库各自升级造成的版本漂移；

2️⃣ 什么场景不该用？

- **① 单团队、单应用、代码量小** — Monorepo 的工具链成本（缓存、编排、依赖图维护）远超收益；
- **② 对权限隔离有硬性要求的组织** — Monorepo 天然全员可见全部代码，只能约束“改”，不能约束“看”；
- **③ 与外部有强隔离需求的开源库/第三方交付仓库**，polyrepo 边界更清晰；
- **④ CI 无法配套缓存工具的团队** — 全量构建耗时会让仓库成为瓶颈。

:::

### 质量与测试

#### 静态检查

- 1. 代码规范：`ESLint`
- 2. 代码格式化：`Prettier`
- 3. Git提交规范：`husky + lint-staged`（Git 提交前钩子只检查暂存文件）。

#### 测试分层

- 1. 单元测试：`Jest / Vitest`（Vitest 与 Vite 同源，启动快、ESM 友好）；
- 2. 组件测试：`Testing Library`（以用户行为为导向）、`Vue Test Utils`；
- 3. 端到端 E2E：`Playwright`（跨浏览器、强稳定性）/ `Cypress`（开发者体验好）。

### 流程与发布

- **CI/CD**
  > GitHub Actions、GitLab CI、Jenkins；典型流水线：lint → test → build → deploy。
- 发布工程
  - 产物文件名带内容 hash（配合 CDN 长缓存，内容变则文件名变）；
  - 灰度发布、一键回滚；
  - Docker 容器化部署（把构建产物 + 静态服务器打进镜像，环境一致）。

### 性能与监控

- **构建性能**
  > 持久化缓存、并行构建、拆包（runtime / vendor / 业务分离）。
- **运行时性能**
  > 代码分割 + 按需加载、gzip / brotli 压缩、图片（WebP/AVIF）与字体（font-display: swap）优化。
- **可观测**
  > Source Map 定位线上报错（Sentry 等）、Web Vitals 指标（LCP / INP / CLS）、Lighthouse 审计。

## 模块化（入门到精通）

::: tip 代码组织与依赖：规范 · 打包 · 复用。— 代码组织、依赖管理与复用的机制
:::

## 规范化（入门到精通）

::: tip 协作统一：风格 · 类型 · Git · 文档 · 标准。— 团队协作的统一约定与工程标准
:::

## 架构化（入门到精通）

::: tip 整体设计：分层 · 状态 · 渲染 · 微前端 · 跨端。— 应用整体结构与模式设计
:::

### 0. 架构化要解决的问题

当应用从"几个页面"长到"几十个业务域"，真正痛的是：**状态乱飞、组件耦合、渲染策略不清、多团队协作相撞**。架构化就是用分层、状态、渲染、微前端等模式，让系统"能长多大、多稳、多易维护"。

---

### 1. 组件架构

#### 1.1 容器组件 vs 展示组件（React）

::: code-group

```tsx [React]
// 展示组件：纯 props 驱动，无取数逻辑，易测试
function UserCard({ name, avatar }: { name: string; avatar: string }) {
  return (
    <div className="card">
      <img src={avatar} alt={name} />
      <span>{name}</span>
    </div>
  );
}

// 容器组件：负责取数与状态
function UserContainer({ id }: { id: string }) {
  const { data, loading } = useUser(id); // 自定义 Hook 取数
  if (loading) return <Spinner />;
  return <UserCard name={data.name} avatar={data.avatar} />;
}
```

```vue [Vue]
<!-- UserCard.vue 展示组件 -->
<template>
  <div class="card">
    <img :src="avatar" :alt="name" />
    <span>{{ name }}</span>
  </div>
</template>
<script setup lang="ts">
defineProps<{ name: string; avatar: string }>();
</script>

<!-- UserContainer.vue 容器组件 -->
<script setup lang="ts">
import { useUser } from "@/composables/useUser";
const props = defineProps<{ id: string }>();
const { data, loading } = useUser(props.id);
</script>
```

:::

#### 1.2 逻辑复用：Hooks（React） vs Composables（Vue）

::: code-group

```ts [Hooks（React）]
// React 自定义 Hook
function useToggle(initial = false) {
  const [on, setOn] = useState(initial);
  const toggle = useCallback(() => setOn((v) => !v), []);
  return [on, toggle] as const;
}
```

```ts [Composables（Vue）]
// Vue 组合式函数（Composable）
import { ref } from "vue";
export function useToggle(initial = false) {
  const on = ref(initial);
  const toggle = () => (on.value = !on.value);
  return { on, toggle };
}
```

:::

> 现代复用首选 Hooks / Composables：**组合优于继承**，横向抽离状态逻辑而非层层包裹。

#### 1.3 原子设计落地

```
src/components/
├── atoms/      Button, Input, Avatar
├── molecules/  SearchBar (Input + Button)
├── organisms/  UserCard (Avatar + Name + Actions)
├── templates/  PageLayout
└── pages/      UserProfilePage
```

#### 1.4 跨层级通信：Context（React）vs provide/inject（Vue）

::: code-group

```tsx [Context（React）]
// React：高频变化的状态不要放 Context，否则所有消费者重渲染
const ThemeContext = createContext<"light" | "dark">("light");
function App() {
  const [theme] = useState<"light" | "dark">("light");
  return (
    <ThemeContext.Provider value={theme}>{/* ... */}</ThemeContext.Provider>
  );
}
```

```ts [provide/inject（Vue）]
// Vue：provide / inject 跨层级共享
// 祖先
import { provide, ref } from "vue";
provide("theme", ref("light"));
// 后代
import { inject } from "vue";
const theme = inject("theme");
```
:::

### 2. 状态管理

#### 2.1 客户端状态：Zustand（React，轻量）

```ts
import { create } from "zustand";
interface CartState {
  items: string[];
  add: (item: string) => void;
}
export const useCart = create<CartState>((set) => ({
  items: [],
  add: (item) => set((s) => ({ items: [...s.items, item] })),
}));
// 使用：const add = useCart(s => s.add)
```

#### 2.2 客户端状态：Pinia（Vue，官方推荐）

```ts
// stores/cart.ts
import { defineStore } from "pinia";
export const useCart = defineStore("cart", {
  state: () => ({ items: [] as string[] }),
  actions: {
    add(item: string) {
      this.items.push(item);
    },
  },
});
// 组件内：const cart = useCart(); cart.add('book')
```

#### 2.3 Redux Toolkit（React，复杂可预测流）

```ts
import { createSlice, configureStore } from "@reduxjs/toolkit";
const cart = createSlice({
  name: "cart",
  initialState: { items: [] as string[] },
  reducers: {
    add: (s, a) => {
      s.items.push(a.payload);
    },
  }, // 内部"可变"写法，RTK 用 Immer 保证不可变
});
export const store = configureStore({ reducer: { cart: cart.reducer } });
```

#### 2.4 服务端状态：TanStack Query（React）/ SWR

```ts
// React：取数缓存/重取/失效，和本地 UI 状态分离
import { useQuery } from "@tanstack/react-query";
function useUser(id: string) {
  return useQuery({
    queryKey: ["user", id],
    queryFn: () => fetch(`/api/user/${id}`).then((r) => r.json()),
    staleTime: 60_000,
  });
}
```

Vue 等价（`@tanstack/vue-query` 或 `swr-vue`）：

```ts
import useSWR from "swr";
const { data, error } = useSWR(`/api/user/${id}`, fetcher);
```

> **关键区分**：来自 API、会过期、需缓存的服务端状态，交给 TanStack Query/SWR；本地 UI 状态才放进 Redux/Zustand/Pinia。

---

### 3. 渲染架构（CSR/SSR/SSG/ISR）

#### 3.1 Next.js 渲染策略选择

```tsx
// app/page.tsx —— 默认 SSR（动态）
export default async function Page() {
  const data = await fetch("...").then((r) => r.json()); // 每次请求服务端取数
  return <List data={data} />;
}

// 静态生成 SSG
export const dynamic = "force-static";

// 增量再生 ISR：每 60s 重建一次
export const revalidate = 60;
```

#### 3.2 水合（Hydration）注意事项

```tsx
// ❌ 服务端/客户端不一致会导致 hydration mismatch
function Bad() {
  const now = new Date().toISOString(); // 两端时间不同
  return <time>{now}</time>;
}
// ✅ 用 useEffect 在客户端再赋值，或标记 suppressHydrationWarning
function Good() {
  const [now, setNow] = useState("");
  useEffect(() => setNow(new Date().toISOString()), []);
  return <time suppressHydrationWarning>{now}</time>;
}
```

#### 3.3 CSR vs SSR 取舍

| 场景               | 推荐      | 理由             |
| ------------------ | --------- | ---------------- |
| 后台 / 工具类      | CSR       | 不需 SEO，交互重 |
| 内容 / 电商 / 营销 | SSR / SSG | SEO 好、首屏快   |
| 博客 / 文档        | SSG / ISR | 构建期生成，最快 |

---

### 4. 微前端

#### 4.1 qiankun 主应用注册子应用

```ts
// 主应用
import { registerMicroApps, start } from "qiankun";
registerMicroApps([
  {
    name: "sub-vue",
    entry: "//localhost:7100", // 子应用入口
    container: "#subapp",
    activeRule: "/sub-vue",
  },
]);
start();
```

```js
// 子应用（Vue）导出生命周期
export async function mount(props) {
  render(App);
}
export async function unmount() {
  app.unmount();
}
```

#### 4.2 Module Federation（webpack 5 运行时共享）

```js
// 宿主 webpack.config.js
const { ModuleFederationPlugin } = require("webpack").container;
plugins: [
  new ModuleFederationPlugin({
    name: "host",
    remotes: { remote: "remote@http://localhost:3001/remoteEntry.js" },
  }),
];
// 使用：const RemoteBtn = lazy(() => import('remote/Button'))
```

> 微前端何时**不该**用：小团队、单一产品、无并行发布需求——拆了反而增加通信与重复依赖成本。

---

### 5. 工程模式（Clean Architecture / Fiber）

#### 5.1 Clean Architecture 分层（前端落地）

```
src/
├── domain/       纯业务规则（不依赖框架，可单测）
│   └── user.ts   export function canCheckout(user) {...}
├── usecases/     应用用例（编排 domain）
│   └── checkout.ts
├── adapters/     接口适配（API、存储）
│   └── userApi.ts
└── ui/           React/Vue 组件（最外层，依赖内层）
```

> 依赖规则**只能向内**：`ui → usecases → domain`，内层不感知外层，核心业务逻辑可独立测试、可换框架。

#### 5.2 React Fiber 与并发渲染

```tsx
// 并发特性：用 startTransition 标记非紧急更新，避免长任务阻塞输入
import { startTransition, useState } from "react";
function Search() {
  const [query, setQuery] = useState("");
  const [list, setList] = useState([]);
  function onChange(e) {
    setQuery(e.target.value); // 紧急：输入框立即响应
    startTransition(() => {
      // 非紧急：列表可被打断
      setList(filterBigList(e.target.value));
    });
  }
}
```

> Fiber 把渲染拆成可中断的小任务，支撑 `Suspense` / `startTransition` 等并发能力，避免主线程长任务卡顿。

#### 5.3 安全架构要点

```http
# CSP：限制可执行脚本来源，防 XSS 注入
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-xxx'
```

- XSS
  > 所有用户输入做转义/校验（框架默认转义，慎用 `dangerouslySetInnerHTML` / `v-html`）；
- CSRF
  > 同源策略 + Token 校验；
- SRI
  > `<script integrity="sha384-...">` 防 CDN 资源被篡改。

---

### 附：架构选型决策表

| 问题         | 选项                              | 触发条件               |
| ------------ | --------------------------------- | ---------------------- |
| 状态放哪？   | Redux / Zustand / Pinia           | 本地 UI 状态           |
|              | TanStack Query / SWR              | 服务端取数缓存         |
| 渲染方式？   | CSR / SSR / SSG / ISR             | 看 SEO 与更新频率      |
| 要微前端吗？ | qiankun / ModuleFederation / 无界 | 多团队并行 + 独立部署  |
| 要跨端吗？   | RN / Flutter / uni-app / Taro     | 需覆盖 App / 小程序    |
| 要 BFF 吗？  | 自建中间层                        | 前端直连多后端成本高   |
| 要 DDD 吗？  | Clean Architecture                | 业务复杂度高、需强可测 |

> 架构化的核心不是"用最牛的"，而是**为当前规模选最合适、且能平滑演进**的方案。
