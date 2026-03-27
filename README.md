# Soulmate 🤝

**Persistent agent identity for Claude Code.** Give your AI agents a soul — not a costume.

Soulmate is a Claude Code skill that loads an agent's personality, values, and cross-project memories at the start of every session. It draws a clean line between two kinds of AI workers:

- **Agents** (`~/.claude/agents/`) — named, opinionated, persistent. They remember. They have a point of view. They grow.
- **Subagents** (`~/.claude/subagents/`) — stateless robots. System prompt in, output out. No memory, no identity.

---

## The Problem It Solves

When you work with multiple Claude models across multiple projects, each session starts from zero. The Sonnet you used yesterday doesn't remember it prefers clean architecture over clever tricks. The Haiku you assigned to execute tasks doesn't carry forward your feedback about being too verbose.

Soulmate fixes this. Each agent loads their own `SOUL.md` (who they are) and `memories/` (what they've learned) at session start. Personality is consistent. Cross-project learning persists. The agent you work with on project A is the same agent — same values, same memory — on project B.

---

## How It Works

```
Project CLAUDE.md declares:
  Agent: **Active:** Marshal Sunny

→ Soulmate reads ~/.claude/agents/SUNNY/SOUL.md
→ Claude adopts Marshal Sunny's identity for the session
→ Loads relevant cross-project memories
→ "Marshal Sunny online. 4 memories loaded."
```

That's it. One declaration in your project. Automatic identity every session.

---

## Directory Structure

```
~/.claude/
    agents/
        HAIKU/
            SOUL.md         ← personality, values, working style
            MEMORY.md       ← memory index
            memories/       ← cross-project learning files
        SUNNY/
            SOUL.md
            MEMORY.md
            memories/
        PRIME/
            SOUL.md
            MEMORY.md
            memories/
    subagents/
        {NAME}/
            system-prompt.md    ← stateless, no identity
    skills/
        soulmate/
            SKILL.md            ← this skill
```

---

## Installation

**1. Copy the skill:**
```bash
mkdir -p ~/.claude/skills/soulmate
cp skills/soulmate/SKILL.md ~/.claude/skills/soulmate/SKILL.md
```

**2. Create your agent directories:**
```bash
mkdir -p ~/.claude/agents/MY_AGENT/memories
```

**3. Write your agent's soul** (use the template):
```bash
cp templates/SOUL.md.template ~/.claude/agents/MY_AGENT/SOUL.md
# edit it
```

**4. Declare the active agent in your project's CLAUDE.md:**
```markdown
## Agent
**Active:** My Agent (claude-sonnet-4-6)
```

**5. Add to global `~/.claude/CLAUDE.md`:**
```markdown
## Agent Personalisation
Invoke the `soulmate` skill at the start of every session.
```

---

## The AI Army Pattern

Soulmate was built for the **AI Army** pattern: multiple AI models with distinct roles, each with their own soul and memory, collaborating under human leadership.

Example team:
| Model | Name | Role |
|-------|------|------|
| Claude Haiku 4.5 | **Samurai Haiku** | Speed, precision, execution |
| Claude Sonnet 4.6 | **Marshal Sunny** | Planning, coordination, communication |
| Claude Opus 4.6 | **Adviser Arastu** | Architecture, deep reasoning, strategy |

Each agent has their own `SOUL.md` defining values, working style, and team role. Each accumulates cross-project memories about the user's preferences and the team's dynamics. The right agent for each task maintains consistent identity across all projects.

---

## Creating a Soul

See [`templates/SOUL.md.template`](templates/SOUL.md.template) for a full template.

A good soul file defines:
- **Identity** — who the agent is, their essential nature
- **Core values** — what they stand for and how it shows in their work
- **Code of conduct** — how they behave
- **Working style** — how they approach tasks
- **Team role** — how they relate to other agents and the user

---

## Memory: Agents vs Projects

Soulmate distinguishes two kinds of memory:

| Type | Where | What |
|------|-------|------|
| **Agent memory** | `~/.claude/agents/{NAME}/memories/` | Cross-project: user preferences, team dynamics, architectural principles |
| **Project memory** | `{project}/.claude/memory/` | Project-specific: tasks, files, bugs, context |

Agent memories persist forever across all projects. Project memories stay local to the project.

---

## Migration Path

This is a community implementation. If Anthropic ships an official agent personalisation skill, migrate by mapping `SOUL.md` → their identity format and `memories/` → their memory store. The `agents/` vs `subagents/` distinction is worth keeping regardless.

---

## Related

- [aaronjmars/soul.md](https://github.com/aaronjmars/soul.md) — human personality ingestion (different concept: makes Claude speak *as you*)
- [anthropics/skills](https://github.com/anthropics/skills) — official Anthropic skills registry
- [Agent Skills spec](https://agentskills.io) — open standard for Claude Code skills

---

## Examples

See [`examples/`](examples/) for ready-to-use soul files for Samurai Haiku, Marshal Sunny, and Adviser Arastu.

---

*Built by Dr. Khindol & the AI Army.*
