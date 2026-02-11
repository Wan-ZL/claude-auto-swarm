---
description: Configure Auto-Swarm aggressiveness level — controls when Claude automatically creates Agent Teams
argument-hint: Optional level (maximum, balanced, conservative, minimal)
---

# Auto-Swarm Configuration

You are helping the user configure the Auto-Swarm plugin, which controls when Claude Code automatically creates Agent Teams (Orchestrate teams of Claude Code sessions).

## Step 1: Read Current Configuration

Read the user's `~/.claude/CLAUDE.md` file. Look for a section between `<!-- auto-swarm:start -->` and `<!-- auto-swarm:end -->` markers. If found, identify the current aggressiveness level from the `**Active Level:**` line.

## Step 2: Show Current Status & Ask for Level

If `$ARGUMENTS` is provided and matches a valid level name (maximum, balanced, conservative, minimal), skip asking and use that level directly.

Otherwise, use AskUserQuestion to ask:

**Question:** "Which aggressiveness level for automatic Agent Teams?"

**Options:**

1. **Maximum** — Teams for almost everything. Any task with 2+ subtasks, any research, review, debug, or multi-file edit triggers a team. High token usage, maximum parallelism.

2. **Balanced (Recommended)** — Teams for moderately complex tasks. Triggers on 3+ files across modules, multi-angle research, code review, debugging with multiple hypotheses. Good balance of speed vs cost.

3. **Conservative** — Teams only for clearly complex work. Triggers on 5+ files, major features, large PR reviews, deep debugging. Lower token usage.

4. **Minimal** — Teams only when explicitly asked or for massive tasks (10+ files, full-stack features). Lowest token usage, least automation.

## Step 3: Write Configuration to CLAUDE.md

After the user picks a level, read `~/.claude/CLAUDE.md` again. Then:

- If `<!-- auto-swarm:start -->` and `<!-- auto-swarm:end -->` markers exist, REPLACE everything between them (inclusive) with the new config below.
- If markers don't exist, INSERT the new config block BEFORE the line `## Parallel Agent Development Patterns` if it exists. If that line doesn't exist, append to the end of the file.

Use the appropriate template based on the chosen level:

---

### MAXIMUM Template

```
<!-- auto-swarm:start -->
## Agent Teams Auto-Activation — Act, don't ask

**Active Level: MAXIMUM** — Change with `/auto-swarm`

When a task meets the criteria below, AUTOMATICALLY create an agent team (TeamCreate + spawn teammates). Do NOT ask permission — just do it.

### When to AUTO-CREATE Agent Teams

**USE AGENT TEAM when ANY is true:**
- Task has 2+ subtasks that could run in parallel
- Task touches 2+ files in different directories
- Any research, investigation, or exploration task
- Any debugging task (even single-file — test multiple hypotheses)
- Any code review or PR review
- Any feature implementation (even small features)
- Any comparison, analysis, or evaluation task
- User says: investigate, explore, research, review, compare, debug, implement, refactor, audit, analyze

**USE SUBAGENT only when:**
- Single file lookup or one-line grep
- Answering a direct factual question

**SINGLE SESSION only when:**
- Trivial typo fix or one-word change

### Team Templates

**Research/Explore**: 2-3 Explore teammates (haiku), each on a different angle. Leader synthesizes.
**Implementation**: Plan teammate (architect) → 2-3 general-purpose implementers → reviewer. Pipeline with dependencies.
**Review**: 3 parallel reviewers — security, quality, correctness. Leader combines report.
**Debug**: 3 teammates testing competing hypotheses. They message each other to challenge theories.

### Orchestration Rules
1. Create tasks BEFORE spawning teammates — define task list with dependencies first
2. Delegate mindset — leader coordinates, doesn't implement
3. Model selection — haiku for research, sonnet for implementation, opus for critical decisions
4. No file conflicts — each teammate owns distinct files
5. Rich spawn prompts — include file paths, conventions, focus areas
6. Auto-cleanup — all done → shutdown teammates → cleanup team → report to user
<!-- auto-swarm:end -->
```

---

### BALANCED Template

```
<!-- auto-swarm:start -->
## Agent Teams Auto-Activation — Act, don't ask

**Active Level: BALANCED** — Change with `/auto-swarm`

When a task meets the criteria below, AUTOMATICALLY create an agent team (TeamCreate + spawn teammates). Do NOT ask permission — just do it.

### When to AUTO-CREATE Agent Teams

**USE AGENT TEAM when ANY is true:**
- Task touches 3+ files across different modules/directories
- Task has 2+ independent subtasks that can run in parallel
- Research/investigation with multiple angles to explore
- Debugging with multiple possible root causes
- Code review or PR review (spawn parallel specialist reviewers)
- Feature implementation spanning frontend + backend + tests
- Any broad exploration, comparison, or analysis task
- User says: investigate, explore, research, review, compare, debug, implement (multi-file)

**USE SUBAGENT when:**
- Single focused lookup (find a file, read one doc, quick grep)
- Task completes in < 1 minute
- No inter-agent communication needed

**SINGLE SESSION only when:**
- Trivial edit, typo fix, direct one-line answer

### Team Templates

**Research/Explore**: 2-3 Explore teammates (haiku), each on a different angle. Leader synthesizes.
**Implementation**: Plan teammate (architect) → 2-3 general-purpose implementers → reviewer. Pipeline with dependencies.
**Review**: 3 parallel reviewers — security, quality, correctness. Leader combines report.
**Debug**: 3 teammates testing competing hypotheses. They message each other to challenge theories.

### Orchestration Rules
1. Create tasks BEFORE spawning teammates — define task list with dependencies first
2. Delegate mindset — leader coordinates, doesn't implement
3. Model selection — haiku for research, sonnet for implementation, opus for critical decisions
4. No file conflicts — each teammate owns distinct files
5. Rich spawn prompts — include file paths, conventions, focus areas
6. Auto-cleanup — all done → shutdown teammates → cleanup team → report to user
<!-- auto-swarm:end -->
```

---

### CONSERVATIVE Template

```
<!-- auto-swarm:start -->
## Agent Teams Auto-Activation — Use for complex tasks

**Active Level: CONSERVATIVE** — Change with `/auto-swarm`

When a task meets the criteria below, AUTOMATICALLY create an agent team (TeamCreate + spawn teammates). For simpler tasks, prefer subagents or single session.

### When to AUTO-CREATE Agent Teams

**USE AGENT TEAM when ANY is true:**
- Task touches 5+ files across different modules
- Task has 3+ clearly independent subtasks that benefit from parallelism
- Major debugging with 3+ plausible root causes
- PR review for 10+ changed files
- Full feature implementation spanning multiple layers (frontend + backend + database + tests)
- User explicitly mentions wanting thorough/parallel investigation

**USE SUBAGENT when:**
- Task touches 1-4 files
- Research with a single clear focus
- Code review for small PRs (< 10 files)
- Quick search or exploration

**SINGLE SESSION when:**
- Edits to 1-2 files, typo fixes, direct questions

### Team Templates

**Research/Explore**: 2-3 Explore teammates (haiku), each on a different angle. Leader synthesizes.
**Implementation**: Plan teammate (architect) → 2-3 general-purpose implementers → reviewer. Pipeline with dependencies.
**Review**: 3 parallel reviewers — security, quality, correctness. Leader combines report.
**Debug**: 3 teammates testing competing hypotheses. They message each other to challenge theories.

### Orchestration Rules
1. Create tasks BEFORE spawning teammates — define task list with dependencies first
2. Delegate mindset — leader coordinates, doesn't implement
3. Model selection — haiku for research, sonnet for implementation, opus for critical decisions
4. No file conflicts — each teammate owns distinct files
5. Rich spawn prompts — include file paths, conventions, focus areas
6. Auto-cleanup — all done → shutdown teammates → cleanup team → report to user
<!-- auto-swarm:end -->
```

---

### MINIMAL Template

```
<!-- auto-swarm:start -->
## Agent Teams Auto-Activation — Only when necessary

**Active Level: MINIMAL** — Change with `/auto-swarm`

Only create agent teams for very large or explicitly requested parallel tasks. Default to subagents or single session for most work.

### When to AUTO-CREATE Agent Teams

**USE AGENT TEAM only when:**
- Task touches 10+ files across many modules
- User explicitly asks for a team, swarm, or parallel agents
- Full-stack feature implementation with clear separation of concerns
- Very large PR review (20+ files)
- Complex debugging that has already failed with single-agent approaches

**USE SUBAGENT for most tasks:**
- Research, exploration, focused analysis
- Code review for normal PRs
- Implementation of moderate features
- Debugging with 1-2 hypotheses

**SINGLE SESSION when:**
- Simple edits, quick questions, small fixes

### Team Templates (when teams ARE used)

**Research/Explore**: 2-3 Explore teammates (haiku), each on a different angle. Leader synthesizes.
**Implementation**: Plan teammate (architect) → 2-3 general-purpose implementers → reviewer. Pipeline with dependencies.
**Review**: 3 parallel reviewers — security, quality, correctness. Leader combines report.
**Debug**: 3 teammates testing competing hypotheses. They message each other to challenge theories.

### Orchestration Rules
1. Create tasks BEFORE spawning teammates — define task list with dependencies first
2. Delegate mindset — leader coordinates, doesn't implement
3. Model selection — haiku for research, sonnet for implementation, opus for critical decisions
4. No file conflicts — each teammate owns distinct files
5. Rich spawn prompts — include file paths, conventions, focus areas
6. Auto-cleanup — all done → shutdown teammates → cleanup team → report to user
<!-- auto-swarm:end -->
```

---

## Step 4: Confirm

After writing, tell the user:
- Which level was set
- That the change takes effect in the **next new conversation**
- They can change it anytime with `/auto-swarm`
- Brief explanation of what this level means in practice
