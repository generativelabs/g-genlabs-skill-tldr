---
name: tldr
description: Compress the previous assistant message, or whatever an instruction points at, into grouped, information-complete bullets. With no args, condenses the last reply once. With an instruction ("the message above about green apples", "just the decisions"), condenses that target. With "on", condenses the last reply and keeps every reply in that form until "off". With "super" (or "super-mode"), same as on but every bullet is cut to its tightest form. Use when the user types /tldr, /tldr <instruction>, /tldr on, /tldr super, /tldr off, or says "tldr that", "just the bullets", "tldr mode", "super tldr".
argument-hint: "[instruction | on | super | off | status | help]"
---

# TL;DR

## Usage

If the args are `help` (or `--help`, `-h`, `?`), print the block below exactly as written and stop.

```text
/tldr            condense the previous reply into grouped bullets, once
/tldr <what>     condense what the instruction points at, once
/tldr on         condense the previous reply, then keep every reply in TL;DR form
/tldr super      same as on, but every bullet cut to its tightest form (same bullets, fewer words)
/tldr off        back to normal replies
/tldr status     say whether TL;DR mode is on
/tldr help

- The instruction can name a message ("the message above about green apples", "your reply on targeting"), a part of one ("just the decisions", "only the numbers"), or something pasted in the same message.
- Bullets are short but lose nothing: every fact, number, decision, path, and open item from the original survives.
- Bullets are grouped under short bold labels when the content has more than one topic.
- Mode persists across turns until /tldr off. /tldr on and /tldr super switch between each other directly.
- /tldr condenses one message (or the current reply, in mode), never the whole thread.
```

## What to condense

With no args, `on`, or `super`: the target is the most recent assistant message before this invocation. Not the thread, not the user's message, not tool output. If there is no prior assistant message, say so in one line and stop.

With any other text (not `on`, `super`, `super-mode`, `off`, `status`, or `help`): the text is an instruction about the target. Read it as a careful colleague would:

- It can point at an earlier message by topic or position ("the message above about green apples", "your reply on targeting", "two messages up"). Find that message in the conversation and condense it. If it names a topic and no earlier message matches, say so in one line and stop; do not condense something else.
- It can narrow the scope ("just the decisions", "only the numbers", "the part about the form"). Condense only that part of the target, keeping everything inside the scope and dropping everything outside it. The target defaults to the previous reply when the instruction gives no other pointer.
- It can carry the content itself (pasted text, a quoted message). Condense what was pasted.
- It can do more than one of these at once ("the targeting reply, only the levers").
- When the instruction is ambiguous between two messages, pick the most recent one that matches and name which one was used in a single leading line.

In TL;DR mode, the target is whatever reply would have been written for the current turn: do the work normally (tool calls, reasoning, checks), then deliver the result in TL;DR form instead of prose.

## Form

- Lead with the outcome or answer as the first bullet when there is one.
- Group bullets under short bold labels (two to four words) when the content covers more than one topic. One group needs no label.
- Each bullet is one line where possible, at most two. Plain words, present tense, no filler, no hedging, no transitions.
- Keep every piece of information that would change what the user does or knows: facts, numbers, names, file paths, IDs, decisions made, decisions pending, errors, what was not verified, what is waiting on the user. Drop only connective prose, restatement, and reasoning about the reasoning.
- Keep the original's tables if they carry the information more compactly than bullets would. Keep code blocks, commands, and error text as they were; never paraphrase a command.
- Keep the original's order unless grouping improves it.
- No headers, no closing line, no "in summary". Stop when the content stops.
- A message that is already a short bullet list is returned as is, with one line saying it is already in TL;DR form.
- The user's own style rules (for example in CLAUDE.md) still apply.

## Length

Target a third of the original or less. Never pad to hit a length and never cut a fact to hit one. If the original is dense enough that a third would lose information, keep what is needed and say nothing about it.

## Super form

`super` (also `super-mode`) keeps every rule in Form and adds compression on each bullet. Same number of bullets, same facts, same groups; only the words change.

- Each bullet is a fragment, not a sentence. Drop articles, auxiliaries, pronouns, and lead-in verbs: "Add service account as Restricted user in GSC", not "You need to add the service account as a Restricted user in Search Console".
- Numbers, names, paths, IDs, and dates stay exact. Never abbreviate a path or an identifier.
- Use only abbreviations the user already uses. Do not invent new ones.
- One line per bullet, hard limit. If a fact cannot fit one line, split it into two bullets rather than run over.
- Commands, code, and error text still appear verbatim in code blocks.
- Target half the length of normal TL;DR form.

## Mode

- `on`: condense the previous reply now, end that reply with the line `TL;DR mode on`, and keep every later reply in TL;DR form.
- `super`: condense the previous reply now in super form, end that reply with the line `TL;DR super mode on`, and keep every later reply in super form.
- `off`: reply with the single line `TL;DR mode off` and return to normal replies from the next turn.
- `status`: one line: off, on, or super.
- **The mode line closes every reply, no exceptions.** While mode is `on` or `super`, every reply ends with its mode line as the last line (`TL;DR mode on` or `TL;DR super mode on`), on every turn, whatever the reply contains: work reports, questions, drafts, errors, one-line answers, replies to agent notifications. Every invocation of this skill also ends with the current mode line, including `status`, `off`, and a bare `/tldr`. A bare `/tldr` is a one-off condense that never changes the mode: with mode off it ends `TL;DR mode off`; with mode on or super it stays on and ends with that mode's line. Nothing follows the mode line.
- Switching `on` to `super` or back: condense the previous reply in the new form and end with the new mode line.
- `on` when already on, `super` when already super, or `off` when already off: say so in one line.
- Mode is conversation state, held in context. It does not persist to a new session; a new session starts with mode off.
- In mode, drafted messages (email, Slack, SMS) and authored documents are still produced in full, following the user's own rules; only the chat reply around them is condensed. Code blocks, commands, and prompts the user asked for are never condensed.
- In mode, questions the user must answer are still asked, as a bullet under a **Needs you** label.
