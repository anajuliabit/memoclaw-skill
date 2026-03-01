# MemoClaw Skill — Improvement Backlog

## ~~1. Add quick-reference card at top of SKILL.md~~ ✅
Added after prerequisites. Covers essential commands, importance cheat sheet, memory types with decay, and free commands list.

## ~~2. Replace SDK examples with CLI-focused ones in examples.md~~ ✅
Replaced JS SDK (Example 8) and Python SDK (Example 9) with interactive browse, multi-agent namespace, and automated session summarization examples.

## ~~3. Extract API reference to reduce SKILL.md token cost~~ ✅
Moved full HTTP endpoint docs to `api-reference.md`. SKILL.md reduced from 35KB to 21KB (~40% smaller). Removed duplicate "Example: OpenClaw agent workflow" (covered in examples.md Example 2) and inline "Status check" HTTP details.

## ~~4. Document undocumented store flags~~ ✅
Added `--batch`, `--immutable`, `--pinned`, `--expires-at` to quick reference and CLI usage. Added `--min-similarity` to recall quick reference.

## 5. Verify memory_type values against CLI
CLI help mentions `core, episodic, semantic` as memory type examples, but SKILL.md documents `correction, preference, decision, project, observation, general`. Need to verify which set the API actually accepts and reconcile. (Note: Linear issue creation blocked by workspace limit.)
