# CodeGraph 支持的语言与框架路由

> 官方来源：[README.md](https://github.com/colbymchenry/codegraph)

## 支持的语言（20+）

| 语言 | 扩展名 | 状态 |
|------|--------|------|
| TypeScript | `.ts`, `.tsx` | 完全支持 |
| JavaScript | `.js`, `.jsx`, `.mjs` | 完全支持 |
| Python | `.py` | 完全支持 |
| Go | `.go` | 完全支持 |
| Rust | `.rs` | 完全支持 |
| Java | `.java` | 完全支持 |
| C# | `.cs` | 完全支持 |
| PHP | `.php` | 完全支持 |
| Ruby | `.rb` | 完全支持 |
| C | `.c`, `.h` | 完全支持 |
| C++ | `.cpp`, `.hpp`, `.cc` | 完全支持 |
| Objective-C | `.m`, `.mm`, `.h` | 部分支持（类、协议、方法、`@property`、`#import`、消息发送；`.mm` ObjC++ 可能解析不完整） |
| Swift | `.swift` | 完全支持 |
| Kotlin | `.kt`, `.kts` | 完全支持 |
| Scala | `.scala`, `.sc` | 完全支持（类、trait、方法、类型别名、Scala 3 枚举） |
| Dart | `.dart` | 完全支持 |
| Svelte | `.svelte` | 完全支持（script 提取、Svelte 5 runes、SvelteKit 路由） |
| Vue | `.vue` | 完全支持（script + script-setup 提取、Nuxt page/API/middleware 路由） |
| Liquid | `.liquid` | 完全支持 |
| Pascal / Delphi | `.pas`, `.dpr`, `.dpk`, `.lpr` | 完全支持（类、记录、接口、枚举、DFM/FMX 表单文件） |
| Lua | `.lua` | 完全支持（函数、带接收器的方法、局部变量、`require` 导入、调用边） |
| Luau | `.luau` | 完全支持（Lua 的所有功能，加上 `type`/`export type` 类型别名、类型化签名、Roblox 实例路径 `require`） |

## 跨文件覆盖率

影响和 blast-radius 查询的质量取决于背后的依赖图质量。**公平覆盖率** = 具有至少一个*已解析跨文件依赖*的符号承载源文件的份额——在每个语言的真实基准仓库上测量。

| 语言 | 基准仓库 | 覆盖率 |
|------|---------|--------|
| TypeScript / JavaScript | this repo | 95.8% |
| Python | psf/requests | 100% |
| Go | gin-gonic/gin | 96.6% |
| Rust | BurntSushi/ripgrep | 86.7% |
| Java | google/gson | 93.3% |
| C# | jbogard/MediatR | 85.2% |
| PHP | guzzle/guzzle | 100% |
| Ruby | sidekiq/sidekiq | 100% |
| C | redis/redis | 92.2% |
| C++ | google/leveldb | 94.8% |
| Objective-C | SDWebImage | 91.6% |
| Swift | Alamofire | 95.3% |
| Kotlin | square/okhttp | 96.2% |
| Scala | gatling/gatling | 91.2% |
| Dart | flutter/packages | 92.4% |
| Svelte / SvelteKit | sveltejs/realworld | 100% |
| Vue / Nuxt | nuxt/movies | 93.5% |
| Lua | nvim-telescope/telescope.nvim | 84.2% |
| Luau | dphfox/Fusion | 92.2% |
| Liquid | Shopify/dawn | 73.8% |
| Pascal / Delphi | PascalCoin | 75.7% |

## 框架路由识别（14 个框架）

CodeGraph 检测 Web 框架路由文件并生成 `route` 节点，通过 `references` 边链接到处理器类或函数。

| 框架 | 识别的路由模式 |
|------|-------------|
| **Django** | `path()`, `re_path()`, `url()`, `include()` in `urls.py`（CBV `.as_view()`、点分路径） |
| **Flask** | `@app.route('/path', methods=[...])`, blueprint 路由 |
| **FastAPI** | `@app.get(...)`, `@router.post(...)`, 所有标准方法 |
| **Express** | `app.get(...)`, `router.post(...)` 含中间件链 |
| **NestJS** | `@Controller` + `@Get/@Post/...`, GraphQL `@Resolver` + `@Query/@Mutation`, `@MessagePattern`/`@EventPattern`, `@SubscribeMessage` |
| **Laravel** | `Route::get()`, `Route::resource()`, `Controller@action`, 元组语法 |
| **Drupal** | `*.routing.yml` 路由（`_controller`, `_form`, 实体处理器）；`hook_*` 实现在 `.module`/`.theme`/`.install`/`.inc` |
| **Rails** | `get '/x', to: 'users#index'`, hash-rocket `=>` 语法 |
| **Spring** | `@GetMapping`, `@PostMapping`, `@RequestMapping` 在方法上 |
| **Gin / chi / gorilla / mux** | `r.GET(...)`, `router.HandleFunc(...)` |
| **Axum / actix / Rocket** | `.route("/x", get(handler))` |
| **ASP.NET** | `[HttpGet("/x")]` 属性在 action 方法上 |
| **Vapor** | `app.get("x", use: handler)` |
| **React Router / SvelteKit** | 路由组件节点 |

### 框架路由覆盖率验证

| 框架 | 覆盖率 |
|------|--------|
| Express | 100% |
| FastAPI | 98% |
| Flask | 100% |
| NestJS | 96.8% |
| Gin | 96.5% |
| Axum | 100% |
| Rocket | 93.8% |
| Vapor | 100% |
| Laravel | 92% |
| Rails | 89.6% |
| React Router | 100% |
| ASP.NET | 83.9% |
| Spring | 83.3% |
| Drupal | 78.9% |
| Django | 74.1% |

## 跨语言桥接（iOS / React Native / Expo）

CodeGraph 桥接多语言代码库中的语言边界，使 `trace`、`callers`、`callees` 和 `impact` 能跨语言端到端连接。

| 边界 | JS / Swift 侧 | 原生侧 | 方式 |
|------|---------------|--------|------|
| **Swift → ObjC** | Swift `obj.foo(bar:)` | ObjC selector `-fooWithBar:` | `@objc` 自动桥接规则 + Cocoa 前缀 |
| **ObjC → Swift** | ObjC `[obj fooWithBar:]` | Swift `@objc func foo(bar:)` | 反向桥接名称候选 |
| **RN legacy bridge** | JS `NativeModules.X.fn(...)` | ObjC `RCT_EXPORT_METHOD` / Java `@ReactMethod` | 解析宏/注解声明构建 JS→原生映射 |
| **RN TurboModules** | JS `import M from './NativeM'; M.fn(...)` | 原生实现匹配 Codegen spec | 以 `Native<X>.ts` spec 接口为准 |
| **RN native→JS events** | JS `new NativeEventEmitter(...)` | ObjC `sendEventWithName:` / Swift `sendEvent(withName:)` / Java `.emit("e", ...)` | 合成跨语言事件通道 |
| **Expo Modules** | JS `requireNativeModule('X').fn(...)` | Swift / Kotlin `Module { Name("X"); AsyncFunction("fn") }` | 解析 Expo DSL 字面量 |
| **Fabric view components** | JSX `<MyView prop={v}/>` | TS Codegen spec + 原生实现类 | spec → `component` 节点 |
| **Legacy Paper view managers** | JSX `<MyView prop={v}/>` | ObjC `RCT_EXPORT_VIEW_PROPERTY` / Java `@ReactProp` | 同 Fabric |

每个桥接发出标记为 `provenance:'heuristic'` 的边，`metadata.synthesizedBy` 设为稳定通道名（如 `swift-objc-bridge`、`rn-event-channel`）。
