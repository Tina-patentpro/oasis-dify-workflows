# 导入与配置指南

> **本指南描述的是"可以尝试怎么做"，不是"已经验证成功的操作步骤"。**
> 本次发布**没有**在任何 Dify 实例上执行导入或运行，也没有验证任何具体 Dify 版本的兼容性。
> 12 份 YML 是本地设计源，`evidence_status=STATIC_ONLY`。

## 0. 导入前请先想清楚

这些 YML 的价值主要在**可读、可 diff、可讨论**：节点怎么排、提示词怎么写、变量与 Schema 怎么定义。
如果你的目标是"马上得到一套能跑的专利撰写服务"，本仓库帮不了你——它不含模型凭据、插件包、
环境变量真实值、后台服务、部署脚本，也没有验证过导入。

## 1. 准备一个你完全控制的 Dify 环境

- 使用一个**独立、可丢弃**的 Dify 实例或工作区；
- 不要在生产实例、已发布应用或共享工作区上直接导入；
- 记录你的 Dify 版本，便于后续定位 DSL schema 兼容问题；
- 本仓库 12 份 YML 的 `version` 跨 `0.1.2` ~ `0.6.0`，**schema 版本混杂**，
  不同 Dify 版本对旧 schema 的支持程度不同，未知。

## 2. 下载与完整性核对

```bash
git clone https://github.com/Tina-patentpro/oasis-dify-workflows.git
cd oasis-dify-workflows
```

逐文件核对 `catalog.json` 记录的 SHA-256：

```bash
python3 - <<'PY'
import json, hashlib, pathlib, sys
c = json.loads(pathlib.Path("catalog.json").read_text(encoding="utf-8"))
bad = []
for w in c["workflows"]:
    p = pathlib.Path(w["path"])
    h = hashlib.sha256(p.read_bytes()).hexdigest() if p.is_file() else None
    ok = h == w["sha256"] and p.stat().st_size == w["bytes"]
    print(f"{w['slug']:<38} {'OK' if ok else 'MISMATCH'}")
    if not ok: bad.append(w["slug"])
sys.exit(1 if bad else 0)
PY
```

注意 `flow-1-1-background-research` 的 YML 因凭据脱敏，**其 hash 是发布副本的 hash**，
与源项目文件不同，这是预期差异，详见 [sources-and-licenses.md](sources-and-licenses.md)。

## 3. 只读审阅（推荐先做）

导入前先看清里面有什么：

- 打开 `.yml`，看 `app:` 段的 `name` / `mode` / `description` 与 `workflow.graph.nodes`；
- 关注 `model.provider`、`model.name`、Code 节点的提示词、环境变量声明、工具凭据引用；
- 读该流程目录下的 `NOTES.md`，其中列了依赖与已知问题；
- 对照 [依赖说明](dependencies.md) 判断你要准备什么。

**这一步不需要 Dify，也不会产生任何费用。**

## 4. 导入（未验证）

不同 Dify 版本的入口名称略有差异，通常是「创建应用 → 导入 DSL 文件 / Import DSL」。
请选择**单个 `.yml` 文件**，一次导入一个流程。

导入后请如实记录你自己的结果：Dify 版本、导入是否成功、报错原文、你补了哪些配置。
本仓库不提供"导入成功"的证据，也不会把你的成功导入反过来写成"本仓库已验证"。

**禁止事项：**

- 不要用导入结果覆盖**已有应用、已有草稿、环境变量、标签或发布状态**；
- 不要为了"看起来能导入"而删除必需节点、模型或工具引用——那会得到一个看似完整但不可用的图；
- 不要把生产服务器地址或凭据写回 DSL 并提交到公开仓库；
- 不要在未核对插件来源与许可的情况下启用第三方插件。

## 5. 依赖补齐顺序

1. **模型供应商**：多数流程引用 `langgenius/openrouter/openrouter`，
   `flow-3-10-inventor-confirmation` 引用 `anthropic`。请在你的 Dify 中配置对应模型供应商与凭据。
2. **环境变量**：`flow-1-3-overview-search` 声明 `SERPER_API_KEY`（YML 内无真实值），
   按 Dify 平台的环境变量配置方式自行填入。
3. **工具凭据模板**：`flow-2-6-claims-draft` 有 6 处 `{{#<id>.server_*#}}` 模板引用，
   是 Dify 工具／插件凭据的变量占位，导入后需重新绑定到你的工具凭据。
4. **HTTP 请求头（务必处理）**：`flow-1-1-background-research` 第 330 行是
   `Authorization: Bearer YOUR_API_KEY_HERE`。这是**脱敏占位值**，必须替换为你自己的凭据，
   否则该 HTTP 节点无法工作。相关密钥的原持有者应尽快轮换。
5. **知识库 / 文件服务 / 插件**：YML 中若有引用，需自行准备，本仓库不分发。

完整逐项清单见 [依赖说明](dependencies.md)。

## 6. 隔离验证

- 先建一个**独立的临时应用**验证，不要动已有应用；
- 只做**免费**的检查：图能否显示、节点是否齐全、变量与 Schema 是否完整、缺哪些配置；
- 若要做真实运行，请先明确受测版本、输入、环境与费用范围，并单独取得授权；
  **本仓库的发布不包含任何收费业务调用**；
- 把"导入成功 / 配置完成 / 运行成功 / 专业接受"分别记录，不要合并成一句"已验证"。

## 7. 出问题怎么反馈

在 [GitHub Issues](https://github.com/Tina-patentpro/oasis-dify-workflows/issues) 提交时请附：

| 字段 | 示例 |
|---|---|
| 资产 ID / 流程 | `Flow-2-6 权利要求初稿` |
| 版本与文件名 | `V1.2.1` / `flow-2-6-权利要求初稿-V1.2.1.yml` |
| 文件 SHA-256 | 从 `catalog.json` 取 |
| Dify 版本 | 你实际使用的版本 |
| 准确节点 / 行 | 报错指向的节点 ID 或 YML 行号 |
| 预期与实际 | 分开写 |
| 证据路径 | 报错原文、日志片段（**不要贴密钥**） |
| 影响 | 阻断了哪个流程／哪一步 |
| 需要的修正或验证结果 | 明确写出你需要什么 |

不要只写"无法使用，请处理"。涉及 DSL 或提示词的行为性修改，需要新的具名版本与差异记录，
见 [CONTRIBUTING.md](../CONTRIBUTING.md)。
