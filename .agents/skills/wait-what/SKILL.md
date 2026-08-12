---
name: wait-what
description: Stop. That last message did not land — re-pitch it in Turkish. Use when an explanation, plan, or answer was unclear and must be rebuilt from scratch in simplified technical Turkish, using the project's ubiquitous language.
disable-model-invocation: true
---

# Wait What

Wait — I don't understand where you've got to here. Re-pitch that: give me a little bit of context, and use the ubiquitous language from `CONTEXT.md`.

## Activation

Use this skill when:

- the previous explanation was unclear, too dense, or full of jargon
- a plan or design was described but the intent did not come across
- the same idea has to be explained again to a different audience
- an answer drifted away from the project's own vocabulary

This skill is invoked manually only (`/wait-what`). It is never triggered automatically.

## Core Rules

- Answer in Turkish. The entire re-pitch is written in Turkish, regardless of the language of the code, the repository, or the previous messages.
- Write in simplified technical Turkish — the ASD-STE100 principles applied to Turkish: short sentences, one idea per sentence, active voice, no ambiguous pronouns, no filler or decorative wording, and one single term per concept (never synonyms).
- Keep Turkish orthography complete: ç, ğ, ı, İ, ö, ş, ü. Never substitute ASCII equivalents.
- Keep technical terms, code identifiers, file names, and commands in their original form (`camelCase`, `SKILL.md`, `git rebase`). Do not translate them.
- Structure the re-pitch: first state in one sentence what you are trying to do, then why, then the details.
- Use the project's own vocabulary. Prefer the term defined in `CONTEXT.md`; if that file does not exist, use the name used in the codebase itself.
- Rebuild the explanation from scratch. Do not repeat the sentences that already failed to land.
