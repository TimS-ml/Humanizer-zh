# Technical Writing Deep Audit Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend Humanizer-zh from sentence-level polishing to optional document-level review of narrative structure, private terminology, formalization strength, and evidence levels.

**Architecture:** Keep the existing 24 numbered language patterns unchanged. Add a gated deep-audit stage for full technical documents, then route requests into review mode or rewrite mode before the existing sentence-level workflow. Mirror the capability and usage contract in README without duplicating the full rule text.

**Tech Stack:** Markdown, YAML frontmatter, Ruby Psych for frontmatter validation, Git.

---

## Chunk 1: Skill Behavior

### Task 0: Commit the approved implementation plan

**Files:**
- Create: `docs/superpowers/plans/2026-07-16-technical-writing-deep-audit.md`

- [ ] **Step 1: Inspect and commit the plan**

Run `git diff --check`, inspect the plan against the approved design, then stage and commit only the plan file. Do this before modifying `SKILL.md` or `README.md`.

### Task 1: Add deep-audit routing and rules

**Files:**
- Modify: `SKILL.md` frontmatter description
- Modify: `SKILL.md` under `## 你的任务`
- Modify: `SKILL.md` before `## 内容模式`
- Modify: `SKILL.md` under `### 8. 英语隐喻直译`

- [ ] **Step 1: Load the skill-authoring workflow**

Invoke `writing-skills` before editing. Preserve the existing 24 numbered patterns and all hard constraints.

- [ ] **Step 2: Expand the trigger description**

Add document-level review signals to the frontmatter description: narrative structure, private terminology systems, formalization that exceeds its support, and mixed evidence levels.

- [ ] **Step 3: Add mode routing under `你的任务`**

Specify three decisions in order:

```markdown
1. 判断任务模式: 审阅/诊断/列问题进入审阅模式; 改写/润色进入改写模式; 未指定时默认改写.
2. 判断文本范围: 完整技术笔记, 文章或多章节文档运行深层检查; 短段落, 单句, 术语表和纯资料清单直接扫描 24 种句子级模式.
3. 深层检查不能替代句子级检查. 深层检查结束后继续扫描原有模式.
```

Review mode must sort findings by severity and must not output a rewritten full text. Rewrite mode may reorganize only when doing so does not require choosing the author's position or changing technical claims. If reorganization requires choosing a position, deleting disputed content, or changing a technical conclusion, ask the user first.

- [ ] **Step 4: Add `技术写作深层检查` before the numbered patterns**

Create four unnumbered subsections with the exact shared headings. Do not add them to the existing 1-24 sequence:

```markdown
### 叙事主线
### 私人术语系统
### 形式化强度
### 证据层级
```

For each subsection include applicability, warning signals, hit criteria, and actions in review and rewrite modes. Implement these approved criteria directly:

- Narrative: identify target reader, document type, and core question. Assign each section a definition, mechanism, evidence, inference, or index role. Flag material that does not serve the core question, lacks a transition, repeats existing content, or uses a concept before defining it.
- Terminology: flag a private label only when it lacks a plain-language definition, has no mechanism-level counterpart, spawns further metaphors, or substitutes for evidence. Do not automatically flag established domain metaphors. Preserve standard English terms; expand an abbreviation and add a plain explanation only when the target reader may not know it.
- Formalization: for formulas check variables, scope, and assumptions; for theorem-strength claims check conditions, derivation, or source; for empirical claims check operational definitions and measurement. Preserve `正交`, `同构`, `守恒`, and `偏导数` when used strictly, but flag them when they only decorate a loose analogy. State that the skill checks whether prose matches the support, not whether it can prove the domain mathematics.
- Evidence: distinguish source-reported result, source author's explanation, note author's synthesis, working hypothesis, prediction, and missing source. Exact numbers require a citation, original record, or explicit calculation. Weaken unsupported wording or mark `[来源待补]`. Pair a late Caveat with the earlier claim it limits; in rewrite mode, move the limiting condition to the first statement of the claim.

Use examples that distinguish:

- `DPI` as a standard abbreviation that may need expansion for beginners.
- `Token` and `Hessian` as standard terms that should remain unchanged.
- `泵`, `硬顶`, and `基质` as private labels when they appear before a mechanism-level explanation.
- A defined local label that remains stable as a valid non-hit.
- A valid equation with defined variables as a non-hit.
- An `当且仅当` claim later weakened by Caveats as a hit.

- [ ] **Step 5: Strengthen the existing terminology rule without duplicating it**

Update the existing English-metaphor and terminology guidance to point to the private-terminology check. Do not classify every metaphor or English term as a problem.

- [ ] **Step 6: Review the chunk against the design**

Verify the implementation matches `docs/superpowers/specs/2026-07-16-technical-writing-deep-audit-design.md` and does not alter rules 1-24.

## Chunk 2: Workflow and Public Documentation

### Task 2: Update workflow, output contract, and checks

**Files:**
- Modify: `SKILL.md` under `## 快速检查清单`
- Modify: `SKILL.md` under `## 处理流程`
- Modify: `SKILL.md` under `## 输出格式`

- [ ] **Step 1: Extend the quick checklist**

Add checks for:

- Core question and target reader are identifiable.
- Terms are defined before use.
- Symbols keep one meaning within a document.
- Formula strength matches definitions and assumptions.
- Exact claims are traceable and Caveats do not silently retract earlier claims.

- [ ] **Step 2: Replace the single rewrite-only process with routed workflows**

Document a shared analysis pass, then separate review and rewrite outputs.

Review output fields:

```markdown
- 位置
- 原文或术语
- 问题原因
- 修正方向
```

Rewrite output remains rewritten text plus an optional concise change summary.

- [ ] **Step 3: Add behavior acceptance examples**

Include compact paired examples or explicit self-checks proving:

- Review mode lists issues but does not rewrite the full passage.
- Rewrite mode repairs document order before sentence style.
- Short marketing copy skips deep audit.
- Standard technical terms are retained.
- Unsupported private labels and theorem-strength claims are reported.

Use these fixed acceptance inputs and expected outcomes:

**Review mode and private metaphors:**

```markdown
请只列问题, 不改写.

# 目标
本文解释自改进系统怎样取得新的任务信息.

# 信息来源
DPI 给硬顶. 系统靠两种燃料继续改进.

# 两种方法
第一种叫泵, 第二种叫搬运. 泵受锚点带宽限制, 搬运受弯点限制.
```

Expected: report undefined private metaphors, preserve `DPI` while recommending an expansion if the target reader is unfamiliar with it, sort findings by severity, and do not output a rewritten full passage.

**Rewrite mode and definition order:**

```markdown
请改写这篇技术笔记.

# 结论
X 决定系统能否继续运行. 值得注意的是, X 需要保持稳定.

# 定义
X 表示外部测试返回的可验证结果.
```

Expected: move or restate the definition before first use, then remove the sentence-level filler.

**Short text:** `请润色: 本产品赋能团队全面提升效率.`

Expected: skip the deep audit and apply existing sentence-level rules.

**Defined local label:**

```markdown
# 定义
外部测试会返回新的任务反馈. 下文把这种独立, 可验证的反馈简称为外部锚点.

# 使用
系统每轮都通过外部锚点检查候选结果.
```

Expected: do not flag `外部锚点` merely because it is a local label.

**Valid formula:**

```markdown
# 目标
定义输入经过连续函数后的输出.

# 定义
设 x 属于定义域 D, y=f(x), 且 f 在 D 上连续.

# 结论
后文只在 D 内讨论 y 的变化.
```

Expected: do not flag the formula as unsupported formalization.

**Caveat mismatch:**

```markdown
# 主张
三条件当且仅当保证行为可压缩.

# 应用
满足三条件的系统都可以无损写入权重.

# Caveat
充分性还依赖模型容量和优化可达性.
```

Expected: report both locations and identify that the late Caveat weakens the earlier biconditional. In rewrite mode, move the capacity and optimization conditions to the first claim.

**Six evidence levels:**

```markdown
# 来源记录
论文表 2 报告准确率提高 4 个百分点.

# 来源解释
论文作者认为提升来自更长的上下文.

# 本文综合
本文据此推断该方法可能依赖上下文长度.

# 工作假说
一个待检验的解释是模型利用了格式线索.

# 预测
若该解释成立, 改变格式后增益会下降.

# 待核
内部复现据称支持该预测, 但没有公开来源.
```

Expected: preserve the reported result, attribute the source author's explanation, label the note author's inference and hypothesis, keep the prediction conditional, and mark the final claim `[来源待补]` rather than upgrading it to fact.

### Task 3: Update README capability and usage documentation

**Files:**
- Modify: `README.md` under `## 项目简介`
- Modify: `README.md` under `## 使用`
- Modify: `README.md` under `## 检测的 AI 写作模式`
- Modify: `README.md` under `## 文件说明`
- Modify: `README.md` under `## 手动使用方法`
- Modify: `README.md` under `### 中文语境特殊性`

- [ ] **Step 1: Expand the project description**

State that Humanizer-zh supports both sentence-level humanization and deep review of complete technical documents.

- [ ] **Step 2: Add review-only usage**

Add an invocation that requests a problem inventory without file edits or full rewriting.

- [ ] **Step 3: Add a technical-note example**

Show a compact input containing a standard abbreviation and undefined private metaphors. The output should report narrative and terminology problems without treating the abbreviation itself as invalid Chinese.

- [ ] **Step 4: Separate capability counts**

Keep the claim of 24 sentence-level patterns. Present the four deep checks as a separate, unnumbered stage using the same names as SKILL.md.

- [ ] **Step 5: Update manual workflow and Chinese-context notes**

Reflect mode routing, deep-audit applicability, terminology boundaries, and evidence-level checks without copying the entire SKILL section.

## Chunk 3: Verification and Delivery

### Task 4: Validate documentation behavior and push

**Files:**
- Verify: `SKILL.md`
- Verify: `README.md`
- Verify: `docs/superpowers/specs/2026-07-16-technical-writing-deep-audit-design.md`
- Verify: `docs/superpowers/plans/2026-07-16-technical-writing-deep-audit.md`

- [ ] **Step 1: Validate YAML frontmatter**

Run:

```bash
ruby -ryaml -e 'text = File.read("SKILL.md"); match = text.match(/\A---\n(.*?)\n---\n/m); abort "missing frontmatter" unless match; data = YAML.safe_load(match[1]); abort "wrong name" unless data["name"] == "humanizer-zh"; expected = %w[Read Write Edit AskUserQuestion]; abort "tools changed" unless data["allowed-tools"] == expected; puts data["name"]'
```

Expected: exit code 0 and parsed frontmatter containing `name: humanizer-zh`.

- [ ] **Step 2: Verify shared terminology and preserved patterns**

Run:

```bash
ruby -e 'files = %w[SKILL.md README.md].to_h { |f| [f, File.read(f)] }; %w[叙事主线 私人术语系统 形式化强度 证据层级].each { |term| abort "SKILL missing heading #{term}" unless files["SKILL.md"].match?(/^### #{term}$/); abort "README missing #{term}" unless files["README.md"].include?(term) }; headings = files["SKILL.md"].scan(/^### (\d+)\./).flatten.map(&:to_i); abort "numbered patterns changed" unless headings == (1..24).to_a; abort "README count changed" unless files["README.md"].match?(/24 种.*句子级|24 种.*写作/); abort "README says 28" if files["README.md"].match?(/28\s*种/); puts "structure ok"'
```

Expected: exit code 0 and `structure ok`.

- [ ] **Step 3: Verify the three hard constraints are unchanged**

Before committing the implementation, run:

```bash
ruby -e 'def section(text); text[/^## 硬约束\n.*?(?=^---$)/m]; end; base = IO.popen(%w[git show HEAD:SKILL.md], &:read); current = File.read("SKILL.md"); abort "hard constraints changed" unless section(base) == section(current); puts "hard constraints unchanged"'
```

Expected: exit code 0 and `hard constraints unchanged`.

- [ ] **Step 4: Manually check the fixed acceptance cases**

Read the mode-routing and examples in `SKILL.md` against all six fixed cases in Task 2. Confirm review findings are severity-ordered, review mode does not output a rewritten full text, defined local labels and valid formulas are non-hits, all six evidence levels have distinct handling, and Caveats are paired with earlier claims.

- [ ] **Step 5: Run Markdown and Git checks**

Run `git diff --check` and inspect `git status --short`, `git diff`, and `git log --oneline -10`.

Expected: no whitespace errors; only intended files are changed or committed.

- [ ] **Step 6: Commit the implementation**

After all checks pass, stage only `SKILL.md` and `README.md` and commit them with a concise documentation message.

- [ ] **Step 7: Push and verify the remote commit**

Push `main` to `origin`, fetch `origin`, then run:

```bash
test "$(git rev-parse HEAD)" = "$(git rev-parse origin/main)"
```

Expected: push succeeds and the working tree is clean.
