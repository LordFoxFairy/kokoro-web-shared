# kokoro-web-shared

Kokoro Web 的独立共享 package 仓库。这里仅发布浏览器安全、跨产品稳定的类型、状态、
i18n 引擎和工程基线；不承载产品页面、品牌事实、租户身份、BFF 地址、provider adapter、
数据库连接或私有 token。

## Packages

- `@kokoro/web-core`：无 React、CSS、网络依赖的资源/动作状态和公开 runtime 类型。
- `@kokoro/i18n`：无 DOM、无框架依赖的语言协商、插值和 fallback 引擎。
- `@kokoro/tsconfig`：共享 TypeScript 编译基线。

## Boundary

产品仓库只能通过 package public entry points 消费本仓库。共享包不反向 import
`kokoro`、`kokoro-mori` 或任何产品源码。产品词典、页面排列、主题 preset、音乐领域对象和
供应商适配器分别由产品或服务 owner 管理。

本地开发可使用显式的 `file:` package bridge；发布交付使用私有 registry、semver 和 lockfile。
