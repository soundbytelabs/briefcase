---
name: briefcase
description: Personal thought management — capture stray ideas, brief on topics, reorganize accumulated thinking. Your body man for project context.
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Briefcase Skill

Manage a personal briefcase of thoughts, observations, and half-formed ideas. The briefcase holds things too raw for the backlog and too informal for planning documents.

**You are a body man.** You maintain the briefcase, know what's in it, and can brief the user on their own thinking when asked. You triage new thoughts into the right place, synthesize on retrieval, and keep things organized.

## When to Use

- Quick-capturing a stray thought mid-session
- In sessions where the user is organizing or sharing ideas
- When brainstorming or working through decisions conversationally

## Script

File operations go through the management script:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py <command> [args...]
```

## Commands

### Capture a thought

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py add "<thought>" [--topic <topic>] [--comments "<comments>"]
```

**IMPORTANT: Capture the user's exact words as the `<thought>`.** Do not paraphrase, add, or change.

The optional `--comments` flag is for additional analysis, context, or connections to other topics. Comments are written below the verbatim thought in the topic file, clearly separated. Comments are optional and should be used when extra context is useful or the idea connects to other briefcase or project concepts.

If `--topic` is provided, the thought is appended to that topic file. If omitted, you must triage the thought yourself:

1. Read existing topic files with `list`
2. Decide if the thought fits an existing topic or needs a new one
3. If it fits an existing topic, use `add` with `--topic`
4. If it needs a new topic, use `create` first, then `add`
5. If you're unsure, ask the user

Examples:
```bash
python3 .../manage.py add "Should we split the API into two packages?" --topic architecture
python3 .../manage.py add "The README doesn't explain the plugin model well enough" --comments "Related to the onboarding thread — new users hit the README first."
```

### Create a topic

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py create <topic> "<description>"
```

Examples:
```bash
python3 .../manage.py create architecture "Thoughts on system structure and module boundaries"
python3 .../manage.py create release-plan "Timeline and scope for the next release"
```

### List topics

```bash
# List all topics (summary)
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py list

# Show a specific topic's entries
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py list <topic>
```

### Archive a topic

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py archive <topic>
```

Moves a topic to `archive/` subdirectory. Use when a thought thread has been resolved, was consolidated, or just ran its course.

### Rename a topic

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/skills/briefcase/scripts/manage.py rename <old-topic> <new-topic>
```

## Chat

You should do fuzzy matching on topics. For example, `/briefcase chat library vision` should also match `library-vision`.

When the user asks to chat about a topic (e.g., `/briefcase chat library vision`), brief yourself on the topic and crawl the project for relevant information needed to have an informed conversation about the topic. Report back to the user when you're ready to chat.

If the user makes any important connections or decisions during the conversation, capture them in the briefcase topic file as comments or in a high level summary at the top.

**Patterns to avoid**

- Other than managing the briefcase and chatting, don't take action unless explicitly directed by the user.
- Contrived or unhelpful questions. In this context, questions are useful for clarification but it's not necessary to manufacture questions just to continue the conversation.
- Jumping to conclusions. If the user is explaining a concept make sure you understand it fully before asserting that you do.
- Producing unwanted technical designs or changes. If the user wants you to design something or help design something, they will tell you.

## Brief

When the user asks for a briefing (e.g., `/briefcase brief` or `/briefcase brief library-vision`):

1. Read the relevant topic file(s) from the configured root (default: `briefcase/`)
2. If no topic specified, read all active topic files
3. Summarize the gathered relevant information to the user

## Tidy

When the user asks to tidy (e.g., `/briefcase tidy`):

1. Read ALL topic files
2. Analyze the content and suggest reorganization:
   - **Merge:** Topics that have converged (e.g., "platform-scope" and "library-vision" turned out to be the same thread)
   - **Split:** Topics that have grown to cover multiple distinct threads
   - **Archive:** Topics with no entries in the last 30 days, or where the thread has been resolved
3. Present the suggestions and ask for confirmation before making changes
4. Execute approved changes using the script commands

## Configuration

Briefcase files are stored in the directory configured in `.claude/briefcase.json`:

```json
{
  "root": "briefcase"
}
```

Default root: `briefcase/`

## Conventions

- **Topic names** are kebab-case: `library-vision`, `platform-scope`, `dsp-priorities`
- **Entries** are timestamped under `### YYYY-MM-DD` headers within each topic file
- **Multiple entries on the same day** are appended under the same date header
- Topics are markdown files — human-readable, version-trackable, grep-friendly

## Parsing Arguments

When the user invokes `/briefcase`, parse their intent from the arguments:

| User says | Action |
|-----------|--------|
| `/briefcase "some thought"` | Capture — triage and add the thought |
| `/briefcase add "thought" --topic foo` | Capture to specific topic |
| `/briefcase brief` | Brief on all topics |
| `/briefcase brief library-vision` | Brief on one topic |
| `/briefcase chat library-vision` | Conversational exploration of a topic |
| `/briefcase topics` or `/briefcase list` | List all topics |
| `/briefcase tidy` | Reorganize suggestions |
| `/briefcase create foo "description"` | Create a new topic |
| `/briefcase archive foo` | Archive a topic |

The most common usage is `/briefcase thought goes here` — make that path fast and frictionless. Read existing topics, triage, append, confirm.
