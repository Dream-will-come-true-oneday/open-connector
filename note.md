# PR 编写思路

open-connector 以 `src/core` 的类型、JSON Schema、输入校验、执行结果和 SSRF 防护为基础；`src/providers/<service>` 是能力边界，`definition.ts` 声明目录元数据，`actions.ts` 描述业务契约，`runtime.ts`/`executors.ts` 负责凭据、请求和响应转换。生成脚本把定义汇总为 catalog 与延迟加载 registry，避免启动时加载全部 provider。Hono 服务层提供 `/v1`、`/api`、代理、连接管理和 OAuth，MCP 层用搜索、指南、执行三个发现型工具复用同一套 action runner；Transit File、策略和运行日志则由服务层统一管理。

PR 应先从官方 API 文档核对端点、认证、分页、错误和响应字段，再选择一个能补齐真实工作流的 action。定义侧用 `s` helper 写清必填项、边界、枚举和输出；执行侧复用 provider runtime 与 `context.fetcher`，保持请求可取消并接受统一错误映射，绝不直接调用全局 `fetch`。若有非平凡转换，补最小行为测试；随后运行 `npm run generate:catalog`、`npm run fix-check`，检查生成噪声、懒加载和权限声明，最后用聚焦的提交说明动机、API 对照、兼容性和验证结果。
