# tldr

A Claude Code skill that condenses the previous reply, or whatever an instruction points at, into short grouped bullets that keep every fact. It also has a mode that keeps every later reply in that form.

## Install

```bash
git clone https://github.com/generativelabs/g-genlabs-skill-tldr ~/.claude/skills/tldr
```

## Use

```text
/tldr            condense the previous reply, once
/tldr <what>     condense what the instruction points at, once
/tldr on         condense the previous reply, then keep every reply in TL;DR form
/tldr super      same as on, with every bullet cut to its tightest form
/tldr off        back to normal replies
/tldr status     say which mode is on
/tldr help
```

While a mode is on, every reply ends with a line naming it (`TL;DR mode on` or `TL;DR super mode on`).
