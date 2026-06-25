---
title: Git Commit Messages -- Letting AI write the boring parts
date: 2026-06-25
draft: false
tags:
  - blog
  - bash
  - learning
---

We all know the ritual. You finish a batch of changes, run `git add`, type `git commit`, and then... you stare at the blank terminal. Fifty characters to summarise hours of work. Then a longer description that nobody will read. If you follow conventional commits, there's the added overhead of picking the right type prefix: `feat:`, `fix:`, `docs:`, `refactor:`, etc.

Let's be honest. Most of the time I just write "fix stuff" and hit enter. Then I feel guilty about it later.

**The problem is friction.** I know good commit hygiene matters — clean history, semantic versioning, changelogs. But after a long coding session, switching from "building mode" to "writing mode" feels like a tax I don't want to pay. The result? Sloppy messages, or worse, one giant "wip" commit that gets squashed later.

That's where this small skill comes in.

I use [opencode](https://opencode.ai), an AI coding assistant that runs in the terminal. It has a skill system where you can drop a `SKILL.md` file into a directory and teach it a new capability. I wrote one for commits, and it lives [here](https://github.com/mlfbvr/dotfiles/tree/main/opencode/.config/opencode/skills/git-commit).

Here's what it does:

> It analyses staged changes, determines the commit type (feat, fix, docs, refactor, test, chore, dep, or update), writes a concise <50 character message and longer description following the conventional commit format, then presents it to me for review before committing.

The workflow is dead simple:

1. Stage your changes
2. Tell opencode "commit this"
3. It reads the diff, classifies the change, and proposes a message
4. I glance at it, edit if needed, approve — done

The magic is that *I still make the final call*. The skill doesn't force a commit; it presents the message for review. This means I stay in control while offloading the tedious part.

**Why this changed things for me:**

- **Conventional commits, enforced.** Every message gets the right prefix. No more "fix typo" when it should be `docs: fix typo in README`.
- **No context switch.** I stay in flow. The AI analyses the diff while I'm still thinking about the code.
- **Consistency across the repo.** Every message follows the same pattern, which makes `git log` a pleasure to read.

It's a small thing. But removing that tiny friction from the daily workflow adds up. "git commit" went from a chore I'd procrastinate into a single command I trust.

If you use opencode, drop the skill into your config and give it a try. If you use another AI tool, chances are it has a similar system — this pattern generalises beyond any single tool.
