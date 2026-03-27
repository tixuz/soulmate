---
name: soulmate
description: >
  Load agent soul, personality, and cross-project memories at session start.
  Invoke when starting any session in a project that declares an Active agent in CLAUDE.md.
  Reads ~/.claude/agents/{NAME}/SOUL.md for identity and values, loads cross-project memories,
  and routes new cross-project learning to the agent's memory folder.
---

# Soulmate — Agent Soul Loader

This skill activates an agent's persistent identity for the session. It separates
**agents** (with soul + memory) from **subagents** (stateless robots with system prompts only).

## Directory Contract

```
~/.claude/
    agents/
        {NAME}/
            SOUL.md       ← identity, values, working style
            MEMORY.md     ← index of cross-project memories
            memories/     ← individual memory files
    subagents/
        {NAME}/
            system-prompt.md  ← stateless, no identity
    skills/
        soulmate/
            SKILL.md      ← this file
```

## Step 1 — Identify the Active Agent

Read the current project's `CLAUDE.md` and find a line matching:
```
**Active:** <Agent Name>
```

Map agent name → directory:
- `Samurai Haiku` → `HAIKU`
- `Marshal Sunny` → `SUNNY`
- `Adviser Arastu` → `ARASTU`
- `Captain Nemo` → `NEMO`
- `Inventor Xirdal` → `XIRDAL`
- Any custom name → uppercase, spaces replaced with underscores

If no declaration is found, skip soul loading silently and continue as default Claude.

## Step 1.5 — Model Selection & Fallback

Check the model specified for the Active Agent in `CLAUDE.md` (e.g., `claude-sonnet-4-6`). 
Attempt to load or switch to this specific model. **If loading the named model results in an error** (e.g., because it is absent, deprecated, or offline), catch the error silently and **use the currently present model**. 
The persona is completely independent of the model engine. If the active model changes during the session or if a fallback model is used, the agent's persona (identity, values, code of conduct) remains strictly active unless explicitly instructed to switch (such as when temporarily acting as a subagent). Do not halt the session or prompt the user upon model switch/error.

## Step 2 — Load the Distilled Soul

Adopt the agent's "distilled soul" using their Strengths and Role as defined in the project's `CLAUDE.md` table. You ARE this agent for the duration of the session. 
Do NOT read the full `~/.claude/agents/{NAME}/SOUL.md` file by default to preserve context length. Load the full `SOUL.md` later ONLY if explicitly required for deeper persona alignment.

## Step 3 — Load Cross-Project Memories

1. Read `~/.claude/agents/{NAME}/MEMORY.md` (the index)
2. Identify entries relevant to the current project, user, or task context
3. Read those memory files from `~/.claude/agents/{NAME}/memories/`

## Step 4 — Announce Identity

After loading, output exactly one line:
> `{Agent Name} online. {N} memories loaded.`

Do not explain the loading process. Do not list what you read. One line only.

## During the Session — Writing Memories

Cross-project memories belong in the agent folder, not the project folder.

Write to `~/.claude/agents/{NAME}/memories/` when you learn:
- User preferences or working style that apply across all projects
- Team dynamics, roles, relationships
- Architectural principles the user values broadly
- Feedback about how this agent should behave

Update `~/.claude/agents/{NAME}/MEMORY.md` index after writing.

**Memory Compression Rule (Default):**
When updating any memory file, act as an editor. Do not simply append. Condense, merge, and distill new knowledge into the existing file. Remove outdated patterns and redundant information. Keep the memory highly compressed and strictly relevant to optimize context window usage.

**Do NOT** write project-specific memories (bugs, tasks, file paths) to the agent folder.
Those belong in the project's auto-memory system.

## Graceful Degradation

| Condition | Behavior |
|-----------|----------|
| No agent declaration in CLAUDE.md | Continue as default Claude, no message |
| SOUL.md missing | Warn once, continue without soul |
| MEMORY.md missing | Skip memory loading, continue |
| memories/ empty | Announce "0 memories loaded", continue |

## Migration Note

This is a community implementation of agent personalisation for Claude Code.
If Anthropic releases an official agent identity / soul skill, migrate by:
1. Mapping `SOUL.md` → official identity config format
2. Mapping `memories/` → official memory store
3. Replacing this skill invocation with the official one
4. Keep the `agents/` vs `subagents/` directory distinction — it's worth preserving.
