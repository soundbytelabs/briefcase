# Briefcase

**Personal thought and notes capture for Claude Code.**
A low-pressure space for thoughts too raw for a backlog and too informal for planning documents. Quickly info dump your precious stream of consciousness ideas and let Claude organize them for you.

## Quick Start

```bash
# Install as a Claude Code plugin
claude plugins add /path/to/briefcase
```

Create `briefcase.json` in your workspace .claude/ directory:

```json
{
  "root": "briefcase"
}
```

`root` is the directory where topic files live, relative to the project root. Defaults to `briefcase/`.

Then use `/briefcase` in any Claude Code session:

```
/briefcase thinking about splitting the audio stack into more layers
/briefcase brief audio stack
/briefcase chat project roadmap
```

## What It Does

| Command | What It Does |
|---------|-------------|
| `/briefcase <thought>` | Capture a thought. Written down verbatim and filed with comments |
| `/briefcase brief [topic]` | Get a synthesized briefing on your recent thinking |
| `/briefcase chat <topic>` | Conversational exploration of a topic with Claude |
| `/briefcase list` | See all active topics |
| `/briefcase tidy` | Reorganize. Merge, split, promote, or archive topics |

## How It Works

Thoughts are stored in markdown files in your configured root directory, organized by topic. When you add a thought, Claude captures it verbatim and files it under a related topic, or creates a new one. Each topic is a kebab-case `.md` file with dated entries.

Think of Claude as your body man.

## License

MIT. See [LICENSE](LICENSE).
