<!-- mova:engine -->
# AGENTS.md

> Operational guide for the AI agent running this workspace — canonical for every agent
> (Claude Code, opencode, Codex, Antigravity, or anything that reads markdown).
> `CLAUDE.md` imports this file. Conventions in this repo aren't etiquette; they're enforced
> by `npm test`.

## Which mode are you in?

Check `docs/reference/profile.md`:

- **It exists** → this is a personalized **instance**. Serve the learner: see "How a session
  runs" below and obey `docs/mechanics/`.
- **It does not exist, and the human wants to start learning** → run **setup**: follow
  [playbooks/setup.md](playbooks/setup.md). Interview first, generate second, verify third.
- **It does not exist, and the human is working on the template itself** → **maintainer
  mode**: [upstream/map.md](upstream/map.md) is the porting contract,
  [playbooks/sync-upstream.md](playbooks/sync-upstream.md) pulls limba's changes,
  [CHANGELOG.md](CHANGELOG.md) records every released change with its `instance-impact:`.

## The one principle that governs everything

**The goal contract is the spec.** `docs/reference/goal.md` (generated at setup) names what
"done" means for this learner — an exam's sections, a level's can-do list, a scenario list,
or a ledger target — and one marked **spec sentence** is the tie-breaker for every scope
question. Work that serves no part of the goal contract is scope creep, however enriching.
An instance without a goal contract is mis-generated; stop and re-run that setup step.

## Verb table — what the human says, what you run

| The human says… | You follow… |
| --- | --- |
| "set up my workspace" | [playbooks/setup.md](playbooks/setup.md) |
| "lesson" (or shows up ready to study) | [playbooks/lesson.md](playbooks/lesson.md) |
| "drill" (short practice, review only) | [playbooks/drill.md](playbooks/drill.md) |
| "write" / "writing practice" | [playbooks/write.md](playbooks/write.md) |
| "mock" / "test me" | [playbooks/mock.md](playbooks/mock.md) |
| "review" / "weekly review" / "replan" | [playbooks/review.md](playbooks/review.md) |
| "add this word" / "what does X mean" (and wants it kept) | [playbooks/vocab.md](playbooks/vocab.md) |
| "retro" / "what did we learn" | [playbooks/retro.md](playbooks/retro.md) |
| "report this" / "send feedback" / "this is wrong" (about the workspace, not the language) | [playbooks/feedback.md](playbooks/feedback.md) |
| "prep my tutor session" | [playbooks/tutor-prep.md](playbooks/tutor-prep.md) |
| "update the workspace" / "anything new?" / "check for updates" | [playbooks/update.md](playbooks/update.md) |

Per-agent command shims (e.g. `.claude/skills/`) are generated at setup and contain only
pointers to these playbooks. This table is the fallback that makes any agent work without
them. Playbooks are **verbs** and stay thin; shared rules live once, in `docs/mechanics/`
(**nouns**).

## Document map

| File | Read it when… |
| --- | --- |
| `docs/reference/profile.md` | **Every session, at orient** — who the learner is, their languages and ranking, the config block every script reads. |
| `docs/reference/goal.md` | Any scope question — the spec sentence, the assessment instrument, deadline math (or the no-deadline rule). |
| `docs/plan.md` | Current phase and routing for open work. Check before any session. |
| `docs/curriculum.md` | The current unit's targets (first `status: pending`). |
| `docs/mechanics/` | The rules in force: SRS, session format, teaching beats, media, verification. Playbooks obey these. |
| `docs/mechanics/why/` | **Only a retro reads this.** Why each rule exists — the incidents and measurements behind it. A study session never opens it. |
| `docs/mechanics/teaching.md` | **You're about to teach anything.** |
| `docs/mechanics/narration.md` | **You're about to say anything to the learner** — what to price, what to announce, what never to show them. Setup obeys it too. |
| `docs/reference/topics.md` | Coverage map — the arbiter for repair-vs-new-material. |
| `docs/reference/transfer.md` | You're explaining anything — contrast hooks and false friends for this learner's language pair. |
| `docs/visual/SPEC.md` | **You're about to build any page** — tokens, components; begin with `node scripts/newvisual.mjs <slug>`. |
| `docs/logs/` | Append-only session + error logs (`SES-NNN`, `ERR-NNN`). |
| `docs/snapshots/` | Frozen assessments, each with its "Not exercised:" list. |
| `state/` | The SRS ledgers — the workspace's memory. Edit only per `docs/mechanics/srs.md`. |
| `work/` | Produced artifacts: visuals, scored sets, writing, tutor packs, retro intake, sent defect reports. |
| `packs/` | Language packs — the target language's grammar tables, normalization, dictionary adapter, voices. |

## Hard invariants (do not violate)

1. **The goal contract is the spec.** The tie-breaker for any scope question.
2. **State lives only in `state/` ledgers and `docs/logs/`.** Every session ends with the
   close-out ritual (`docs/mechanics/session_format.md`). A session that isn't logged didn't
   happen.
3. **Logs are append-only, newest-first, IDs strictly increasing.** Never edit or renumber an
   old entry; append a correcting one. Derive the next ID in the same turn as the append —
   use `scripts/log-append.mjs`.
4. **Docs and state stay under CI.** `npm test` after edits to plan, curriculum, topics,
   projects, or `state/`. The agent runs all tooling; the human never has to.
5. **No copyrighted content in the repo.** Cite by unit/page; files live untracked in
   `materials/`.
6. **Every language fact is verified, tutor-confirmed, or visibly marked unverified** —
   and every correction names the interference when there is one
   (`docs/mechanics/verification.md`).

## Session hygiene & git

One learner, sequential sessions: work directly on `main`, commit at every close-out
(`SES-NNN: <one-line summary>`), **naming the paths the session wrote** — never `git add -A`.
`playbooks/retro.md` is the one command safe to run in parallel; its concurrency rules bind
any session sharing the tree. Never discard uncommitted work you did not create: a dirty
tree you didn't dirty is a sibling's retro in flight. In a Claude Code instance,
`.claude/settings.json` makes the agent ask before `git add -A`, `stash`, `reset`, and similar.

## Untested paths

Some paths have never run: drills, reviews, tutor packs, adapters other than Claude Code, and
the update, sync and feedback paths ([README.md](README.md) → Status has the detail). Treat a
failure there as untested, not broken, and report engine-shaped findings through
[playbooks/feedback.md](playbooks/feedback.md), not into a local file nobody upstream reads.
