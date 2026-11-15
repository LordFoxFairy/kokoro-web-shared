# kokoro-i18n

共享 i18n 窄包引擎。framework-agnostic、无第三方依赖、无 DOM/React。

## 公开 API（`@kokoro/i18n`，单文件 barrel = `src/index.ts`）

- `createI18n(config)` → `I18n`：注入 locale 全集、源语言完整词典 `source`、增量覆盖 `overrides`，得到 `{ locales, defaultLocale, negotiate, translate }`。
- `interpolate(template, vars)`：`{name}` 占位插值；缺失变量原样保留占位符。
- 类型：`I18nConfig` / `I18n` / `TranslateFn` / `InterpolationVars`。

## 契约

- 三层 fallback 恒不裸露 key：`overrides[locale][key]` → `source[key]`（源语言全量）→ `key` 本身。
- `negotiate(stored, navigatorLanguages)`：显式偏好优先 → 浏览器语言前缀匹配 → 源语言。非法值忽略。
- `source` 必须是 `defaultLocale` 的完整词典；`overrides` 为其它 locale 的 Partial 覆盖。

## 消费方

当前消费方由各应用清单记录。新应用接入时必须在自己的边界内提供词典和框架绑定。

## 制品准备

本地 workspace 入口仍为 `src/index.ts`；`pnpm pack` 用现有 TypeScript 构建，
通过 `publishConfig.exports` 将制品入口设为 `dist/index.js` 与 `dist/index.d.ts`。
仅打包 `dist` 与标准 manifest，不要求消费方编译 `node_modules` 内 TypeScript。
生成目录不提交，源码与公开 API 不变。

发行目标固定为 CNB 通用私有库
`https://npm.cnb.cool/awesomeaicode/npm/registry/-/packages/`，包名保持 `@kokoro/i18n`。
包按内部用途标识 `UNLICENSED`，根包与其它包仍为 private；制品库自身保持 Private、禁止覆盖。
`publishConfig.registry` 固定发行位置，不发布到 npmjs。正式发行必须有发布权限，
从已审查提交发布未占用版本，并核真实 registry 的版本、integrity 与独立拉取结果。
源码版本本身不证明发行成功，禁止覆盖或将 E404 冒称已获完整版本清单。

在 shared 根目录使用已固定 pnpm 与 Node22：

```bash
pnpm --filter @kokoro/i18n lint
pnpm --filter @kokoro/i18n typecheck
pnpm --filter @kokoro/i18n test
pnpm --dir packages/i18n pack --pack-destination /tmp/kokoro-i18n-release
```

审查合 main 后，从精确提交执行 `pnpm --dir packages/i18n publish --access restricted`。
认证仅放用户私有 npm 配置，不进源码或命令参数；App/CI 使用独立只读身份。
App 只在真实 registry 拉取验证后切换精确依赖；修正发行递增版本，不覆盖已有制品。

## 扩展规则

- 新增消费方：各自建 `messages`（源）+ `overrides`（增量）+ 框架绑定，注入本引擎。引擎不持有任何具体词典。
- 引擎保持纯函数、零依赖；React/DOM/存储等副作用留在消费方绑定层。
- 包内 ESLint flat config、测试和精确工具依赖自行维护；isolated linker 下不依赖应用或
  根目录的幽灵工具依赖。
