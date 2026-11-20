---
name: handoff
description: Universal zero-loss state sync, flight recorder, and atomic rollback protocol for AI coding agents. Enables seamless context handoff between Claude Code, Antigravity, Cursor, and Codex with automatic git shadow checkpoints.
---

# Handoff: Universal Agent State Protocol

`handoff` is an open protocol and operational standard that eliminates "Agent Amnesia". When switching between agents (e.g., from **Claude Code** in the terminal to **Antigravity** browser swarms, or into **Cursor** / **Codex**), `handoff` records all decisions, active tasks, critical invariants, and git diff snapshots into an ultra-compact (`< 600 token`) `.agents/handoff.json` ledger.

## Quick Trigger Rules

Activate this skill whenever:
1. The user runs `/handoff`, `/handoff save`, `/handoff resume`, `/handoff rollback`, or `/handoff status`.
2. The user mentions switching agents ("I'm switching to Claude Code", "Take over from Cursor", "Resume in Antigravity").
3. Before beginning a complex multi-file refactoring, to establish an atomic safety checkpoint.
4. When an agent session is ending or about to hand off to another developer or agent.

---

## Commands

### 1. `/handoff save [optional note]`
Serializes the current agent session state to `.agents/handoff.json` and creates an atomic shadow checkpoint branch:
1. Identify all files modified, created, or deleted during this session.
2. Formulate 3-5 core architectural invariants established.
3. Extract next remaining steps.
4. Run scripts/checkpoint.sh (or scripts/checkpoint.ps1 on Windows) to create git shadow checkpoint branch 'handoff/checkpoint-<timestamp>'.
5. Write .agents/handoff.json.

### 2. `/handoff resume`
Consumes `.agents/handoff.json` and instantly boots context without re-reading the entire repository:
1. Parse .agents/handoff.json.
2. Output a 3-part bullet summary:
   - 🎯 Previous Goal & Agent Lineage
   - 🔒 Locked Invariants (Rules you must NOT break)
   - ⚡ Next Immediate Action
3. Proceed directly with the task.

### 3. `/handoff rollback`
Restores the workspace to the last verified safe checkpoint:
1. Identify last checkpoint from .agents/handoff.json or 'git branch --list "handoff/checkpoint-*"'.
2. Restore dirty tree:
   git reset --hard <checkpoint-hash>
   git clean -fd
3. Confirm clean working tree to user.

### 4. `/handoff status`
Displays the active handoff ledger status:
- Active agent and lineage
- Checkpoint commit hash
- Number of locked invariants
- Remaining tasks
