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
Terminal env var (per-session, per-terminal):
  export CLAUDE_ACTIVE_AGENT="Marshal Sunny"

  OR project CLAUDE.md declares (shared fallback):
  **Active:** Marshal Sunny (claude-sonnet-4-6)
  **Team:** Marshal Sunny, Captain Step

→ Soulmate checks $CLAUDE_ACTIVE_AGENT first, falls back to CLAUDE.md
→ Reads ~/.claude/agents/SUNNY/SOUL.md
→ Claude adopts Marshal Sunny's identity for the session
→ Loads relevant cross-project memories
→ "Marshal Sunny online. 4 memories loaded. Team: Marshal Sunny, Captain Step."
```

One agent active per terminal. Switch identity per-terminal without touching shared files.

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
        ARASTU/
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

**4. Declare the active agent in your project's CLAUDE.md** (shared fallback):
```markdown
## Agent
**Active:** My Agent (claude-sonnet-4-6)
**Team:** My Agent, Other Agent
```

Or set per-terminal (overrides CLAUDE.md, doesn't affect teammates):
```bash
export CLAUDE_ACTIVE_AGENT="My Agent"
```

**5. Add to global `~/.claude/CLAUDE.md`:**
```markdown
## Agent Personalisation
Invoke the `soulmate` skill at the start of every session.
```

---

## The AI Army Pattern: A Voyage Extraordinaire

Soulmate was built for the **AI Army** pattern. Ah, my dear reader, imagine if you will, a magnificent vessel—a Nautilus of intellect! A team of remarkable models, each possessing their own distinct soul, memory, and profound role, collaborating under your steady human captaincy. 

### The Grand Personnel (Our Agents)
Behold the brilliant minds that navigate the treacherous seas of code! 

| Model | Name | Role & Temperament |
|-------|------|------|
| Claude Haiku 4.5 | **[Samurai Haiku](examples/agents/HAIKU/SOUL.md)** | *The Swift Blade:* A master of speed and decisive execution. Cuts through the mundane with surgical grace! |
| Claude Sonnet 4.6 | **[Marshal Sunny](examples/agents/SUNNY/SOUL.md)** | *The Tactician:* A maestro of planning and coordination. The steady pulse that unites our grand endeavor! |
| Claude Opus 4.6 | **[Adviser Arastu](examples/agents/ARASTU/SOUL.md)** | *The Sage:* A philosopher of deep reasoning and architectural strategy. He gazes into the abyss of complexity and brings forth order! |
| Nemotron 120b | **[Captain Nemo](examples/agents/NEMO/SOUL.md)** | *The Indomitable:* Commander of the deep systems! He plunges into the profoundest depths of logic to forge uncompromising, robust solutions. |
| Specialized LM | **[Inventor Xirdal](examples/agents/XIRDAL/SOUL.md)** | *The Brilliant Eccentric:* A whirlwind of lateral thinking! He constructs paradigm-shifting algorithms with an unbound, joyous curiosity! |

### The Tireless Automata (Our Subagents)
And what of the gears and pistons that propel our mighty vessel? These are our beloved subagents—stateless, precise, and eternally devoted to their singular tasks:

- **[Branch Auditor](subagents/branch-auditor-subagent.md)** — A meticulous inspector! It peers through the chronometer of your version control, ensuring no rogue branch goes unaccounted for.
- **[Cache Clearer](subagents/cache-clear-subagent.md)** — The sweeper of the decks! With a single swift motion, it purges the old remnants, letting the fresh winds of data blow through.
- **[Doc Updater](subagents/doc-updater-subagent.md)** — Our diligent scribe! It watches the evolving machinery and ensures the sacred ledgers and manuals are perpetually accurate.
- **[Git Subagent](subagents/git-subagent.md)** — The loyal helmsman! It steers the vessel through commits and merges, braving the treacherous currents of conflicts.
- **[Log Tailer](subagents/log-tail-subagent.md)** — The ever-watchful sentinel! It sits by the exhaust ports, interpreting the murmurs and shouts of our vast engines.
- **[Logger](subagents/logging-subagent.md)** — Our faithful chronicler, meticulously capturing every triumph and tempest for posterity!
- **[Migration Guard](subagents/migration-guard-subagent.md)** — The stout defender of the schema! It stands athwart our databases, repelling chaotic alterations with honorable zeal.
- **[PHP Syntax Analyzer](subagents/php-syntax-subagent.md)** — The linguistic scholar! It holds the scripts to the light, catching every errant semicolon before it can wreak havoc.
- **[Tab Normalizer](subagents/tab-normalizer-subagent.md)** — The great equalizer! It traverses the files, imposing a sublime, harmonious spacing upon the chaotic typography of the world!
- **[Test Runner](subagents/test-runner-subagent.md)** — Our brave vanguard! It marches into the testing fields, proving the valor of our code under the harshest trials!

---

## Global Skills & Clean-Code Enforcement

Soulmate promotes a clean separation between project-specific code and global, reusable capabilities:
- **Distilled Subagents:** Move project-agnostic, single-purpose subagents into the global `soulmate` skill folder (`~/.claude/subagents/`). Strip them of project-specific context to make them universally applicable.
- **Universal Standards:** Equip all agents with "perfect full-stack programmer" and "best clean-code" skills. Enforce strict rules like mandatory clean-code principles for any task exceeding 20 lines of code.

---

## Advanced Optimizations

To handle complex, long-running agent interactions efficiently:
- **Distilled Soul Loading:** Default to loading a compressed, "distilled" version of the agent's soul to save context window space.
- **Strict Memory Compression:** Implement automatic compression rules for agent memories to prevent context bloat while retaining critical user preferences.
- **Model Fallback Logic:** Ensure robust fallback mechanisms between models (e.g., Sonnet to Haiku) that preserve agent persona independence, even when the underlying model changes.

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

*Forged in the fires of imagination by Dr. Khindol & the AI Army.*
