# agent-skills

A few working-style skills for Claude Code, shared as a courtesy. They follow the
[Agent Skills](https://agentskills.io/specification) format and ship as a Claude Code plugin.

## Install

```
/plugin marketplace add paxel/agent-skills
/plugin install agent-skills@agent-skills
```

Skills are then available as `agent-skills:<name>`.

## Skills

- [blunt-replies](skills/blunt-replies/SKILL.md): every reply opens with `OK` or `NO` and a time stamp; no flattery, no agreement rituals.
- [customer-service](skills/customer-service/SKILL.md): collect complaints without fixing anything until the queue is closed, then grill them one question at a time.
- [refresh](skills/refresh/SKILL.md): reload your standing policy skills ("take your pills") and acknowledge each one.
- [long-walk](skills/long-walk/SKILL.md): after a grilling, carry a whole milestone to committed code without asking, one commit per ticket.

## refresh.list

`refresh` loads the skills listed in `~/.claude/refresh.list`, one per line, in order.
A line may carry arguments after the skill name; empty lines and `#` comments are ignored.

```
# standing policies, loaded in this order
style-plugin:terse-mode full
my-commit-rules
agent-skills:blunt-replies
```

## License

MIT
