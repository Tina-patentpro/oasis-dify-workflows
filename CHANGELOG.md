# 变更记录

## 2026.11.07-rc1（预发布，首次公开）

- 首次公开发布 12 份专利撰写 Dify 工作流**本地设计源 YML**，逐项版本与 SHA-256 见
  [catalog.json](catalog.json)。
- 许可证 **Apache-2.0**（权利主体 OasisIntWrite），由用户于 2026-11-07 明确确认；
  与姊妹仓库是分开的独立授权决定，`OIW-RIGHTS-20261107-01` 不自动延伸至此。
- 每份附 `NOTES.md`（输入输出、依赖、选版依据与已知问题）；共用文档覆盖导入、
  依赖、验证范围、来源与许可。
- 12/12 通过 YAML 语法解析（Ruby Psych）；逐文件完成敏感内容扫描。
- `flow-1-1-background-research` 的 Bearer 凭据已脱敏为 `YOUR_API_KEY_HERE`，
  该文件为**唯一与源文件字节不同**的资产，差异已登记，尚待技术复核。
- 排除 `Flow-3-8`（源 YML 语法错误）与 `Flow-3-9`（无 YML）；两个 Chatflow 与
  专利附图说明生成暂缓，原因见 [README](README.md)。
- 本次发布**未执行**实例导入、真实运行、专业审阅或用户接受；**未产生任何收费调用**。

> 集合版本与单份 YML 的历史版本编号分开。本次发布没有把任何 YML 升格为源项目的
> "当前候选"（源登记册 14 个流程仍为 `current_candidate_yml=undesignated`）。
