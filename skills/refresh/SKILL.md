---
name: refresh
description: Reload every standing policy skill named in ~/.claude/refresh.list into the current session and acknowledge each one, so the user sees the session is properly medicated.
---

# Refresh

## Purpose
One command that re-arms every standing policy after a new session or context compaction. Loads each policy skill via the Skill tool, actually reading its instructions instead of just naming it, then reports an acknowledgment the user can eyeball.

## Trigger
- `/refresh`
- "take your pills", "did you take your pills"
- Session start of a work session, when the user asks to load policies.

## The roster

The skills to load are not in this file. They are listed in `~/.claude/refresh.list`, which belongs to the user:

- One skill per line, in the order they are loaded.
- A line is the skill name as the Skill tool knows it, optionally followed by arguments: `style-plugin:terse-mode full` loads `style-plugin:terse-mode` with args `full`.
- Empty lines and lines starting with `#` are ignored.

Example:

```
# standing policies, loaded in this order
style-plugin:terse-mode full
my-commit-rules
agent-skills:blunt-replies
```

## Procedures

1. Read `~/.claude/refresh.list`. If it is missing or lists no skill, say so in one line, point at the format above, and stop.
2. Invoke the Skill tool for EVERY skill in the list, in its order. Do not skip any; do not substitute from memory. The point is reading the current instructions.
   - A line with arguments passes them as the skill's args.
   - A mode skill that is already active this session at the listed level (for example via a session hook) does not need to be loaded again; still confirm it in the ack.
3. If a listed skill does not exist, note it in the ack instead of failing.
4. After ALL are loaded, output exactly one ack block and nothing else:
   - One line per skill: `💊 <skill-name> — <one-clause summary of its core rule, drawn from what was just read>`
   - Final line: `Pills taken. <N>/<total> policies active.`
   The one-clause summaries are the proof of reading: they must reflect the loaded content, not this file or the list.
5. Take no other action. Do not start work, do not touch files; the next user message decides what happens.

## Guidelines
- The summaries stay under ~10 words each; the whole ack fits on one screen.
- New standing policies get added to `~/.claude/refresh.list`, never to this file. The list is the single roster.
