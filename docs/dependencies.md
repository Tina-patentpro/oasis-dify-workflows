# 依赖说明

> 下表从 12 份 YML **机械提取**（正则匹配 `provider:` / 环境变量 `name:` /
> `{{#…server_…#}}` 模板引用），用于定位配置项，**不是兼容性或可用性验证**。
> 本次发布没有连接任何 Dify 实例，也没有验证任何插件或模型的实际可用性。

## 1. 逐项依赖

| 流程目录 | 模型供应商（YML 内标识） | 声明的环境变量 | 其他凭据引用 |
|---|---|---|---|
| `flow-1-1-background-research` | langgenius/openrouter/openrouter | — | Bearer 占位值（需替换） |
| `flow-1-2-triz-analysis` | langgenius/openrouter/openrouter | — | — |
| `flow-1-3-overview-search` | langgenius/openrouter/openrouter | SERPER_API_KEY | — |
| `flow-1-4-solution-selection` | 未检出 | — | — |
| `flow-1-5-technical-disclosure` | langgenius/openrouter/openrouter | — | — |
| `flow-2-6-claims-draft` | langgenius/openrouter/openrouter | — | 工具凭据模板 ×6 |
| `flow-2-7-specification-draft` | langgenius/openrouter/openrouter | — | — |
| `flow-3-10-inventor-confirmation` | anthropic | — | — |
| `flow-4-11-claims-final` | langgenius/openrouter/openrouter | — | — |
| `flow-4-12-specification-final` | langgenius/openrouter/openrouter | — | — |
| `flow-5-13-final-check` | langgenius/openrouter/openrouter | — | — |
| `flow-6-14-office-action-response` | langgenius/openrouter/openrouter | — | — |

`Flow-1-4 技术方案筛选` 未检出模型 provider，请导入后自行确认其节点配置是否需要补充。

## 2. 模型供应商

- `langgenius/openrouter/openrouter`：多数流程使用的模型供应商标识。OpenRouter 是第三方
  聚合服务，**按量计费**，需要你自己的账户与 API Key；本仓库不含其凭据。
- `anthropic`：`flow-3-10-inventor-confirmation` 中检出。同样需要你自己的账户与凭据。

> 这些标识只是 DSL 中的配置引用。本仓库**不分发**相应插件代码、不授予使用权，
> 也不推定你已获得这些服务的使用许可。导入前请自行核对服务条款与费用。

## 3. 环境变量与工具凭据

| 类型 | 出现位置 | 处理方式 |
|---|---|---|
| `SERPER_API_KEY` | `flow-1-3-overview-search` | YML 只声明描述（"请在 Dify 平台环境变量中配置"），**无真实值**；按你的 Dify 环境变量机制自行填入 |
| `{{#<id>.server_*#}}` ×6 | `flow-2-6-claims-draft` | Dify 工具／插件凭据的**模板变量引用**，导入后重新绑定到你的凭据；不是可直接使用的密钥 |
| `Bearer YOUR_API_KEY_HERE` | `flow-1-1-background-research` 第 330 行 | **脱敏占位值**，必须替换为你自己的凭据，否则对应 HTTP 节点无法工作 |

**没有任何一份 YML 含真实 API Key、Token、Cookie、密码或带凭据的连接串。**
若你在自己的实例里填入了真实值，请勿把导出文件再提交回公开仓库。

## 4. 其他外部依赖（未随包分发）

| 依赖 | 说明 |
|---|---|
| Dify 实例 | 你需要自备；本仓库不含服务端地址、部署脚本或后台服务 |
| 插件包 | YML 中若引用工具插件，需自行核对来源、版本与许可后安装 |
| 知识库 / 数据集 | 若节点引用知识检索，需自行准备语料；本仓库不分发任何知识库内容 |
| 文件服务 | 文档抽取、文件上传等节点依赖实例的文件能力 |
| 自定义 Code 节点依赖 | 若节点代码引用第三方库，需在你的 Dify 运行环境中自行确认可用性 |

DSL `version` 跨 `0.1.2` ~ `0.6.0`，**schema 版本混杂**；不同 Dify 版本对旧 schema 的
兼容程度未知，本次未验证。

## 5. 费用提示

- 下载与使用本仓库**免费**（Apache-2.0 许可的源码与资源）；
- 模型推理、检索服务（如 Serper）、图像服务通常由第三方按量计费，费用由你与服务商结算；
- 本仓库**不提供**免费公共推理接口，也不承诺任何额度、服务等级或效果指标；
- 本次发布过程**未执行**任何收费业务调用；
- 云端服务、模型费用、专利撰写咨询与私有部署是与本仓库分离的商业事项。

## 6. 缺件时怎么办

按 [导入与配置指南](import-guide.md) 逐项补齐或**明确停用对应分支并留痕**。
不要通过删除必需依赖，发布一个看似完整但无法使用的图；也不要把生产服务器地址
默认写成公共免费接口。
