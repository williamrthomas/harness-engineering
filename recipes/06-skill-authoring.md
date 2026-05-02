# 06 — Skill authoring

Build a skill that compounds.

---

## When a skill is right

A skill is the right answer when:

- The knowledge applies *sometimes*, not always.
- The knowledge is *too specific* to live in `AGENTS.md` without bloating it.
- The knowledge is *reusable* across tasks or projects.
- The knowledge is *stable enough* to be worth writing down.

If it applies always → put it in `AGENTS.md`. If it applies once → don't
write it. If it changes weekly → write it short and revisit.

---

## Anatomy

A skill is a directory with a `SKILL.md` and optional bundled files.

```
release/
├── SKILL.md           ← required; metadata + body
├── checklist.md       ← bundled, referenced from SKILL.md
└── scripts/
    └── tag.sh         ← bundled, referenced from checklist.md
```

`SKILL.md` opens with frontmatter:

```yaml
---
name: release
description: |
  Cut a release of this repo. Use when the human says "ship it", "release",
  "tag a version", or asks to deploy after merging. Do NOT use for hotfixes;
  see the hotfix skill.
---
```

The frontmatter is loaded into the system prompt always. Make it specific:
*when* to use, *when not to*. If the description is vague, the agent will
either invoke the skill at the wrong time or fail to invoke it at the right
time.

---

## Three-level discipline

The progressive disclosure is the engineering, not a feature.

- **Level 1 — frontmatter.** Always loaded. Tens of tokens. Just enough for
  the agent to decide.
- **Level 2 — body.** Loaded when the skill is selected. Hundreds of
  tokens. The actual instructions.
- **Level 3 — bundled files.** Loaded only when referenced from the body.
  Thousands of tokens, used surgically.

The win: a library of fifty skills costs only fifty short descriptions in
the system prompt. Most never load. The ones that load, load deeply.

---

## Writing the body

The `SKILL.md` body is a tight playbook:

```markdown
# Release

Cut a release of `foo-service`.

## When to use
- The human says "ship it", "release", "tag X.Y.Z".
- After a feature has merged to main and CI is green.

## When NOT to use
- For hotfixes — see `hotfix/SKILL.md`.
- When CI is red.
- When the changelog is unwritten.

## Steps
1. Verify CI is green on `main`. If not, stop.
2. Run `./scripts/release/changelog.sh` to draft the changelog.
3. Open `CHANGELOG.md`. Verify entries; correct as needed.
4. Bump version in `package.json` (semver).
5. Commit: `git commit -am "Release v$VERSION"`.
6. Tag: `git tag v$VERSION && git push --tags`.
7. CI auto-deploys on tag push. Watch the deploy log.
8. Verify `/healthz` on production returns 200.
9. Post in #releases: "Released v$VERSION. <link to deploy>".

## If something goes wrong
See `rollback.md` (bundled). Do NOT improvise rollbacks.
```

Notice the shape:

- Front-loaded *when to use / when not*.
- Numbered steps the agent can check off.
- Explicit error path that points to a bundled file.
- No general advice. No editorializing. No "best practices."

---

## The art of the description

The frontmatter description is the only part loaded always. It deserves
disproportionate care.

A good description answers three questions in one paragraph:

1. **What is this skill for?** ("Cut a release of this repo.")
2. **When should the agent use it?** ("When the human says ship/release/tag.")
3. **When should the agent NOT use it?** ("Not for hotfixes; see hotfix.")

If the description does not answer all three, the skill will misfire.

---

## Test the skill with the agent

The skill is a tool by another name. Test it the same way:

1. Hand the agent a task that should invoke the skill.
2. Watch: did it find the skill? Did it use it correctly? Did it skip a
   step? Did it follow the error path?
3. Hand the agent a task that should *not* invoke the skill.
4. Watch: did it correctly leave the skill alone?

Each failure tells you something about the description, the body, or the
bundled files. Iterate.

---

## Versioning and pruning

Skills are code. They drift.

- Review the library quarterly. Anything not invoked in 90 days is a
  candidate for removal.
- When the underlying process changes, the skill changes too. Skills that
  outlive their underlying process become silent landmines.
- Keep the descriptions short enough that the agent's view of the skill
  library remains scannable. A library with 200 skills is rarely better
  than a library with 30.

---

## Sharing

Skills are portable. Once built, share:

- Inside the team: `docs/skills/` in the monorepo.
- Across the company: an internal skills package, versioned.
- Publicly: the open standard supports cross-platform portability ([Anthropic, Dec 2025](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)).

A skill someone else wrote, reviewed, and battle-tested can save a week of
work. Be the team that ships those.
