# Briefcase

**Personal thought management for Claude Code.**
Capture stray ideas, brief on accumulated thinking, and reorganize threads as they evolve. A low-pressure space for thoughts too raw for the backlog and too informal for planning documents.

## Quick Start

```bash
# Install as a Claude Code plugin
claude plugins add ./tools/plugins/briefcase

# Configure (in your project's .claude/briefcase.json)
echo '{ "root": "briefcase" }' > .claude/briefcase.json
```

Then use `/briefcase` in any Claude Code session:

```
/briefcase "Should we narrow platform scope to M7 only?"
/briefcase brief library-vision
/briefcase chat dsp-priorities
```

## What It Does

| Command | What It Does |
|---------|-------------|
| `/briefcase "thought"` | Capture a thought — triaged to the right topic automatically |
| `/briefcase brief [topic]` | Get a synthesized briefing on your recent thinking |
| `/briefcase chat <topic>` | Conversational exploration of a topic with Claude |
| `/briefcase list` | See all active topics |
| `/briefcase tidy` | Reorganize — merge, split, promote, or archive topics |

## How It Works

Thoughts are stored as markdown files in your configured root directory, organized by topic. Each topic is a kebab-case `.md` file with dated entries:

```
briefcase/
├── library-vision.md
├── platform-scope.md
├── dsp-priorities.md
└── archive/
    └── old-topic.md
```

A topic file looks like this:

```markdown
# Library Vision

Thoughts on SBL's direction, identity, and long-term goals

## Entries

### 2026-03-09

Should we drop RP2040 support? Most energy is on M7.

### 2026-03-10

Decided: DSP-capable platforms only. FPU required.

The README doesn't tell the DSP story well enough.
```

No database, no proprietary format. Plain markdown — human-readable, version-trackable, grep-friendly.

## The Body Man

Claude acts as your **body man** for the briefcase. It knows what's in there, triages new thoughts into the right topic, synthesizes across entries on retrieval, and asks probing questions during exploration. Briefings compress — 15 entries become a paragraph or two, not 15 bullet points.

During `/briefcase chat`, Claude connects dots between briefcase thoughts and broader project context. It captures new insights back to the topic file as they emerge. When a thread matures, it suggests promoting to a backlog item or planning document — but doesn't push. The briefcase is a thinking space, not a task tracker.

## Configuration

Create `.claude/briefcase.json` in your project root:

```json
{
  "root": "briefcase"
}
```

`root` is the directory where topic files live, relative to the project root. Defaults to `briefcase/`.

## Management Script

File operations are handled by the Python script at `plugins/briefcase/skills/briefcase/scripts/manage.py`:

```bash
manage.py create <topic> "<description>"
manage.py add "<thought>" [--topic <topic>]
manage.py list [<topic>]
manage.py archive <topic>
manage.py rename <old-topic> <new-topic>
```

## License

MIT. Copyright (c) 2026 Michael Skiles.
