# Subagent In-Place Edit Contract Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 补齐 Humanizer-zh 在 subagent 原地编辑场景中的约束优先级, 提问降级和非空交付契约.

**Architecture:** 修改集中在 `SKILL.md` 的任务定义, 快速检查清单, 处理流程和输出格式. 现有深层检查, 24 种句子级模式和三条硬约束保持不变. 仓库版本验证通过后, 将同一文件同步到本机安装目录.

**Tech Stack:** Markdown, YAML frontmatter, Ruby 验证脚本, Git

---

### Task 1: 增加执行环境与 Caller 约束

**Files:**
- Modify: `SKILL.md:22-35`

- [ ] **Step 1: 在任务定义中加入执行契约**

在审阅模式和改写模式说明之后加入一节. 先用白话定义 Caller. 该节需要明确:

```markdown
### 执行环境与 Caller 约束

发起当前 Skill 任务的一方, 下文称 Caller. Caller 可能是用户, 也可能是调用本 Skill 的上层 agent.

- Caller 明确提供的术语表, 保留清单, 禁改范围和输出格式高于本 Skill 的默认行为. 本 Skill 只补充 Caller 没有覆盖的部分. 这条规则不能覆盖系统或平台的更高优先级约束.
- 直接面向用户时, 需要作者判断的问题可以提问. Subagent 或批量任务没有直接提问渠道时, 保留原文并标记缺口, 不停下来等待. 缺少哪类信息, 就使用对应标记: `[定义待补]`, `[机制待补]`, `[来源待补]` 或 `[待作者确认]`.
- 缺口标记不授权补造全称, 机制, 来源或作者立场.
```

- [ ] **Step 2: 调整改写模式中的提问说明**

保留直接面向用户时的提问行为. 句末增加对新执行环境章节的引用, 避免与 subagent 规则冲突.

### Task 2: 拆分两种改写交付形式

**Files:**
- Modify: `SKILL.md:522-568`

- [ ] **Step 1: 更新快速检查清单**

增加一项检查:

```markdown
- ✓ **Caller 要求直接改文件?** 完成后返回非空摘要; 没有修改时说明原因
```

- [ ] **Step 2: 更新处理流程**

在任务模式之后判断交付形式. Return-text 返回修订正文. In-place edit 直接修改文件, 最后返回摘要. Subagent 无法提问时使用缺口标记.

- [ ] **Step 3: 更新输出格式**

将改写输出拆成两项:

```markdown
**改写模式, return-text:** Caller 要求返回修订正文时, 提供完整的重写文本. 如有帮助, 再附简短的更改总结.

**改写模式, in-place edit:** Caller 要求直接修改文件时, 完成后必须返回摘要. 摘要至少包含文件名, 修改类别与数量, 行数变化, 以及未完成项或阻塞原因. 没有修改任何文件时, 明确说明原因. 空结果不算完成.
```

### Task 3: 验证并同步安装副本

**Files:**
- Verify: `SKILL.md`
- Sync: `/home/tim/.agents/skills/humanizer-zh/SKILL.md`

- [ ] **Step 1: 验证 frontmatter 与模式数量**

Run:

```bash
ruby -e 't=File.read("SKILL.md"); f=t[/\A---\n(.*?)\n---/m,1] or abort "missing frontmatter"; abort "name changed" unless f.include?("name: humanizer-zh"); %w[Read Write Edit AskUserQuestion].each { |x| abort "missing #{x}" unless f.include?("  - #{x}") }; abort "pattern count changed" unless t.scan(/^### \d+\./).size == 24; puts "frontmatter and 24 patterns ok"'
```

Expected: `frontmatter and 24 patterns ok`

- [ ] **Step 2: 验证三条硬约束未改变**

Run:

```bash
ruby -e 'def s(t); t[/^## 硬约束\n.*?(?=^---$)/m]; end; a=IO.popen(%w[git show HEAD:SKILL.md], &:read); b=File.read("SKILL.md"); abort "hard constraints changed" unless s(a)==s(b); puts "hard constraints unchanged"'
```

Expected: `hard constraints unchanged`

- [ ] **Step 3: 验证新契约完整**

Run:

```bash
ruby -e 't=File.read("SKILL.md"); ["执行环境与 Caller 约束", "[待作者确认]", "改写模式, return-text", "改写模式, in-place edit", "空结果不算完成"].each { |x| abort "missing #{x}" unless t.include?(x) }; puts "new contracts present"'
```

Expected: `new contracts present`

- [ ] **Step 4: 同步安装副本并验证一致**

将修改后的 `SKILL.md` 同步到 `/home/tim/.agents/skills/humanizer-zh/SKILL.md`, 再运行:

```bash
ruby -e 'abort "installed copy differs" unless File.binread("SKILL.md") == File.binread("/home/tim/.agents/skills/humanizer-zh/SKILL.md"); puts "installed copy matches"'
```

Expected: `installed copy matches`

- [ ] **Step 5: 检查 diff 并提交**

Run:

```bash
git status
git diff -- SKILL.md
git log --oneline -10
git add SKILL.md docs/superpowers/plans/2026-07-17-subagent-in-place-edit-contract.md
git commit -m "fix: define subagent edit contract"
git push origin main
```

Expected: commit succeeds, push updates `origin/main`, final worktree is clean.
