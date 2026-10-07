<!-- mova:engine -->
# agents/claude-code/ — the Claude Code adapter

How setup instantiates mova's verbs for an instance whose profile says
`agent: claude-code`. Claude Code auto-discovers skills in `.claude/skills/` and
auto-invokes them when the conversation matches their `description` — so the adapter is
one generated skill per active verb, each a **pointer and nothing else**.

## What setup generates (into the INSTANCE, never this repo)

For **each active playbook verb** (see "Which verbs are active" below), write
`.claude/skills/mova-<verb>/SKILL.md` from [skill.template.md](skill.template.md):

- Copy the template **from line 2** (line 1 is this repo's `mova:engine` marker — the
  generated shim is instance content and must not carry it, and Claude Code requires the
  YAML frontmatter to start at line 1).
- Fill the slots from the playbook's frontmatter:
  - `{{VERB}}` — the frontmatter `verb:`.
  - `{{SUMMARY}}` — the frontmatter `summary:`, verbatim, single line.
  - `{{TRIGGERS}}` — the frontmatter `triggers:` phrases, joined into the description so
    Claude Code auto-invokes on them. The description is the routing surface; the triggers
    ARE the routing data.
- The body stays exactly the pointer: *Follow `playbooks/<verb>.md`. All rules live there
  and in `docs/mechanics/` — this file deliberately holds none.* Do not "helpfully" inline
  any of the playbook. A rule copied into a shim is a rule free to drift; the whole design
  is that AGENTS.md's verb table works with no shims at all, and shims only add
  auto-invocation.

Also copy [launch.json](launch.json) to the instance's `.claude/launch.json` — the visuals
preview server (`name: "visuals"`, port 8791) that lets Claude Code preview generated pages
from `work/visuals/`.

Also copy [settings.json](settings.json) to the instance's `.claude/settings.json`. If the
instance already has one, merge its `permissions.ask` entries into the existing list. It makes
Claude Code ask before `git add -A`, `git commit -a`, `stash`, `reset`, `restore`,
`checkout --`, and `clean`. AGENTS.md states the rule in prose for every agent; this file is
what holds it for Claude Code, because a shared tree with sibling retros is exactly where a
sweeping `git add` or a `stash` loses someone else's work.

## Which verbs are active

A playbook is active when its frontmatter `scenarios:` is `all` or includes the profile's
focus mode (`focus:` config key, default `full` — see `setup/scenarios/focus_modes.md`;
the frontmatter is the authority), EXCEPT: `maintainer: true` playbooks are never active in an instance, and
`tutor-prep` is active only when `docs/reference/goal.md` has a `## Tuition` section (see
that playbook's activation rule). `docs/agents.test.ts` enforces exactly this: an adapter
file per active verb, each ≤ 20 lines, with no rule text leaked in.

## Regeneration

Shims are generated, never hand-edited: `/update` regenerates them when a playbook's
frontmatter changes (a CHANGELOG `instance-impact:` step names it). To change what a verb
does, change the playbook.
