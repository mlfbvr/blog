# Blog Writing Agent — Implementation

## Goal
Create an opencode skill at `~/.config/opencode/skills/blog-writer/SKILL.md` that produces blog posts indistinguishable from Martin's writing style.

## Steps

- [x] Plan approved by user
- [ ] Create `~/.config/opencode/skills/blog-writer/SKILL.md` — main skill definition
  - [ ] Skill metadata (name, description, trigger conditions)
  - [ ] Author persona section (full style guide distilled from 10 posts)
  - [ ] Iterative workflow instructions (outline → draft, 2-pass)
  - [ ] Front matter generation rules
  - [ ] Tag inference rules
  - [ ] Quality checklist for self-review before presenting
- [ ] Verify the skill is discoverable by opencode
- [ ] Test with a sample topic

## Review

After implementation, the skill should be loadable via `skill` tool and produce posts matching the analyzed voice, structure, and habits.
