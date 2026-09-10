# Porting `ptr-abuse-check` to other agents

Claude Code and Codex are covered by the repository layout itself — the skill
folder is checked in under `.claude/skills/` and `.agents/skills/`, and the
README explains how to link them. This file covers everything else: runtimes
without `SKILL.md` discovery, and the rules a port must not break.

Paths below assume the collection is cloned to `~/claude-codex-skills`.

## Gemini CLI

Gemini CLI has no `SKILL.md` discovery. Point a thin custom command at the
skill folder instead:

`~/.gemini/commands/ptr-abuse-check.toml`

```toml
description = "Analyse a customer PTR request for phishing / brand-impersonation signals (advisory only)."

prompt = """
Read ~/claude-codex-skills/.claude/skills/ptr-abuse-check/SKILL.md and follow it
exactly for the request below. Read watchlist.md and response-templates.md from
the same directory when the instructions refer to them.

Request:
{{args}}
"""
```

Invoke with `/ptr-abuse-check <IP = hostname>`. Project-scoped equivalent:
`<repo>/.gemini/commands/`. Subdirectories become namespaces, so
`commands/ptr/check.toml` is invoked as `/ptr:check`.

For always-on context instead of a command, append a pointer to the skill in
`~/.gemini/GEMINI.md` — but that costs context on every session, which is why
the command form is preferred for a manual-only skill.

## Other agents

Anything that supports "read this file and follow it" can use the skill:

| Agent | Mechanism |
|---|---|
| Cursor | `.cursor/rules/ptr-abuse-check.mdc`, `alwaysApply: false`, body pointing at the folder |
| GitHub Copilot | `.github/instructions/*.instructions.md` referencing the folder |
| AGENTS.md-based tools | a short section in `AGENTS.md` naming the path and when to read it |
| Anything else | pass the contents of `SKILL.md` as a system/developer message |

The rule is the same everywhere: the agent must read `SKILL.md` **before**
analysing a PTR request, and must load `watchlist.md` and
`response-templates.md` only when `SKILL.md` tells it to.

## Behavioural contract (do not change when porting)

Whatever runtime it runs under, the skill must remain:

1. **Advisory only** — it never calls a PTR write tool, never approves and never
   refuses. It reports signals and stops.
2. **Operator-decided** — the final call, and the 4-digit PIN required by the
   write tool, always come from the human.
3. **Template on request only** — the customer reply is produced only when
   explicitly asked for.
4. **Reported in Turkish** — instructions are English for the agent, the output
   is Turkish for the operator.
5. **Manual-only** — the model never invokes it on its own. Every port must set
   the host's equivalent of `disable-model-invocation` /
   `allow_implicit_invocation: false`; a `description` alone is not enough.

If a port drops any of these, it is no longer this skill.
