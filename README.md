# agent-skills

A few working-style skills for Claude Code, shared as a courtesy. They follow the
[Agent Skills](https://agentskills.io/specification) format and ship as a Claude Code plugin.

## Install

```
/plugin marketplace add paxel/agent-skills
/plugin install agent-skills@paxel
```

Skills are then available as `agent-skills:<name>`.

## Skills

- [blunt-replies](skills/blunt-replies/SKILL.md): every reply opens with `OK` or `NO` and a time stamp; no flattery, no agreement rituals.
- [customer-service](skills/customer-service/SKILL.md): collect complaints without fixing anything until the queue is closed, then grill them one question at a time.
- [refresh](skills/refresh/SKILL.md): reload your standing policy skills ("take your pills") and acknowledge each one.
- [long-walk](skills/long-walk/SKILL.md): after a grilling, carry a whole milestone to committed code without asking, one commit per ticket.

## Works best with Matt Pocock's skills

`customer-service` and `long-walk` are built around a grilling: an interview that settles
every decision before any code is written. That workflow comes from
[Matt Pocock's skills](https://github.com/mattpocock/skills), and especially his grilling
skill. I use them heavily and these skills rely on them. In my words: they are "really
fucking good".

```
/plugin install mattpocock-skills
```

It is in Claude Code's official marketplace, so there is nothing to add first.

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
