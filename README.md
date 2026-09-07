# china-finance-skills

> 中国会计准则财务技能包（精简重构版）— **2 个技能**覆盖记账理账到审计全流程。

基于《小企业会计准则》(财会[2011]17号)、《企业会计准则》(财会[2006]3号及后续修订) 和中国注册会计师审计准则 (CAS) 构建的 AI Agent 技能包，覆盖小企业（年收入<500万）→ 成长企业（500-3000万）→ 中型企业（3000万+）全规模段。

## 为什么只有 2 个技能

市面上多数技能包把每个知识点拆成独立技能，装上后几十个 description 常驻上下文，agent 还要在相似技能里做选择题。本仓库遵循 **渐进式披露（progressive disclosure）** 原则重新组织：

- **SKILL.md 只做入口路由**：工作流编排 + 按需加载指引，常驻上下文的只有 2 条 description
- **细节知识下沉 references/**：23 份参考文档按任务加载，用多少读多少
- **技能边界清晰**：做账（accounting）和查账（audit）是两类人群、两条工作流，触发判断零歧义
- **公司级记忆飞轮**：references 没有的科目与政策，经确认写入本地公司档案，越用越懂你的公司

## 技能一览

### 📗 accounting — 记账理账报表税务

| 参考文档 | 内容 |
|---------|------|
| `chart-of-accounts.md` | 标准科目表：小企业 66 科目 + 企业准则 167 科目，含编码和借贷方向 |
| `coa-mapping.md` | 两套准则科目双向对照、企业准则独有科目、编号对照 |
| `journal-entry.md` | 记账凭证编制，借贷平衡验证 |
| `month-end-close.md` | 月末结账：小企业 5 步 / 企业准则 7 步（含减值+递延税+公允价值） |
| `profit-loss.md` / `balance-sheet.md` / `cash-flow.md` | 三大报表，两套准则版本 |
| `margins.md` | 毛利率/净利率分析：驱动因素拆解 + 行业对标 + 利润质量 |
| `vat.md` / `income-tax.md` / `individual-tax.md` | 增值税（数电发票）/ 企业所得税（含递延税）/ 个人所得税 |
| `tax-rates.md` | 2025-2026 税率速查（增值税/企税/个税/附加税/印花税） |

### 📕 audit — 审计程序

| 参考文档 | 内容 |
|---------|------|
| `risk-assessment.md` | 风险评估（CAS 1211），识别重大错报风险 |
| `materiality.md` | 重要性水平计算（CAS 1221） |
| `cash.md` / `receivables.md` / `inventory.md` / `fixed-assets.md` / `payables.md` | 货币资金/应收/存货/固定资产/应付实质性程序（函证、监盘、账龄分析、截止测试） |
| `revenue.md` / `expenses.md` | 收入/费用实质性程序 |
| `adjustments.md` | 审计调整分录：重分类/账项/跨年滚调 |
| `report.md` | 审计报告（CAS 1501/1502/1503），四种意见类型 |

## 安装

```bash
# 方式一：skills CLI
npx skills add billLiao/china-finance-skills

# 方式二：手动复制（Claude Code 为例）
git clone https://github.com/billLiao/china-finance-skills.git
cp -r china-finance-skills/accounting china-finance-skills/audit ~/.claude/skills/
```

| 平台 | 技能目录 |
|------|---------|
| Claude Code | `~/.claude/skills/` |
| Cursor | `.cursor/skills/` |
| OpenCode | `.opencode/skills/` |
| Trae (中国版) | `.trae-cn/skills/` |

## 使用示例

```
帮我给一家年收入 300 万的小规模纳税人企业建账
→ 触发 accounting：先判断准则（小企业会计准则），再输出 66 科目建账方案

12 月底了，带我走一遍月末结账
→ 触发 accounting：按准则版本输出 5 步/7 步结账清单，含计提、结转分录

应收账款余额 380 万，帮我做账龄分析和坏账评估
→ 触发 audit：输出账龄分析表，标记长账龄客户和坏账风险

上次新增的「1122-01 应收账款—A客户」这个科目，这个月继续用
→ accounting：从公司记忆直接命中，无需重新确认
```

## 记忆机制：越用越懂你的公司

准则只有一套，公司各有各的科目体系和会计习惯。两个技能都内置公司级记忆：references 没覆盖的科目、政策、审计发现，经你确认后写入本地公司档案，下次自动沿用——技能是通用的，记忆是专属的。

```
~/.china-finance-skills/companies/{公司名}/
├── profile.md            # 公司档案（accounting / audit 共用）
├── custom-accounts.md    # 科目扩展（accounting）
├── policies.md           # 会计政策与习惯（accounting）
└── findings.md           # 历史审计发现（audit）
```

- **写入有门槛**：agent 的推断一律标注「请确认」，确认后才入记忆，防止记忆污染
- **优先级**：公司记忆 > references 通用规则 > agent 推断
- **隐私**：记忆只存本地（路径可用环境变量 `CHINA_FINANCE_MEMORY_PATH` 自定义），绝不写入 git 仓库
- **生态飞轮**：整理记忆时发现行业通用知识，欢迎提 PR 补充进 references/

机制细节见 [accounting/SKILL.md](accounting/SKILL.md) 与 [audit/SKILL.md](audit/SKILL.md) 的「记忆机制」一节。

## 与原版的关系

本仓库是对 [kylin985ti/china-accounting-skills](https://github.com/kylin985ti/china-accounting-skills)（25 个独立技能）的**结构重构**：全部财务知识内容保留，组织方式从"25 个平铺技能"改为"2 个入口技能 + 23 份按需加载的参考文档"，并统一了跨文档引用路径。内容层面的改动仅为合并与引用修正，未增删财务知识点。

## 免责声明

本技能包提供的信息仅供参考和学习用途。所有财务处理、税务申报和审计意见应由持证会计师（CPA）或税务师审核确认。因使用本技能包信息产生的任何后果由使用者自行承担。

## 致谢

本项目的全部财务与审计知识内容来自 [kylin985ti/china-accounting-skills](https://github.com/kylin985ti/china-accounting-skills)（MIT License，Copyright (c) 2026 kylin985ti），感谢原作者的整理工作。本仓库仅对其技能组织结构进行重构，原始内容版权归原作者所有。

## License

[MIT](LICENSE) — 包含原始项目与本项目双重版权声明。
