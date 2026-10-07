# Changelog

Every entry carries an `instance-impact:` line — what a personalized copy of this template
must do about the change: `none` (template-repo internals), `engine files auto-update`
(the instance `/update` playbook handles it), or a named regeneration step.

## 0.20.0 — 2026-10-07

Claude Code now reads the workspace's operational guide in every session. Before this, an
instance's CLAUDE.md asked the agent in words to read AGENTS.md, and Claude Code doesn't
load AGENTS.md by itself when a CLAUDE.md exists. The guide reached a session only when the
agent chose to open it. Claude Code also asks before git commands that could sweep up or
discard another session's work.

instance-impact: **engine files auto-update; then two steps if your agent is Claude Code.**
(1) Replace `CLAUDE.md` with the template's two lines. If you wrote your own content into it,
keep that content and put `@AGENTS.md` on its own line at the top instead. `/update` can't do
this silently: your copy predates the engine marker, so no old hash exists to compare against.
(2) Copy `agents/claude-code/settings.json` to `.claude/settings.json`, or merge its
`permissions.ask` entries into the one you have. Other agents: nothing to do.

- **CLAUDE.md imports AGENTS.md.** It's two lines: the `mova:engine` marker and `@AGENTS.md`.
  The marker puts CLAUDE.md in the manifest, so future fixes reach instances through
  `/update`.
- **The Claude Code adapter ships `ask` permissions** for `git add -A`, `git commit -a`,
  `stash`, `reset`, `restore`, `checkout --`, and `clean`. AGENTS.md keeps the rule in prose
  for every agent, now with its reason: a dirty tree you didn't dirty is a sibling's retro in
  flight.
- **AGENTS.md lost its Status section.** It was maintainer history loading in every learner's
  session, and older instance copies already contradicted it. It moved to README → Status.
  AGENTS.md keeps one short section naming the paths that have never run, so a failure there
  reads as untested rather than broken.
- **From limba's PORT-033**, which rewrote limba's instruction file for current models. The
  lock doesn't move: PORT-030 to PORT-032 are still unsynced.

## 0.19.0 — 2026-09-08

When a mark says you were wrong, the sheet now says whether anything checked that mark or
whether it is the agent's reading. When you say the agent is wrong, it checks before it
answers, instead of agreeing first and finding sources afterward. And a workspace whose
language pack has no dictionary tells you so at the start of every session, not only on the
hub.

instance-impact: **engine files auto-update, then run `npm test`.** Every marked sheet already
in `work/sets/` that scores something wrong and carries no `Source:` line fails the new check,
and the failure message gives the one line to add — for a sheet nobody checked at the time,
`Source: the agent's word — unverified`. From then on every sheet is written with its trail:
`?` after each mark nothing attested, a `Source:` line at the foot, one sentence inviting a
dispute. Nothing else you write changes.

- **Marking is a checkpoint of the verification rule.** A ❌ or 🟡 is a claim about the
  language — the agent's reading against yours — and takes the same three states as a ledger
  row: attested through the pack's dictionary, tutor-confirmed, or marked `?`. A word's meaning
  or form goes through the adapter before the sheet is published. What no adapter can check —
  what a clause means, which reading a form allows — wears `?`, counts in the score until it is
  disputed, and the sheet says you may dispute it. `work/sets/marked.test.ts` holds the trail.
  It is the marked-sheet twin of `visualcheck.mjs`'s check 10 and deliberately as crude: it
  cannot tell a grammar mark from a spelling mark, so it asks for the sheet-level trail
  whenever anything is marked wrong and trusts the rule for the per-mark `?`. Its trail key is
  structural (`Source:` opening a line), not a wording, because sheets are written in your
  meta-language.
- **A dispute is a check, not a concession.** When you contradict a mark or a taught claim, the
  first sentence back names the check, never the verdict; the next reports what the check found
  and where; then the item is re-scored. Where nothing can check it, the `?` stays, both
  readings go into the sheet, the score line says *disputed*, and the item joins the list a
  tutor clears. The rule cuts both ways on purpose: an agent that yields to pushback cannot tell
  a right learner from a wrong one.
- **The null adapter speaks at the door.** The session opener carries one line — every fact
  today is the agent's word until a tutor confirms it — whenever the pack declares no
  dictionary. The hub already said it; a banner on a page you open later is not a sentence at
  the start.
- **The incident.** The first study session ever to run to completion on this engine — a
  Turkish instance under ChatGPT's Codex agent, 2026-08-24, on a pack built at setup with no
  dictionary — scored a correct reading of a time clause as wrong, built the repair lesson on
  its own wrong rule, and reversed itself on the learner's word with no `?` anywhere. The
  learner left. The story is in `docs/mechanics/why/verification.md`, which is new:
  verification.md's own preamble and incidents moved there, verbatim, to pay for the rules.
- **What this buys, said plainly.** It does not lower the model's error rate. It guarantees you
  can always tell a checked mark from the agent's guess, and that a dispute triggers a check
  instead of a flip. On a pack with no dictionary every grammar-meaning mark is `?` by
  construction; the only route to attested is the tutor loop.
- **The word budgets held.** session_format.md gained the sheet trail, the dispute bullet in
  the correction policy, the door line and the sheet's file name, and paid by moving nine
  provenance paragraphs verbatim to `why/session_format.md`: 7,399 of 7,400 words. verification.md is
  1,298 of 1,300 after its preamble left. teaching.md's "absolutes have to be earned" gained
  one item: a ❌ on what a sentence means is an absolute about your reading, earned the same
  way. verification.md's Status section, which still said the enforcement tooling was
  unshipped, now names what enforces what.
- **Also.** The profile page counts sheets named `_marked.md` as well as `-marked.md` — the
  first instance to write one used the underscore, and a sheet the stats page cannot see reads
  as a session with nothing wrong. AGENTS.md's Status no longer says no session has run. The
  close-out failure the same session reported (`tts: say` stopped `closeout.mjs`) shipped as
  0.18.1.
- **The release manifest describes this tree only.** `scripts/manifest.mjs` now skips agent
  worktrees (`.claude/worktrees/`, `.worktrees/`), the same rule `vitest.config.ts` already
  applies. Cutting this release with a sibling worktree open hashed 121 of its files into the
  engine manifest — paths `/update` would have offered every instance as new engine files.
- **Owed to limba**, written down in `upstream/backports/2026-09-08_marking-is-a-claim.md`.

## 0.18.1 — 2026-09-08

Your close-out finishes on every voice setting. A workspace whose profile says `tts: say` could
not close a session that added a word: the close-out ran the audio warmer, the warmer refused, and
the ritual stopped at a step it treats as required.

instance-impact: **engine files auto-update.** Nothing to run and nothing to rewrite. If your
profile says `tts: say` or `tts: none`, the close-out now prints one line saying why there is no
audio cache to warm and carries on. Your deck never carried embedded sound on those settings, and
that has not changed. One new engine test arrives with the update and runs inside your `npm test`:
it fails if the profile's `tts:` value is not one setup writes (`edge`, `say`, `none`), with the
fix in the message.

- **Two scripts held two copies of one rule, and they disagreed.** The audio cache holds
  edge-voice clips only, so `tts: say` has nothing to warm — `speak.sh` speaks the system voice
  live, never through the cache. The close-out's gate said *any voice but `none`*; the warmer's
  said *`edge` only*, with exit 1. `say` is a value setup writes, and the first real instance to
  finish a session hit it (Turkish, ChatGPT Codex, 2026-08-24): the agent ran the regeneration
  chain by hand and reported the script as tripping on `tts: say`.
- **The verdict lives once now.** `scripts/tts-cache.mjs` answers *is the cache in play* from
  the profile's `audio:` and `tts:` together, and the four scripts that read or fill the cache —
  the deck, the warmer, the embedder, the close-out — take it from there. The deck's rule was
  already the right one (`audio: true` and `tts: edge`); the other three now match it. One
  consequence worth naming: the warmer and the embedder no longer run on `audio: false`, because
  the deck would not have embedded what they produced.
- **"Nothing to warm" is a state, not a failure.** The warmer exits 0 with one line on a profile
  with no cache, so a close-out that follows step 9 literally on a `say` profile is not stopped
  by it. The embedder keeps its exit 1: a page authored with audio placeholders that ships mute
  is the failure it exists to catch (limba, 2026-08-12), and a `say` profile authors that page
  mute instead.
- **A test holds the four to one gate, across every value setup writes.** The list of values is
  read off the profile template, so a value added there without a verdict fails; every cache
  script must import the verdict and keep no `"edge"` comparison of its own — source-level and
  crude, the way the narration gate is crude; and in an instance the profile's own value must be
  documented, and on a profile with no cache the warmer must exit 0. Verified by hand against
  `say`, `none`, `edge` and `audio: false` fixture profiles, including a full `--finish` on `say`.
- **Not a port, and nothing owed back.** limba's close-out has no profile gate at all — one
  learner, one voice — so the disagreement was born in the port. `docs/mechanics/media.md` marks
  the two cache fillers as `tts: edge` only in its toolbox (3,095 of 3,100 words); the incident
  went to `why/media.md`. Close-out step 9 in `session_format.md` keeps its wording — *run the
  warmer first* is now true on every profile, because the warmer answers for itself — and the
  file is 2 words under its budget.

## 0.18.0 — 2026-08-25

Building a page and putting it on your board are now one job, not one job and a habit. If a page
is in your index but missing from the hub, `npm test` says so instead of leaving you to notice.

instance-impact: **engine files auto-update, then run one command.** After the update, run
`node scripts/hub.mjs` and commit the result — if your board was already current, that is a
no-op. From then on CI fails when a page you have indexed is missing from the board, with the
fix in the failure message. Nothing changes in what you write.

- **The order has a name now: gate green → index row → hub → send.** It holds for every page
  the board renders — a drill surface, a cheat sheet, a superseded row — not lessons alone.
  The board is generated *from* the index, so a row reaches you only when the generator
  re-runs, and **indexed but not on the board is reachable only by filename, which is not
  reachable.** The hub is your one bookmark; a page it does not carry is a page you have to
  remember the name of.
- **A test holds it, and it checks containment rather than freshness.** The obvious check is to
  regenerate and diff, and it is the wrong one: your hub also prints queue counts and a
  countdown to your goal date, so it differs from its own regeneration most mornings and would
  go red on a clean tree while catching nothing. Whether every page the index links to appears
  on the board is deterministic, and it is exactly what goes wrong. Verified by taking one
  filename out of a generated hub and watching the check fail.
- **The failure names both ways a page goes missing.** Either the board is stale, or the row's
  Units cell names no unit your curriculum defines and no `U00` — in which case the generator
  renders that page nowhere and says so on stdout, and regenerating will never fix it. Fix the
  cell, then regenerate.
- Ported from limba PORT-029. Upstream this was a rule promotion; here the instruction was
  already the index rule's own heading, so what arrived was the enforcement. One divergence,
  owed back upstream and written down in `upstream/backports/`: limba's failure message names
  only the stale board, which sends a session with a mis-filed row round a loop that never goes
  green. Two incidents moved from `docs/mechanics/media.md` to `docs/mechanics/why/media.md` to
  pay for the new words — the word budget working as designed.

## 0.17.0 — 2026-08-20

Your workspace gets a shelf for cheat sheets — pages that hold several units' tables side by
side — and, before you have one, a prompt telling you a cheat sheet is worth asking for. Two
pages that were rendering nowhere now say so out loud.

instance-impact: **engine files auto-update.** Nothing to run and nothing to rewrite. After the
update your hub can show one new section above the unit board, and `node scripts/hub.mjs` can
print one new warning line. What changes in what you *write* is optional: a page that belongs to
no single unit is filed with `U00` in its index row's Units cell and `reference` as its Kind.

- **A shelf for pages that belong to no single unit.** Once several grammar systems have been
  taught, their tables start being confused for one another rather than simply not known — and
  the answer is one page holding them side by side. That page fits no unit, so it goes on the
  `U00` shelf, which the hub renders above the unit board. **It is deliberately not a unit in
  your curriculum**: everything that counts units would have counted it — units done, your pace
  against the goal date, the aspect map CI requires every unit to appear in — and a page that
  re-prints delivered material must move none of those numbers.
- **The hub tells you a cheat sheet is worth having, before you have one.** A section that only
  appears after you have already made a cheat sheet can never tell you cheat sheets exist.
  Upstream, the feature arrived because the learner happened to say the tables were mixing.
  So the slot has two faces and never an empty one: the shelf when you have sheets, and before
  that an offer — fired on the actual symptom, live mistakes spread across three or more systems
  you have already been taught, with those systems named in the sentence. Below that, nothing
  renders at all.
- **A page filed under a unit that does not exist rendered nowhere.** Not on the unit board, not
  anywhere — while its index row still read as filed, so nothing looked wrong. The hub now names
  those pages on stdout, the same rule it already follows for sections it skipped: a thing that
  vanishes without being named is the bug. The report stays quiet until you have a curriculum, so
  a fresh template checkout is not told every page it owns is an orphan.
- Ported from limba PORT-028. Two divergences, both owed back upstream and written down in
  `upstream/backports/`: limba renders the shelf with an inline conditional that bypasses its own
  skip-and-name gate, and limba has no offer — its learner already knows the shelf is there.

## 0.16.0 — 2026-08-19

Your workspace grows a second page. The hub becomes the page you act **from** — one
instruction, and the arithmetic behind it folded away — and a new **Profile & stats** page
becomes the page you look back from. Three numbers that were quietly wrong get fixed, and the
one that had gone dark for twelve days now fails CI rather than disappearing.

instance-impact: **engine files auto-update, then run one command.** After the update, run
`node scripts/profilepage.mjs` once, add its output to your visuals folder, and commit it —
or just close out a session, which now does it for you. Three things change in what you read:
your hub's opening panel is much shorter, with everything it used to say one click away; "this
week" now means Monday to Sunday instead of the last seven days, so the number resets; and your
review queue is reported as two numbers instead of one, because half of it was never a backlog.
**One thing changes in what you write**: a lesson's close-out asks for two scores — grammar and
vocabulary — and a lesson entry with no grammar number now fails `npm test`. Entries you have
already written are exempt.

- **A second page, and the hub could finally stop saying everything.** The hub's opening band
  had grown to six blocks before it said what to do. It now shows one instruction, the material
  waiting, and the week's mix — with the lead sentence, the parts still open, the queue
  arithmetic and the last session's pointer folded behind one disclosure. Everything that came
  off it is on the profile page at the length it deserves: the claims this workspace is
  operating on about you, the coverage ranking in full, every graded check ever recorded, the
  whole error history, and the marking sheets, which nothing had ever read.
- **A summary that is a truncated copy is worth less than a link.** The first attempt put a
  shortened version of the fuller view on the hub. Four bars off a thirteen-bar ranking is too
  few to rank anything by and too many to skim past, on the page you act from. Five hub tiles
  now carry a link instead, **anchored to the section** rather than the top of the page —
  landing on a long page and hunting for the thing you clicked is a hint, not a link. The ids
  are declared in one map and a section with no anchor **fails the build**: a link that quietly
  stops working is worse than one that stops loudly.
- **One unit state, computed once.** The hub derived "how far into this unit are we" three
  separate times, and none of the three could see that a page had been built and not yet
  taught. So a finished page could sit on disk while its own unit's row read *not opened*. One
  function now answers it — `taught` · `part-taught` · `staged` · `not-opened` · `unmapped`,
  with "material waiting" reported alongside, because a part-taught unit can also have its
  second half already built. A free audit falls out: when your curriculum's own status line and
  its aspect statuses disagree — which happens exactly when a close-out flipped one and forgot
  the other — the page shows the disagreement instead of picking a side.
- **THE WEEK was a rolling seven days, so it could never be pointed at.** It never reset, which
  meant "this week" was a different window every morning and your plan's weekly load had nothing
  to be scored against. It is now Monday to Sunday, labelled with its dates and its days
  remaining. *Monday to Sunday, not Monday to Friday*: upstream ran a quarter of its sessions at
  a weekend, and a working-week window would have silently dropped every one. Measure your own
  record rather than inheriting that ratio.
- **THE QUEUE could not reach zero, and said so in the alarm colour.** Tier 1 has a zero-day
  interval, so a tier-1 row falls due again every morning however often you answer it. Blended
  into one total and printed as a debt, a perfectly healthy queue read as an unpayable one.
  Split into **what came around on schedule** — which reaches zero, and is the only half that
  earns an alarm — and **the daily recognition pool**, sized in minutes and never as a count to
  clear. `queue.mjs --counts` prints the same split, so triage and the dashboard tell one story.
  The general rule: **a metric that cannot reach zero must not be rendered as a backlog.**
- **THE GRADED CHECK had gone dark and nothing noticed, including the check itself.** Two
  independent failures. The close-out template asked for `N/M = P%` while the parser demanded a
  literal `/10`, so the score chart could freeze without anything failing — *the two ends of one
  pipeline disagreed and no test compared them*. And a lesson could close with no score at all
  and no surface anywhere would say so. Now the same reader serves the page and the test, the
  chart plots **percentages** so a test of ten and a test of twenty-one compare, the template
  asks for grammar and vocabulary separately, and a lesson entry with no grammar number fails
  CI. A lesson that genuinely ran no check says so on the record with
  `<!-- no-graded-check: <reason> -->` — what the gate removes is skipping it *silently*.
- **The same-day warning stopped naming rows you cannot see.** The queue is oldest-first, so
  rows you already answered today are its tail — and once the backlog runs past the display cap,
  that tail is exactly the part not on screen. The verdict now distinguishes what is shown from
  what is merely due. A surface that makes you re-derive it by hand is the failure the verdict
  was added to remove.
- **Density, measured rather than felt.** A cap on a clamped element is now derived **from** the
  clamp: a card's description was capped looser than the three lines it renders in, so a cell
  could pass its own test and still be cut off mid-word. Long lists fold — a unit's aspects
  after five, and the claims about you to the claim plus its date and evidence, because a
  settled fact nobody scrolls to cannot be contested, which is the only reason that half of the
  page exists. The page you are on now carries the colour in the nav; it used to render muted
  while the pages you could still take rendered coloured, which is the state and the affordance
  exactly backwards.
- **The frame stopped being copied.** The token block was in three places and a fourth was
  about to be written. It now has one definition — `scripts/page-shell.mjs`, which imports the
  pinned theme rather than restating it — and every reader every generated page shares lives in
  `scripts/sources.mjs`. Two new contract tests hold both. Alongside them the rule that decides
  whether a section renders when empty: a **specification** with zero rows is a broken parser
  and throws; a **record** with zero rows is a young workspace, renders nothing, and **names
  what it skipped**, so a section missing from the page *and* from that line is a bug.
- **An error taxonomy needs a code for the letters of a word you already know.** Vowels and
  consonants are siblings, not a hierarchy: a run of one with none of the other means something
  a merged code would delete. The Romanian pack gains `ORTH-SEQ`; every pack's taxonomy gains
  the rule that decides such a case — a near-neighbour earns its own code when its **repair
  route** differs. This one routes to writing practice and never to meeting the word again,
  which is the opposite of what a vocabulary gap prescribes.
- **Two tests were failing in your copy and passing in the template's, which is the worst
  direction for a test to be wrong in.** Both were found by running the whole suite against a
  generated workspace rather than against this repo, and neither has anything to do with the
  rest of this release. The visuals index checked its own authoring guidance as though the
  example filename in it were a real page, so a freshly generated workspace failed on a file
  that was never meant to exist — comments are now stripped before links are read, the same
  discipline the error tally uses for tokens it discusses rather than declares. And a pack
  check asserted that *this repo's* word lists were empty, which stops being true the moment
  you learn your first word; it now makes its claim against a fixture, where the claim is
  actually about the parser.
- **The word budget did its job three times in this release**, which is worth recording because
  it is the mechanism nobody sees working. Every rule added here had to pay for itself: the
  session mechanic and the media mechanic each went over their ceiling, and each time the fix
  was the one the failure message names — provenance moved to `why/`, and the rules a study
  session never needs moved to the page spec, where whoever builds a generator will look.

## 0.15.0 — 2026-08-17

A page build no longer happens in silence. The rule that says so gets the first enforcement
mechanism narration has ever had, and it is a test over the documents rather than over what
the agent said.

instance-impact: **engine files auto-update.** Nothing to regenerate. Two things change in
your sessions: before a lesson builds a page with audio, and before the close-out rebuilds
your deck and hub, you are told it is starting and roughly how long — instead of the chat
going quiet. A new engine test arrives with the update and runs inside your `npm test`.

- **What happened, and it was not here.** limba took this narration mechanic as a back-port
  on 2026-08-17. Five hours later one of its lessons built a 73-clip page — about fifteen
  minutes — and said nothing before, during, or about how long. The learner read the silence
  as a crash. Two of limba's three causes were mova's wording, shipped unchanged; its
  PORT-019 is the diagnosis and this release is mova's answer to it.
- **A trigger an agent cannot evaluate is not a trigger.** `narration.md` § 2 said *"any
  stretch over ~2 minutes"*. An agent has no clock, so that sentence described a situation
  and was never a condition being checked — it fired on nothing, in both repos. **§ 2 now
  triggers on the command you are about to run**, and says the announcement goes *before* the
  chain starts, because once it starts the next chance to speak is when it ends.
- **mova named the ingredient where limba named a chain, and that difference is deliberate.**
  limba has one long silence and named its three commands. mova has three separate stretches
  — the setup build, a page build in a lesson, close-out's regeneration — that share no chain
  but share one slow ingredient: a network call per clip, `tts-embed.mjs` and `tts-warm.mjs`.
  `newvisual.mjs` and `visualcheck.mjs` are deliberately off the list; they are local and
  fast, and a trigger list longer than the things that are actually slow stops being read.
- **The one that was live here: a rule reaches the agent through the verb, not the mechanic.**
  limba's lesson skill quoted § 2 as one word — *"silently"* — the opposite of what that
  section's last paragraph asked for. mova's own `playbooks/lesson.md` ran the whole page
  build with no announcement instruction in the step, and opened its orient with the same
  bare *"silently"* and no pointer. It had nowhere to fire because no lesson has ever run
  here. **The announcement now sits at the step**: `lesson.md` part 2, `session_format.md`
  § Media moves and close-out step 9, `media.md` at both invocations, `teaching.md` rule 10.
  `setup.md` already had it, and is the one flow that never broke.
- **Narration stops being the mechanic with no mechanism.** Whether the agent *said* the
  thing leaves no artifact and never will. But *"the step that runs the slow command carries
  the announcement"* is a static property of this repo. New
  **`docs/narration.callsite.test.ts`** asserts it, holds § 2 to naming the slow commands,
  and fails if § 2 ever reverts to a duration. It is crude the way `agents.test.ts`'s shim
  leak heuristic is crude, and its header says so.
- **It also asserts the general form, and that found a live one.** Every numbered rule in
  `narration.md` must be cited by at least one playbook or mechanic — the weakest useful
  version of *"the noun reached a verb"*. § 6 (one thing at a time) failed it: the interview
  obeyed the rule in full and pointed at it nowhere.
- **A verb may restate a rule; it may not restate it without the pointer.** New convention in
  `docs/mechanics/README.md`. A verb that only said *"obey narration.md"* would put the rule
  out of reach at the moment of action, so paraphrase at the call site is required — what it
  does badly is drop the half it did not quote. Every restatement now carries its `§ N`, and
  a section holding two opposite defaults is never cited by one of them alone.
- **What was not adopted: limba's merge of announce-a-silence and never-show-the-work.** They
  stay two rules here with two headings. The incident is the evidence for that shape — limba's
  merged section led with *never show the work*, the announcement sat last, and the skill that
  quoted it took the half it led with.
- **`narration.md` ends at 899 words of 900, and no rule was dropped to fit.** The room came
  from moving provenance to `why/`, where the budget test says it belongs and where most of it
  was already duplicated, plus one paragraph that left `session_format.md` close-out step 7 so
  step 9 could afford the announcement. limba kept its merge to stay under its own cap and
  filed the tradeoff as an open question; that is the failure mode this note exists to name.
- **A statement waits in [`upstream/backports/`](upstream/backports/).** The call-site gate is
  new machinery limba does not have, and answers the first of the three questions it filed.
  Which commands belong on its list is left to limba.

## 0.14.1 — 2026-08-17

0.14.0 stopped the hub promising a drill takes 10–15 minutes. Five other surfaces were still
promising it. No new capability; this is the rest of the same correction.

instance-impact: **engine files auto-update.** Nothing to regenerate. Your guide pages and the
session-format mechanic stop quoting a drill's minutes as fact about you, and the lesson's
per-part boxes are now labelled as the default split they always were.

- **The gap 0.14.0 left.** `narration.md` § 1 (0.13.1) bars predicted durations and allows
  minutes only where they are measured for *this* learner. The hub now obeys it and prices the
  real queue from `srs.md`'s model. Meanwhile `README.md`, both guide pages and the mechanic
  itself still stated a flat **10–15 minutes** for a drill, and **about an hour** for a lesson,
  to a reader they were measured on. On the same page as a computed ≈66′ figure, that is the
  two-totals-for-one-thing defect 0.14.0 was released to fix, one level up.
- **Nobody re-fitted the number, deliberately.** limba measured 15′, 36′ and 55′ against that
  heading — a 3.7× spread — and judged three points enough to stop quoting it and not enough to
  replace it (its PORT-016). Its `/review` verb owns the re-fit. mova has taught nobody, so it
  has no standing to invent a replacement. **What changed is the claim, not the figure.**
- **`docs/mechanics/session_format.md`** now says the drill's box is **a budget to size the set
  against, never a prediction**, and points at the playbook's size rule, which already framed it
  that way and was the only place saying so. The 3.7× overrun and the decision not to re-fit
  moved to `why/session_format.md`.
- **The two guide pages state the protocol instead of a number.** The verb table's length column
  reads *"Your budget — the queue inside it"*, and each block's section says *say the time you
  have; the set is sized to fit it*. That is what actually happens, and it is more use to a
  learner than a figure that was wrong by 3.7× for the person it was taken from.
- **The five-part table is labelled `Default box`** with one sentence saying whose learner it was
  fitted to and that the review playbook re-fits it. The numbers are unchanged — this is the
  mechanics' `default (measured on limba's learner — recalibrate)` marker translated into the
  language a learner reads.
- **`README.md` keeps real minutes, and that is not an exception being smuggled.** Its reader has
  not adopted anything yet and cannot use "one unit's material" to decide whether this fits their
  evening — the same case `narration.md` § 1 already carves out for the setup build. So it quotes
  what the reference workspace actually measured (drills at 15, 36 and 55 minutes; its one timed
  lesson ≈70, not necessarily in one sitting) and says whose numbers those are.
- **Two statements now wait in [`upstream/backports/`](upstream/backports/).** limba's own
  `session_format.md` states the box bare while its drill skill calls it a constraint — the same
  one-line gap. Whether limba should re-fit rather than re-frame is left to limba, which is where
  the sessions are.
- The mechanics word budget bit twice more. `session_format.md` ends at 7,375 of 7,400 words with
  three more incident retellings moved to `why/` — including one that had been inlined in a table
  cell the hub renders, where nobody would look for provenance.

## 0.14.0 — 2026-08-17

The hub stops publishing three signals and letting the learner arbitrate. It states one
verdict: which block to run, and the arithmetic that picked it.

instance-impact: **engine files auto-update.** Nothing to regenerate. Two things change on
your hub the next time it builds: the "What to do next" panel now opens with a single
recommendation, and every queue figure on the page drops rows you already reviewed today, so
it agrees with `node scripts/queue.mjs`. **If your session log has entries whose `Next`
pointer states a queue count, move `POINTER.effectiveFrom` in `docs/logs.entries.test.ts`
forward to today's date** — the rule is not retroactive and those entries were written before
it existed.

- **The defect, inherited whole from limba (PORT-018).** The panel printed the last session's
  pointer, a weekly pace verdict and the queue's size side by side. Each answers a different
  question and nothing ranked them, so the surface whose job is *what do I do now* became
  something to double-check. mova's copy was worse in two ways: it printed the pointer **twice**
  — once as "Next block", then the whole sentence again four lines below — and it carried a
  hardcoded **"a drill clears the queue in 10–15 minutes"**, which limba measured 68 minutes
  wrong. That string also outlived 0.13.1, which had retracted predicted durations from
  narration hours earlier: the rule shipped and a generated page went on contradicting it.
- **`docs/mechanics/session_format.md` → *Which block to run*** is the new rule, and the
  portable half of PORT-018. **The week's mix decides** — whichever side of the phase's load is
  further behind its share over the trailing 7 days, each side being a target *and* a ceiling,
  ties to the lesson. **The queue never decides**: it sizes part 1 and breaks a tie only toward
  a drill, the one block where the queue is the whole session. **The pointer never decides**:
  item (1) says what *leads* the block. Orient step 4 now routes here instead of reading two
  numbers and stopping.
- **One queue, one number.** `hub.mjs` counted every due row; `queue.mjs` drops the rows already
  reviewed today, because part 1 cannot score them again — and said so in its own docstring the
  whole time. Both now count `unseen`, the skipped rows are named in the tile rather than
  silently dropped, and the panel does the subtraction it used to leave to the learner: how far
  part 1's box reaches, how many keep their place in line, and what the alternative block would
  cost from `srs.md`'s model.
- **A pointer states work, never counts** (`docs/logs/README.md`, gated by
  `docs/logs.entries.test.ts` from `POINTER.effectiveFrom`). Counts freeze at close-out while
  every reader recomputes its own, so a pointer names the *set* — "the 14 city words" — and
  `queue.mjs` owns the number. The gate is dated because append-only logs cannot be edited to
  satisfy a later rule, and `hub.mjs` strips the frozen clause out of older pointers rather than
  print two totals for one queue.
- **The verdict never names a verb this focus mode refuses**, and the mix chips no longer score
  a mode against one — 0.7.1's rule, which the chips had been quietly breaking. In a
  drill-only instance there is no mix to weigh, so the panel states no verdict and falls back to
  the pointer.
- **Found while verifying, and fixed here: the verdict could name a drill with nothing to run.**
  When the mix owes a drill and every due row was already reviewed today, a drill has no
  content — its whole session *is* the queue. mova now runs the lesson and says why, keeping the
  owed drill visible. limba's verdict has the same shape; the statement is in
  [`upstream/backports/`](upstream/backports/) and the ranking rule may yet want a clause of its
  own.
- **`vitest.config.ts` is new (PORT-017), and ported against its `Kind: infra`.** Vitest's
  default `exclude` covers `node_modules` and nothing else, so a full checkout sitting under the
  repo root — an agent worktree — gets its `*.test.ts` files collected and run beside the real
  suite. Every contract test here resolves its data relative to itself, so those runs report on
  a tree the session cannot see, edit or fix, and "my mistake" becomes indistinguishable from "a
  sibling mid-edit". It cost limba a blocked close-out after two sessions reported it and
  correctly declined to delete someone else's directory. An instance inherits this the moment
  any agent opens a worktree, and its human is a learner with no correct action available —
  which is why the map now carries a row for a file limba filed as its own plumbing.
- **The mechanics word budget did its job and is worth recording.** `session_format.md` had
  ~13 words of headroom, so the new rule did not fit. What moved to `why/session_format.md` was
  provenance: the file's header had grown a central list of markers while telling every reader
  that provenance belongs in `why/`, plus three incident retellings. The rule file ends at
  7,398 of 7,400 words.
- `upstream/limba.lock` advances to PORT-018 (limba `a37d5ef`).

## 0.13.1 — 2026-08-17

Two rules shipped this morning, corrected the same day by the repo they were sent to. No new
capability; this is 0.8.0 being wrong and limba having the data to prove it.

instance-impact: **engine files auto-update.** Nothing to do — but the agent will say less
than it did yesterday, and that is the point.

- **Predicted durations are out; counts are in.** § 1 of the narration mechanic told a block
  to state "a count and the minutes that count implies", and the lesson opener said "about an
  hour". limba checked its own session log — which has carried wall-clock durations since the
  SRS cost model was found unfittable — and its **two recorded minute forecasts missed by
  roughly 2× over and 5× under, while the item counts were close.** A number a learner plans
  their evening around, wrong by 5×, costs more than the silence it replaced. Blocks are now
  priced as a count against the agreed budget; minutes are quoted only where they are
  measured for that learner, and never as the headline.
- **The setup build estimate stays in minutes**, and the exception is now stated in the rule
  rather than left implicit. It answers a different question: the build is nine steps whether
  it runs ten minutes or fifty, and someone deciding whether to walk away cannot use a step
  count. It remains marked *assumed*, and setup records its own elapsed time so it can be
  re-fitted the way limba re-fit these.
- **"Part 3 of 5" is retracted — it was false on its own terms.** 0.8.0 put a running counter
  at the top of every part, reasoning from the interview counter (`2/4`) that a real
  onboarding run had validated. limba declined it, and its second reason applies here and was
  missed here: **the denominator moves.** Part 1 is conditional — a rule *this repo shipped
  in 0.7.0* — so on every day the review block stands down there are four parts, and the
  count lies. A part is now announced only when the shape departs from the one the learner
  knows, or on resumption.
- **The interview counter is untouched**, in both repos. It earned its place with a learner
  who could not yet see the shape of what they had agreed to; someone twenty sessions into a
  five-part lesson is not that person.
- The measurements moved to `why/narration.md` rather than into the rule file, which is what
  the mechanics word budget is for — the rule file went over on the first attempt and the
  overage was, exactly as the budget's docstring predicts, provenance creeping back in.
- `upstream/limba.lock` advances to PORT-016, the entry these corrections arrived in.

## 0.13.0 — 2026-08-17

A shipped language pack can now reach a workspace that was set up before it existed.

instance-impact: **engine files auto-update, and the next `/update` may offer you a pack.**
Nothing is swapped without an explicit yes, and the offer is always preceded by a report of
what it would change about rows you have already saved.

- **The gap.** `/update` diffs engine files through `upstream/manifest.json`, and pack files
  are deliberately absent from it — hashing them would make every update flag a learner's own
  corrections to their language facts. Right for the active pack; wrong in one case nobody
  designed. A learner whose language shipped **after** they set up is still running the pack
  generated live during setup, by the playbook that once produced a pack advertising an accent
  fold it had not implemented and a silently disabled ledger guard, and no update would ever
  mention that a verified one now exists. The same blindness meant a *fix* to a shipped pack
  could never reach the instances holding it.
- **`upstream/packs.json`** is the missing baseline: the same idea as the manifest, for packs,
  written by the same release ritual. It grants no permission to overwrite anything — it only
  lets update tell three states apart: the template ships no pack for this code, the instance
  already holds the shipped one (so an improvement is an update to offer), or the two differ
  with no baseline (generated at setup, or corrected by the learner — **never assume which**).
- **`scripts/packdiff.mjs` answers the only question worth asking**: not "do the packs
  differ", but "what breaks for *this* learner". It loads both packs, runs both classifiers
  over the rows actually in `state/`, and separates the two outcomes that are not alike —
  a row the candidate cannot classify at all, which fails `state/ledgers.test.ts` and turns a
  learner's workspace red on words they entered correctly, from a row that merely files under
  a different facet on the deck. It also catches rows that would owe a required fact the
  candidate introduces, curriculum levels a candidate's scale drops, and form-marking being
  switched off underneath existing plurals.
- **`playbooks/update.md` § 6b offers, and never takes.** The report is shown before any
  question is asked; a safe swap is offered in one sentence, an unsafe one is described with
  its damage and left to the learner; "no" is complete and returns next update, exactly as
  step 6's *keep* does. A swap replaces `packs/<code>/` and nothing else — the transfer notes,
  error taxonomy and curriculum generated from the old pack at setup have since accreted this
  learner's measured data, and the pack knows nothing about it.
- **Tested against real pack tables rather than mocks**, because a fabricated table proves the
  function runs, not that the shipped packs are safe against each other. German → Spanish
  loses the neuter and is caught; Romanian → Spanish reclassifies `a vorbi` from verb to
  phrase and is correctly reported as advisory rather than breaking; moving *to* Romanian
  catches the rows that would owe its required eu-form.

## 0.12.1 — 2026-08-17

A flaky CI failure, found and removed. No behaviour change for a learner.

instance-impact: **engine files auto-update.** Nothing to do.

- **CI failed on the 0.12.0 push against a page that was never a learner's.**
  `scripts/visual-shell.test.ts` drives the real `newvisual.mjs` CLI on purpose — the
  skeleton's own content is part of what it checks — and that CLI writes into
  `work/visuals/`, where three other scanners look. vitest runs test files in parallel, so
  for a few hundred milliseconds the fixture is visible to them. On the runner,
  `docs/visuals.content.test.ts` enumerated it and then leakcheck opened it *after* the
  shell test's cleanup had removed it. The ENOENT surfaced as **"the answer-leak gate did
  not run"** — the loudest failure message in the visual system, reported against a file
  with no learner behind it, on a run whose actual subject was green.
- **Latent since the tests were ported**, and it fired now because the suite grew enough to
  shift the scheduling. The next push passed with no change, which is the signature worth
  naming: a red run followed by a green one on unrelated work is a race, not a fix.
- **The fixture date is now reserved and every scanner skips it.** `visualcheck.mjs` exports
  `FIXTURE_DATE` and one shared `isAuthoredPage` predicate, and the three places that
  enumerate `work/visuals/` — the CLI's `--all`, the content test, the index test — all use
  it, so they cannot drift about what counts as a learner's page.
- **The two halves are pinned to each other by a test.** `visual-shell.test.ts` now asserts
  that its own fixture carries the reserved date and that `isAuthoredPage` rejects it. A
  future fixture renamed without reading this entry fails immediately instead of flaking
  once a month.
- Verified both directions: the broken probe page is invisible to every scanner under the
  reserved date, and the identical file under a real date still fails on its missing favicon
  and theme overrides.

## 0.12.0 — 2026-08-17

Greek — the first pack in a non-Latin script, and the bug it found in the shared adapter.

instance-impact: **engine files auto-update.** `packs/_shared/wiktionary.mjs` changed how it
reads gender; every pack that uses it benefits, and no instance action is needed. The new
`packs/el/` is inert in a workspace that did not select it.

- **`packs/el/` — Modern Greek.** Three genders, an endings list that turns every regular
  Greek plural into one clean swap (`-ο → -α`, `-ος → -οι`, `-η → -εις`, `-ί → -ιά` with the
  accent visibly moving), and the neuter `-ας/-ατα` and `-ως/-ώτα` classes left to show as
  the genuine stem growth they are. `packcheck` exits 0; CI now runs seven packs.
- **The engine held; the shared adapter did not.** It read a noun's gender from the position
  of a token in the rendered dictionary line, which works in Latin script and fails
  completely in Greek: `βιβλίο • (vivlío) n (plural βιβλία)` puts a transliteration exactly
  where the gender was expected, so **every Greek noun came back genderless** while the rule
  looked correct on five packs. It now reads Wiktionary's own gender markup.
- **That fix had two regressions, and the existing packs' fixtures caught both.** Reading
  the markup naively also picked up the genders of the *forms* listed after the headword:
  Italian `uovo` came back masculine **and** feminine (that is its plural `uova`), and German
  `Stadt` came back feminine and neuter (its diminutive `Städtchen`). The parser now scopes
  gender to the head of each line. Neither would have been visible without recorded
  fixtures pinning the old answers — this is the first time the golden sets have caught a
  live defect rather than documented one.
- **Greek recognises its own verbs.** `verbHeadword` is not null for only the second time
  (the Romanian reference pack is the other): Greek cites verbs in the first person singular
  (`γράφω`, `μιλάω`, `έρχομαι`), which ends in `-ω`, `-ώ` or `-μαι` where nouns do not. Greek
  verb rows need no `(v)` tag, unlike all five Romance and Germanic packs.
- **The normalizer is doing structural work rather than tidying.** Greek is the only pack
  whose look-alikes are *identical glyphs*: `Α` (Greek), `A` (Latin) and `А` (Cyrillic)
  render the same in every font, so a mixed-script word is invisible to the eye and unmatched
  by the ledger. It folds the fourteen Latin capitals, lowercase `o`, and twelve Cyrillic
  homoglyphs — the last is not hypothetical, since this engine came from a learner who types
  Ukrainian daily. It refuses the final sigma (`ς`/`σ` are one letter in two positions) and
  the tonos (a missing accent is a language error that also moves the spoken stress).
- **A fact no pack's row shape can hold** is named rather than hidden: where the accent lands
  in the plural. `παιδί → παιδιά` moves it, `βιβλίο → βιβλία` does not, and nothing
  mechanical catches a row that gets it wrong. The pair marking shows it moving; the
  curriculum teaches the rule.

## 0.11.0 — 2026-08-17

French, German, Italian and Portuguese. With Spanish and Romanian that is six packs, and
the five most-requested European languages now start with no pack build at all — the branch
that dominated the setup estimate.

instance-impact: **none for an existing instance.** A pack is read only by the instance that
selected it at setup; the new directories are inert in an existing workspace.

- **Four packs, each green through the same gate** — `packcheck` exits 0 with no warnings
  for all six, and CI runs every one. Each ships 35 classification fixtures spanning every
  facet, 16 marked form pairs, a normalize fixture per look-alike including uppercase, and
  five recorded dictionary cases of which two expect `found:false`.
- **The endings tables were tuned against real pairs rather than written from grammar
  books**, and four were wrong on the first pass. French needed `eu` and `ail` before
  `cheveu/cheveux` and `travail/travaux` stopped reading as stem changes. German needed `n`
  REMOVED — it cut `Mann` into `Man`+`n` and turned a clean umlaut plural into a fake stem
  change. Italian needed `che/ghe/chi/ghi` replaced by plain `he/hi`, which is the
  difference between `amic|a → amic|he` and a fabricated stem change. Portuguese needed
  `res` removed, because it cut `flor` at its own `r`.
- **What the tuned cuts now teach, without a rule anywhere:** `cheval → chevaux`,
  `Buch → Bücher` and `Stadt → Städte` as umlaut-plus-suffix in two colours,
  `pão → pães` against `limão → limões` against `mão → mãos` side by side, and
  `Mädchen`, `Löffel`, `pays`, `città` and `lápis` marked nowhere at all because nothing
  changes.
- **Every normalizer argues its own list, and two of them argue mostly about what they
  refuse to fold.** German does NOT fold `ae/oe/ue/ss` — the convention is real but
  `Steuer`, `Feuer` and `Museum` spell those pairs as ordinary letters, and `Maße`/`Masse`
  are different words while Swiss German writes `ss` throughout. That is the direct
  correction of the 2026-08-15 pack, which advertised the fold and shipped an identity
  function. Portuguese refuses to touch `à` (the crase), the tilde, and `â ê ô` — `avô` and
  `avó` are different people. French refuses the whole é/è/ê family. What each one DOES fold
  is only the diacritics its language never uses, where a fold cannot hide an error.
- **The empty `required_fact:` is argued four more times, and the answers differ.** French
  and Italian: the fact is gender and the row already carries it. Portuguese: gender plus
  the `-ão` plural, and the second one is a real unguarded gap, stated as such. German: the
  closest call of the five — capitalisation would select nouns cleanly, better than any
  Romance pack manages, but the guard matches its second pattern against the notes cell
  while gender and plural live in the parenthetical, so declaring it would demand the plural
  twice. Italian names a fact its own row shape cannot hold: `uovo (m) → uova (f)`.
- **Provenance is stated in every pack.** Only Romanian was extracted from a measured
  learner. The other five were assembled from references and have taught nobody; each
  `notes.md` says so and marks its rankings assumed, and each ranks its interference notes
  for an English L1 while telling setup to re-rank for any other.

## 0.10.0 — 2026-08-17

The first pack built on the shared dictionary adapter, and the first language a learner can
start without waiting for one to be generated. Spanish is also the language every screenshot
in the README already used.

instance-impact: **none for an existing instance.** A pack is only read by the instance that
selected it at setup; `packs/es/` is inert in a Romanian workspace. A learner who wants to
switch languages generates a new workspace rather than re-pointing this key.

- **`packs/es/` — Spanish.** Two genders, an endings list cut so that a singular and its
  plural land on the same stem, the indefinite article as the gender test in both numbers,
  and 35 classification fixtures spanning every facet the deck shows. Verified through the
  same gate as the reference pack: `node scripts/packcheck.mjs es` exits 0 with no warnings,
  and CI now runs it for both packs.
- **The pair fixtures teach what Spanish plurals actually do**, because the endings list was
  chosen for it: `lápiz → lápices` and `luz → luces` mark the z→c stem change, `canción →
  canciones` and `inglés → ingleses` mark the accent that drops, `joven → jóvenes` marks the
  accent that appears, and `crisis` and `lunes` are marked nowhere at all because nothing
  changes. None of that is hand-written; it falls out of the cut.
- **The normalizer folds two classes and says why each is safe.** ñ typed on a layout that
  has none (`ń`, `ň`, `ǹ`, `ṅ`), and the wrong accent direction from layouts where grave or
  circumflex is the easy key. The second is safe *for Spanish specifically*: the language
  uses neither diacritic, so `à` is not a possible Spanish spelling and folding it cannot
  hide a real error. The identical fold would be destructive in French or Italian — which is
  the reason normalizers belong to packs. A **missing** accent is still graded as an error,
  and `ü` is never touched. All 24 look-alikes carry a fixture, uppercase included.
- **The one empty manifest key is argued in prose rather than left blank.** Spanish has an
  unpredictable per-lexeme fact — the verb stem-change class, `pensar → pienso` — and it is
  deliberately NOT declared, because the ledger guard selects rows by matching the target
  string and Spanish has no infinitive marker: any pattern that catches `pensar` also
  catches `mujer` and `lugar`. The pack states the key, what is lost, and why declaring an
  unenforceable guard would be worse. That is the failure the first agent-generated pack
  shipped silently.
- **`verbHeadword` is `null` for the same reason**, so Spanish verb rows carry an explicit
  tag. A shape rule would classify `mujer` as a verb.
- **The shared adapter is now under CI.** `packs/es/dictionary.mjs` is fourteen lines of
  configuration; the recorded fixtures pin the cases that matter — the irregular plural this
  pack contains no rule for, the two-gender headword, the `-a`-and-masculine trap, a real
  Wiktionary page with no Spanish section, and a word with no page at all.

## 0.9.0 — 2026-08-17

A workspace can now report a defect back to the template it was copied from. Until today
every channel pointed inward: `retro` files findings into the instance, `update` pulls the
template's changes in, and a defect found in a generated workspace died there. AGENTS.md
had asked for a report since the bootstrap commit and named no mechanism.

instance-impact: **engine files auto-update, plus one regeneration step.** A new verb
means a new adapter shim — **regenerate the adapters (setup step 8)** so "report this" and
"send feedback" route. The verb is live in every focus mode.

- **New verb: `feedback`.** The agent drafts an engine-defect report, **shows the learner
  every line that would leave their machine**, and sends nothing without a yes to that
  specific report. "No" is a complete ending and the report stays on disk. Three exits, in
  order: `gh issue create` when the CLI is authenticated, otherwise a prefilled issue link
  the learner clicks and submits themselves, otherwise the file to paste anywhere. Filing on
  GitHub needs an account, and the verb says so in one sentence rather than letting the
  learner discover it at a sign-in wall.
- **The payload carries structure and never content.** Version, agent, OS, mode, focus,
  `goal_kind` as a kind, pack code and contrast-ranking codes, capability flags, the engine
  file, and a reproduction. Never the goal in the learner's words, the scenario list, ledger
  rows, log entries, snapshots, their writing, or **the open-floor answers the interview
  collects** — which are the most personal thing setup ever asks for. A defect that cannot
  be shown without personal content is described in shape, and the report says so. The
  product's promise is that nothing leaves the machine; a feedback verb that quietly ships a
  study record would break the thing that makes the workspace worth trusting.
- **The reporter is usually the agent, not the learner.** A learner cannot report that a
  compressed rule was backwards or that a drill under-predicted its own length by 5× —
  they do not know those were wrong. Both happened in generated instances and both died
  locally. `retro` now routes engine-shaped findings here, offers the report once, and drops
  it if the answer is no.
- **What the verb will not do**, stated as rules rather than left to judgment: never send on
  a standing permission or an earlier yes, never batch (a queue that drains itself is
  telemetry with extra steps), never send a usage digest unless the learner asked for exactly
  that and read it, never fabricate a reproduction.
- Sent and declined reports are kept in `work/feedback/outbound/`, so the same defect is not
  reported twice and the learner keeps a copy of what they approved.
- A GitHub issue template lands in `.github/ISSUE_TEMPLATE/`, carrying the same sections and
  the same warning about personal content, for anyone filing by hand.

## 0.8.0 — 2026-08-17

Narration becomes a mechanic, and onboarding stops guessing how long it will take. 0.5.0
fixed what the first real onboarding run said out loud — the missing topic counter, the
unannounced silence, the handoff that was a verb instead of a tour. This release goes after
what that run could not see: the interview asked for answers it could work out itself, the
build priced every workspace the same, an interrupted build could not be resumed at all,
and the rules for how the agent talks lived in five files and one generated one.

instance-impact: **engine files auto-update, plus one optional profile edit.** The
narration mechanic, the session rules and the guides all arrive through `/update`. The
setup and interview changes affect newly generated workspaces only; setup does not run
twice. Optional: your profile's Operational profile carries a seeded bullet reading
*"Plain language at the moments that matter…"* — that rule now lives in the engine
(`docs/mechanics/narration.md`) and reaches you through updates. Replace the bullet with a
pointer to it, and keep a bullet there only for a way **your** narration differs from the
default.

- **How the agent talks was a rule with no owner.** `playbooks/drill.md` owned "state the
  item count and the minutes it implies", the close-out ritual owned "say it plainly", media
  owned the clickable link, setup owned the two clocks, `session_format.md` owned "state
  position before content" — and the profile *template* shipped "plain language at the
  moments that matter" as a seeded bullet, so an engine rule sat once per instance where no
  template update could ever reach it. New `docs/mechanics/narration.md` owns all of it in
  eight rules and 760 words, with the story in `why/narration.md` and the scattered copies
  reduced to pointers. The scattering had a real cost: 0.5.0's interview counter was never
  applied to the build or to the session, because nothing connected the three as one
  problem.
- **The rule nobody had written down: never show the work.** Every agent that runs this
  workspace is a coding agent whose default register is the tool call — the command, the
  output, the green tick. The learner is not a developer and setup's promise is that they
  answer questions and nothing else. Narration rule 3 states the test: say it only when it
  changes what the learner should do.
- **An interrupted setup could not be resumed, and the gate made sure of it.** The gate
  asked one question — does `docs/reference/profile.md` exist — and the profile is written
  at generation step 1 of 9. So a build that died at step 4 answered the learner's second
  attempt with "this is already an instance, use update": ten minutes of interview thrown
  away, and a half-built workspace that looked finished. The gate now decides between three
  states, resumes at the first missing artifact, and re-asks only the one or two answers
  that live outside the profile and are genuinely gone with their file.
- **One estimate for every build, and the builds differ by a factor of four.** "5–15
  minutes" was stated three times and computed never. A target language with no pack adds a
  full pack build; a zip copy adds an install; an exam goal adds format research; a narrow
  focus subtracts. Setup now carries a branch table, prices *this* build at the interview's
  close, re-anchors when a branch runs long, and says the reason with the number — "closer
  to 45, because Georgian has no language pack yet".
- **Nine steps, nine validations, nine commits, and the learner heard none of it.** 0.5.0
  announced the silence; the silence still ran unbroken to the handoff. Each finished step
  now reports one plain line saying what now exists — not what is being done, and never a
  command, a path or a test result.
- **The greeting described the questions and not the consequences.** It never said what the
  learner would end up with, and never said what setup does on their machine: installs
  dependencies, writes inside this folder and nowhere else, commits after each step, runs
  its own checks, and reaches the network exactly three times, each named. That paragraph is
  where a learner decides whether to leave an agent alone with their disk for a quarter of
  an hour.
- **The tour never said what the product is.** Its four parts told the learner what to say,
  where things live, what the plan holds and what happens next — all true, all downstream of
  a sentence nobody had written: what this thing is and what a week with it looks like. The
  README says it; the learner who was handed a copy by a friend has never read the README.
  The tour now opens with the loop in two or three sentences.
- **Six interview topics became four, and one of them stopped being a question.** The
  starting level merged into the goal topic — it is the one answer nothing downstream trusts,
  since the placement measures it — and tuition merged into a "shape of the program" topic.
  The explanation language is confirmed rather than asked (they opened the conversation in
  it), and the focus mode is now **proposed from the goal** rather than offered as a
  four-way menu to someone who has not yet seen the apparatus. `full` remains the default
  and the only mode any real run has exercised. Nothing the templates consume was dropped;
  the counter reads `1/4` through `4/4`.
- **A new last question: "anything else I should know before I build this?"** The four topics
  collect what generation cannot run without. They collected nothing about the keyboard that
  cannot type the target language, the six weeks already lost to a move, or the job the
  language actually has to work in — and every one of those is cheaper now than as a
  correction three sessions in. It is one open question with two or three examples, silence
  is a complete answer, and it is not numbered so it cannot become a fifth topic. The profile
  template already had the slot.
- **A session now has visible edges.** `session_format.md` had said "state position before
  content" since the scaffold and meant position *in the material*. A learner inside a
  sixty-minute session was never told which of five parts they were in — the identical
  complaint the interview counter answered on the other side of the product. The lesson
  prices itself at the door, adjusted for what is actually due, and each part opens by
  saying where it sits.
- **Setup now records what the build cost.** One line in the generation record: wall-clock
  minutes, the estimate it was given, and the branches taken. The branch table above is a
  guess until instances report back, and the SRS cost model spent weeks unfittable for
  exactly this reason — nobody had written the number down.

## 0.7.1 — 2026-08-16

The hub stops advertising verbs the workspace will refuse. Its command list is now filtered
by the instance's own focus mode, and the rule behind it is under CI for the first time.

instance-impact: engine files auto-update. The next `hub.mjs` run rewrites the page; no
regeneration step and nothing for the learner to do.

- **A narrow instance's hub was naming words its own setup had turned off.** The list was
  built by reading every file in `playbooks/`, and every copy ships every playbook whatever
  focus mode was chosen — so a vocabulary-only workspace advertised `lesson`, `write` and
  `mock`, each of which it refuses politely when asked, and `setup`, which runs once before
  the hub exists. Nothing failed and no test could fail: the page was internally consistent
  and externally wrong. The setup tour has carried a rule against exactly this since the
  first real onboarding run; nothing enforced it anywhere else. Found reading the two drawers
  at the foot of the hub, not by running anything.
- **`scripts/verbs.mjs` is the one reader of the focus rule.** It answers "which verbs does
  this instance answer to" from the authority the scenario doc already names — each
  playbook's `scenarios:` frontmatter — with `all` meaning every mode and `none` keeping
  setup and the maintainer sync off a learner's page without a hard-coded name list.
  `tutor-prep` stays gated on the goal contract's `## Tuition` section rather than on the
  mode, which is its own activation rule. Extracted rather than inlined for the reason
  `inline-md.mjs` was: a rule inside `hub.mjs` cannot be tested, and hub defects have reached
  learners twice.
- **The two prose tables that derive from that frontmatter are now checked against it.**
  `setup/scenarios/focus_modes.md` says in its own words that the frontmatter wins when they
  disagree — which is only true if something notices. `scripts/verbs.test.ts` parses both
  that table and the learner-facing one in `docs/guide/commands.md` and requires each to name
  exactly the live verbs of each mode.
- **A short list now says why it is short.** Under a narrow focus the drawer opens with one
  line — this workspace is *vocab*-focused, the other verbs are switched off, say `review` to
  widen it — because a list missing four entries otherwise reads as a workspace that lost
  something.
- Also: a verb's one-line description stopped at a full stop or a hyphen but not at an em
  dash, so `update` printed its entire summary in a column of one-liners, and a one-sentence
  summary kept a trailing stop while the rest had none.

## 0.7.0 — 2026-08-16

A lesson no longer opens with a review block that cannot measure anything. Part 1 is now
conditional, and the condition is the queue rather than the calendar. And the README stops
introducing the workspace with nine verbs: it shows three, tells the learner what the first
session is and what every session after it looks like, and hands the full list to a guide of
its own.

instance-impact: engine files auto-update — including `docs/guide/commands.md`, which is new
and arrives by the update playbook's own rule for a file the instance does not have. No
regeneration step — the playbook shims are pointers, and the session-entry template ships
inside `closeout.mjs`.

- **Part 1 of a lesson runs only when it can produce a score or a tier move.** The workspace
  had already established both halves of the premise and never drawn the conclusion: a row
  moves tier at most once per calendar day, and the calibration band does not apply to a
  same-day repeat — after which the session was still told to run the block, as declared
  re-exposure. That is a ~10′ box inside a 60–75′ lesson spent on rows answered two hours
  ago. Three cases collapse into one gate: nothing due (a first lesson whose placement just
  seeded the ledger), every due row already reviewed today (the second or third lesson in an
  afternoon), and the learner simply asking to start at the teaching. A partly same-day queue
  is the interesting case and it is not a skip — the queue sorts oldest `last` first, so
  today's rows are already its tail and part 1 stops where the tail starts.
- **The gate is a command, not a memory.** `node scripts/queue.mjs --counts` now names the
  verdict for part 1 beside the counts it already printed. It had been reporting the overlap
  since the rule that needs it was written, and nothing downstream ever read it.
- **Re-exposure is offered, not assumed.** It has learner-reported value (*being asked about
  it is what makes me remember it*) and no measured durable value — nothing in this
  machinery's history has shown a same-sitting gain surviving to the next session. That
  combination makes it a two-minute offer, never a mandatory block.
- **A skipped part 1 and a collapsed one no longer look alike in the log.** The session
  entry's SRS field takes `skipped — <reason>` in place of counts, because the review
  playbook re-fits the interval ladder from those entries — and an entry with no score
  otherwise reads as retention failing, which shortens every interval in the wrong
  direction. The review playbook now counts the skips, and treats three declines in a row as
  a finding about the schedule rather than about the learner.
- **The reclaimed time may not become more new material.** The intake volume is a calibrated
  constant; an intake number that moves because part 1 happened to be empty is one no review
  can re-fit. It goes to applied practice, or the session ends early and says so.
- Housekeeping this paid for: `session_format.md` was at 97% of its word budget, so the
  repair loop's three audit findings moved to `docs/mechanics/why/` where the rule file had
  been carrying them.
- **The reverse channel has a home**: `upstream/backports/` holds one statement per change
  that originates here and is owed upstream to limba, and `playbooks/sync-upstream.md` names
  it. The first is this change.
- **The README's "How you use it" is a walkthrough now, not a table.** It had opened with all
  nine verbs at once — reference material standing where the answer to "what do I actually
  do" belongs. It names the three that carry a week, then answers the two questions a new
  learner has, in order: what happens the first time (setup hands over one word, the first run
  of it probes instead of teaching, and the probe may be declined), and what the loop is
  afterwards — read *What to do next* on the hub, say the word in a fresh chat, let the
  session close itself, browse the deck between sessions, come back through the hub for any
  topic already covered.
- **`docs/guide/commands.md` is new** — the full command reference: the plain words that reach
  each verb, how long each takes, what each leaves behind, and which of them a focus mode keeps
  live. `how-sessions-run.md` gave up its verb table and its focus table to it and keeps what
  it is for: the rituals, the five parts, the teaching beats, the surfaces, where files land.
  The two pages had been growing the same table twice, which is how a table starts lying.
- **"Chat is the only interface" was inaccurate and is now "chat is the only thing you
  operate."** The hub and the deck are interfaces as well; the claim worth making is that
  neither takes input — both are generated from the files at every close-out.
- **Bookmarking the hub means bookmarking the link, not the path.** `work/visuals/index.html`
  is not something a browser can bookmark, and the absolute URL differs per machine — so the
  README points the learner at the clickable `file://` link every session already prints
  (`docs/mechanics/media.md` → Delivering a visual), rather than at a repo path.
- **The hub printed its verb list twice** — once inside the "how the learning works" drawer and
  again in the Commands drawer directly below it. The drawer now names the three verbs a week
  is made of and points down to the one list.

## 0.6.0 — 2026-08-15

The first real sync from limba, the reference implementation this engine was extracted
from: `PORT-006` through `PORT-014`, nine entries. Three earlier entries were already fixed
here during extraction and one is a back-port that originated in this repo, so none of those
were re-applied.

Two of the nine are defects that were shipping. The rest cut what every session has to read
before it can teach, and automate the third of a session that was spent looking up formats.

instance-impact: engine files auto-update, then **mark the open phase in `docs/plan.md`**.
Add ` (current)` to the heading of the phase you are in — exactly one — and add a `Pacing
table` section with the five columns `Phase | Units | Weeks | Ends | Weekly load`, writing
the load as digits plus a noun (`~4 lessons + 2 drills`). The hub could never find the phase
without the marker and said nothing about it; it now refuses to build instead. The pacing
table is what the new weekly-pace score reads. Nothing else needs a regeneration step:
`docs/mechanics/why/` arrives with the update, and the new scripts need no setup.

- **The hub silently lost the current phase in every generated instance, and now fails
  loudly.** `currentPhase()` matched `[^\n(]+?`, which stops at the first `(` — so a phase
  title carrying its own parenthetical returned null. mova's own plan template never emitted
  the `(current)` marker at all, so this fired for every instance, on every build: the "Right
  now you are in Phase N" line vanished and no pacing row was highlighted, with no error.
  It now collects every marked heading and throws unless there is exactly one, and the plan
  template emits the marker and a pacing table.
- **The hub scores the week against the plan's own weekly-load rule.** The target is read out
  of the current phase's pacing row, never retyped in the script, so changing the plan changes
  the score. Reviews and mocks are held out of both counts — a review is not a study block and
  must not flatter the lesson count. Upstream, a weekly review found 11 sessions in 7 days,
  3 lessons against 8 drills where 4 + 2 was planned, and no unit closed for five days. Every
  one of those numbers was in the log the whole time and nothing added them up. The dashboard
  also headlines the single next block instead of re-publishing the whole pointer.
- **The close-out is three commands instead of ~25 lookups** — `node scripts/closeout.mjs
  --start` at orient, `--brief` when close-out begins, `--finish` at the end. Measured
  upstream across 31 transcripts: a lesson or drill runs ~42 minutes of agent-active time and
  ~13 of those (31%) are the close-out — not the scripts, which take about a second, but ~60
  serial tool calls, a third of them the agent re-reading a document to recall a *format*. So
  the brief prints every template the ritual needs, the `work/` audit computed rather than
  eyeballed, the queue, the live tally and the ledger rows touched. `--finish` regenerates,
  gates the pages this session changed, runs the tests and prints a **path-named** `git add`.
  **It never commits and never writes a log**: `log-append.mjs` owns log IDs inside its lock,
  and a script that commits will eventually sweep a sibling session's unfinished work into a
  session commit. `--start` exists because naming only this session's paths needs to know what
  was already dirty *before* the session began, and orient is the only moment that is true.
- **A teaching page has one frame, and a new page starts from it.** `node
  scripts/newvisual.mjs <slug> [--vocab]` writes a skeleton whose tokens, themes, hub link and
  reveal machinery are already correct and which **passes every `visualcheck` gate before a
  word of content exists** — asserted end to end by running the real CLI in CI. In mova the
  frame is read from the pinned `docs/visual/starter.html` rather than copied into the
  generator, so the reference page stays the single definition.
- **`visualcheck` now catches a duplicated audio-player STYLE, which had no runtime symptom.**
  The existing check caught a duplicated player *script*; a hand-copied `<style>` block was
  invisible to it, and upstream two live pages carried the player's CSS two and three times
  over. Run against those pages, the new check reports exactly 2 and 3 and clears the other
  seventeen. It counts *definitions*, not rules: `.vocab td.au .tts-row` is a scoped override
  that needs the player to exist, and one correct player legitimately carries three
  `.tts-play` rules.
- **Each teaching beat now has one home.** The 2026-07-31 chat/visual table licensed six of
  the eight beats in both places — three marked *"yes, in full"* — so the duplication it was
  written to stop simply moved out of tables and into prose and ran for two weeks. Before the
  page, chat carries four things and nothing else: placement in a line or two, the load as a
  number, one trap, the link. After the page it is answer-only, at any length. The test: if a
  chat sentence would survive deletion because the page already says it, delete it.
- **A compressed rule may compress the rule, never contradict the table.** The carry-away line
  is the shortest thing on a page, so it is what the learner runs from memory, and it outranks
  the table whenever the two disagree. Upstream, a page tabled a three-case rule correctly,
  compressed it to one case, and produced a form that does not exist — twice, the second time
  on a retest *after* the correction had been published in the same session. Before shipping
  the line, run it over the hardest row of its own table.
- **A session-log entry points at the record; it does not restate it.** Lesson, drill, write
  and vocab entries warn at 350 words and fail past 450; reviews and mocks are exempt, because
  a synthesis across many sessions is meant to be long. Upstream entries ran 794–1,544 words,
  and the difference was retelling: that log named 48 distinct `ERR-NNN` IDs, every one of
  which already had its own entry averaging 262 words. Not retroactive — the budget starts
  2026-08-16, since old entries are immutable — and `<!-- long-entry: <reason> -->` keeps the
  length of an entry that earns it.
- **A mechanics file holds rules; `docs/mechanics/why/<file>.md` holds the story.** Study
  sessions read the rule file; `playbooks/retro.md` and the housekeeping pass read both, and
  new provenance is written to `why/`. 2,890 words of incidents, audits and retractions moved
  out of `session_format.md`, `srs.md`, `teaching.md` and `media.md` — **moved paragraph for
  paragraph, verbatim, nothing summarised or deleted**, and verified by extracting every bold
  claim from each pre-split file and checking it against the new pair. **The marker stays with
  the rule**: a rule keeps its *assumed* / *derived from* / *measured* tag where a session
  reads it, because that tag is what tells a session whether it may question the rule.
- **The rule files are budgeted and `why/` is not, because the split alone would refill.** The
  retro loop has no counter-pressure by construction — each retro adds a rule *and* the story
  justifying it, and nothing has ever removed either. Upstream, `session_format.md` grew 401 →
  7,250 words in sixteen days. `docs/consequential.test.ts` caps each rule file at today's
  weight, leaves `why/` uncapped as the pressure valve, and refuses a `why/` file with no
  matching rule file so provenance cannot outlive what it explains. Going over is not a style
  failure: it means provenance crept back (move it) or the file gained rules (retire one).
- **The SRS per-block cost is measured rather than assumed, and the tier-1 gate is documented
  as uncomputed.** Three upstream drills with recorded durations fit `a` = 5.7′, 5.5′ and
  15.4′; two land within 30 seconds of the assumed 6, so **the number does not move** — its
  provenance does, and the outlier gains a mechanism (a session that *discovers* something
  spends its time outside the block loop; budget roughly triple). Separately: nothing computes
  the two-clean-passes promotion gate, and any workspace inheriting this ladder inherits the
  hole, so the interim bookkeeping convention is now stated at the gate instead of being
  reinvented per session.
- **The porting contract is total again.** `upstream/map.md` gained rows for limba's four new
  scripts and the `why/` tree, and the four mechanics rows became `split`. A limba file with no
  row is a sync error by design, so the map is the thing that has to stay complete.

## 0.5.1 — 2026-08-15

The Romanian pack's dictionary adapter — the one thing an instance cannot check by hand —
was wrong about 18% of the time, and nothing in the repo ever called it.

instance-impact: engine files auto-update. An instance on the `ro` pack should re-run
`node scripts/factcheck.mjs --out work/.factcheck.json` and regenerate the hub: every
verdict the old adapter recorded was drawn from a parser with four defects, so a clean
sweep and a scary sweep are equally untrustworthy until it is re-run.

- **The adapter reported a wrong verdict on 14 of 79 real ledger rows.** Measured against
  the reference instance's actual vocabulary ledger: 12 contradictions raised against
  correct rows, and 2 dictionary words reported as absent. Two of the false positives are
  words this pack's own `golden/words.json` lists as correct examples. `verification.md`
  exists because a mova learner cannot check the agent's claims about the target language;
  the adapter is the mechanism that check runs on, and a verification tool that cries wolf
  on 18% of a clean ledger is worse than none, because the learner stops reading it. Four
  causes, all now fixed and all pinned by fixtures:
  - It read `definitions.slice(0, 6)`. dexonline returns up to 184 entries in no useful
    order; `obraz` has 42 and every parseable one sits past index 6, so a plain dictionary
    word came back NOT FOUND.
  - It captured the headword and discarded it, so any entry that merely *mentioned* the
    query contributed facts. `ochi` (eye) came back carrying the conjugation of the
    unrelated verb *a ochi*, "to aim"; `carte` could pick up `scorpion`.
  - It stored the inflected slot raw. For a masculine/feminine pair dexonline prints
    `prieteni, -e` — the plural, then the feminine counterpart's *ending* — so a correct
    row never matched its own plural. The slot also holds syllabification (`(pri-e-)`).
  - It answered `found: true` for words with no entry at all: `România` came back with
    `forms: ["#sf#"]`, markup from another slot asserted as an attested form.
- **`lookup()` now reports every sense, because a headword has several.** The contract's
  `gender: string | null` becomes `genders: string[]`: Romanian `calculator` is neuter as
  *computer* and masculine as *person who calculates*, and `ochi` is a noun and a verb.
  `factcheck.mjs` applies the rule that **one attesting sense is attestation** — a
  contradiction now means the source states this kind of fact and none of what it states is
  what the ledger says. It also folds `î`/`â` before comparing, so an older orthography in
  the source is not a contradiction. On the same 79 rows: 0 contradictions.
- **`golden/dictionary.json` tests the parser offline.** `createAdapter(options)` now
  honors `options.fetch`, so packcheck replays recorded real responses — eight words
  covering each defect, including one the source does not have — and asserts what
  `lookup()` returns. Before this, packcheck instantiated the adapter, checked that
  `lookup` was a function, and stopped; nothing in the repo called it. Reintroducing any of
  the first three defects now fails `packcheck ro` with the case's own explanation of what
  it pins. The fixture is a warning rather than an error when absent, and `packs/SPEC.md`
  gains "What a dictionary adapter gets wrong" so the next pack author reads the four
  failures before writing one.

## 0.5.0 — 2026-08-15

Fixes from the first onboarding run driven by a real person rather than a test harness.
Nothing here is a broken file; every item is the workspace failing to say what it was
doing. Setup is the only part of this product a learner meets before they trust it, and it
was running as a black box between two one-line messages.

- **The interview gave no sense of size or progress.** The greeting promised "a few
  questions" and then asked them one at a time with no count, so a learner could not tell a
  two-minute interview from a twenty-minute one — and answered the first topic at the size
  that belonged to the fourth. The greeting now previews the ground the questions cover in
  six short phrases (areas, never the questions themselves — a preview that enumerates is
  the questionnaire dump the interview forbids), and every asked topic opens with its
  position, `1/6` through `6/6`. The denominator is fixed: Topic 7 is the silent
  environment probe and is never counted, and follow-ups inside a topic do not advance it.
- **Generation was an unannounced 5–15 minute silence.** After a turn-by-turn interview,
  the longest pause in the product arrived with no warning, which reads as a crash rather
  than as work. The interview's close now states the number before the build starts, says
  the learner can walk away, and names the one branch that stretches it (a target language
  with no pack). The generation act repeats the figure as it begins.
- **Setup ended with a verb instead of an orientation.** The learner got "ready — say
  lesson" for a workspace they had never seen, built while they were away: no idea which
  verbs it answers to, that the hub and the deck exist, or what the plan it just wrote
  actually says. The handoff is now a four-part tour — what you can say, where things live,
  the plan you just built, and what only you can do — capped at about a screen, with the
  guides carrying the depth.
- **A generated page was handed over as a repo path.** Every page rule said "name it and
  send it inline"; none said "give them something to click", so the learner ended up in a
  file manager hunting for the thing that had just been made for them. Every response that
  builds or regenerates a page now carries its absolute `file://` URL on its own line — the
  hub at every close-out included. The repo path stays as the record.
- **A page reached the hub only at close-out.** The index row and the hub rebuild were both
  close-out steps, so a page could exist, render, and be taught from for an hour while the
  learner's one bookmark did not know it existed — and a second session looking for prepared
  material saw nothing and built it again. Both now happen the moment the page passes
  `visualcheck`: write the row, run `node scripts/hub.mjs`, then teach. Close-out keeps the
  second rebuild (the ledgers moved) and becomes the audit that fills in the delivery date.
  Undated rows now render on the hub as **not taught yet** and sort last inside their unit,
  so a page waiting for a session is not read as this morning's lesson.
- **`/update` could be run but not asked.** The only way to find out whether the template
  had moved was to start the verb that changes files. Update now has an explicit
  **check-only** mode: resolve, fetch, compare, summarize, then stop and ask — nothing in
  the instance touched, and "not now" a complete ending. The weekly `review` runs that
  check on its own, best-effort and silent when there is nothing to say. And
  `template_source` is now resolved at setup from the git remote instead of being demanded
  from the learner at first update — a URL the setup agent had had in hand all along.
- **The free path recommended a second engine as if it could carry the workspace.** A free
  tier metered in requests does not survive an agent that spends dozens of them per
  instruction; the allowance ran out mid-task in a real run. Gemini is now documented as
  the engine you switch *to* for an explanation and away from for the work around it, setup
  is explicitly to be run on the built-in engine, and the guide says plainly that switching
  engines mid-task costs nothing because every file is already on disk.
- **Every generated page had a blank tab icon.** The hub is meant to be the one bookmark and
  study pages sit open in tabs for months, so the surface that identifies them was a generic
  sheet of paper. Every page now carries the mark — a lowercase serif `m` on the theme's
  accent, embedded as a `data:` URI so nothing is requested and a page opened from disk in
  2030 still has it. One canonical string in `scripts/favicon.mjs`: the two generators import
  it, the two `docs/visual/` pages carry it literally, and `visualcheck` fails a page without
  one (holding the engine pages to the exact string, and letting an instance re-tint its own).
  Safari ignores `data:` favicons and will keep showing its generic icon; the alternative was
  a sibling file that dies the moment a page travels, so the tab stays plain there rather
  than the page reaching out.
- **The README described the product without showing it.** Four screenshots now carry the
  three surfaces: the hub's dashboard, one unit card with *taught* and *retained* as two
  separate bars, a study page, and the deck. They come from a synthetic workspace (an English
  speaker, Spanish, 63 days to a B1 exam) run through the real generators, so no image can
  show a layout the scripts do not produce — provenance and the remake recipe are in
  `docs/assets/README.md`.
- instance-impact: **engine files auto-update, plus one regeneration step.**
  `playbooks/update.md`'s summary and triggers changed, and adapter shims carry both as
  their routing data — **regenerate the adapters (setup step 8)** so "anything new?" routes.
  Optional: add `template_source:` to your profile's config block (your agent can read it
  off `git remote get-url origin`) so no future check has to ask for it. The setup and
  interview changes affect newly generated workspaces only; setup does not run twice.
  A page authored **before** this version carries no favicon and will now fail
  `visualcheck`: add the line printed by `node scripts/favicon.mjs` to its `<head>`, or
  rebuild the page. The hub and the deck fix themselves at the next regeneration, and
  `docs/assets/` is README material an instance can delete without consequence.

## 0.4.0 — 2026-08-15

Fixes from the first two lesson pages ever generated in real instances — a German page
(opencode/MiMo) and a Romanian page (Antigravity/Gemini Flash 3.7). Both passed every gate
the workspace had. Both were broken in the same places, which makes them template defects,
not model defects.

- **Every guided attempt on both pages printed its own answer** — "3 of 3 items give their
  answer away" and "5 of 5". The detector that catches this shipped in the repo, had a mode
  built for exactly this page kind, and was named in none of the four documents an agent
  reads while building a page. `visualcheck` is now the single publish gate for a page and
  runs the leak check itself, failing on HIGH. The starter no longer prints the unverified
  promise "the answer appears nowhere else on this page" to the learner.
- **The starter scaffolded four sections; teaching requires eight beats** — in the same
  numbered typography, while the spec says to start from the starter and figure out nothing.
  Both pages were missing exactly the beats the scaffold had no slot for, and beat ⑥ (naming
  what was used but not taught) was missing from both. The starter now scaffolds all eight,
  each saying why it exists and when deleting it is legitimate.
- **A teach-page word list printed open is a drill surface** — the German page's ten-row
  open table was the direct cause of its leak. Now checked, with paradigm tables (the same
  word in several target forms) explicitly exempt as reference material.
- **~70 asserted language facts across the two pages carried no verification trail.** Pages
  now need visible `?` markers or a source note; a pack with no dictionary must say so on
  the page.
- **`formal场合` and a mojibake glyph reached learner-facing files** through every gate.
  Pages are now script-range checked against the instance's own languages.
- **The lesson playbook contradicted the mechanic it tells you not to re-derive**, running
  applied practice before the graded check — the pre-swap order, reversed in the mechanic
  with a warning that scores are not comparable across the change.
- **The placement gate could never fire**: it tested for any snapshot, and setup always
  writes one. It now tests for a *placement* snapshot. Declining the probe is a supported
  choice that records the level as `GUESSED` and leaves the plan milestone open — a guessed
  level is a debt, not a measurement.
- **"Built, not taught" is now a state.** Material prepared without a session gets an index
  row with no delivery date and a hub regeneration so the learner can open it — and writes
  no ledger rows, no scores, no session entry.
- **The visuals index header never matched the code reading it.** `hub.mjs` reads those
  cells positionally; the template declared a different order, so every instance dashboard
  has been rendering scrambled visual cards. Header fixed and the readers documented as
  positional.
- **The pack validator passed a hollow pack.** The first agent-generated pack (German) had
  a normaliser mapping every character to itself, six test fixtures that all pass against a
  function that does nothing, `inflection: false` on a language whose own word list is full
  of Buch/Bücher, a fabricated Duden citation over two endings that are not plural endings,
  and four taxonomy rows whose "wrong" form equalled its own correction — two of which
  reached the generated instance. That pack now fails with 13 named errors. Empty manifest
  keys must be justified in prose; fold claims are executed, not read; fixtures must
  exercise behaviour. `GENERATE.md` now ends each step in output the agent must show.
- Also: the reference Romanian pack gained the four uppercase normalisation fixtures its
  own spec asked for, and the taxonomy linter now runs against the generated instance file
  as well as the pack notes.
- instance-impact: **engine files auto-update, and one manual step.** Any instance created
  before today has the old column order in `work/visuals/README.md` — rewrite that header to
  `Date | Page | Teaches | Units | Kind` when updating, or the dashboard stays scrambled.
  Existing pages will now fail `visualcheck`; that is the point, and fixing them is the
  learner's call, not the updater's.

## 0.3.0 — 2026-08-15

Fixes from two generated instances: an Italian-native learner with no formal goal and a
vocabulary-only focus, and a German-native learner at B1 aiming for B2 with no date and a
weekly tutor. Both passed their own suites (112 and 130 tests); both friction logs traced
back to the same root — **the templates assumed a full-focus learner working toward an
exam**.

- **The handoff promised a verb the instance refuses.** Setup ended by telling every
  learner to say "lesson", which is switched off under a narrow focus — the first
  instruction a learner ever received would have failed. `focus_modes.md` now carries the
  rule everything cites: placement and assessment run in every mode, on whichever verb is
  live (placement rides lesson / drill / drill / write; assessment rides mock / drill /
  drill / mock). Five files defer to it. The interview now promises the ritual and never
  names a verb, because the focus mode is not known when it asks.
- **The pacing arithmetic check was inert on both no-deadline goal kinds.** It arms only
  when the goal states a volume target, and neither the level nor the functional template
  emitted one — so exactly the goals with no date to keep them honest also had no numbers
  check. Both templates now emit it, and smoke reads a skip as a defect for those kinds.
- **The tuition cadence contradicted itself** between the scenario file and the playbook
  that actually runs. Named both cadences, made the playbook the authority, and added
  `playbooks/` to setup's Reads list — the contradiction had been undiscoverable.
- Scenario files disagreed about which verbs are active; every goal template's Assessment
  section now names the verb that runs it, since a running instance never reads `setup/`.
- The curriculum is no longer described as optional under a narrow focus (everything
  downstream requires it; under a narrow focus it orders capture rather than being walked).
- **Two kinds of short contrast ladder.** The existing guidance covered a learner who holds
  little. A learner whose one language is a close relative has a short ladder for the
  opposite reason, and the risk inverts: they hold a confident opinion about every form,
  and it is sometimes wrong. Both cases now stand side by side.
- "The systems align" is a finding in every transfer section, not only register — an
  alignment the learner is never told about is an advantage they never use.
- With several anchors available, the rule that mattered daily was missing: which one to
  reach for. Added, plus a correction — a weakly-held language is barred as a *production*
  source, not as a recognition-side lexical bridge.
- Seed counts became selection rules rather than arithmetic a careful agent has to break.
- The hub called its topic-aspect total "grammar taught" on a page where many aspects are
  lexical; the snapshots README carried an index table an instance could not append to
  without diverging from the template at every update.
- Five further defects surfaced while wiring the above: the lesson playbook's placement
  trigger could never fire, the drill playbook had no placement clause to receive it, the
  mock playbook wrote to the removed index table, and both the README and the level
  scenario carried the same stale assumptions.
- instance-impact: engine files auto-update. Existing instances keep their generated
  files; the setup and template changes affect future generations only.

## 0.2.2 — 2026-08-15

- **README gained a free-path onboarding section** for people who have never installed a
  local AI tool. It names its audience, says plainly that the paid mainstream agents are
  easier and steadier, then gives the free route: opencode Desktop with two free engines
  connected at once — DeepSeek V4 Flash (Free), built into opencode, for driving the workspace's
  scripts, and Gemini 3.7 Flash on a free Google AI Studio key for the teaching-heavy
  sessions — so no single provider's promo ending can strand a learner mid-course. Carries
  the two warnings that decide the choice: free tiers may train on the learner's profile,
  error history and writing, and free tiers close without notice (four did between April
  and June 2026).
- instance-impact: none (README is template-facing; nothing in an instance changes).

## 0.2.1 — 2026-08-14

Fixes from the first full setup proof run (a fictional English-native learner, Romanian
exam scenario, generated from a bare template copy — 138 tests passed on the instance).

- **Setup could not install its own dependencies.** No file ran `npm install`, so the
  zip-download path met `vitest: command not found` at the first smoke step, right after
  the interview promised the learner was done. The environment probe now installs and
  verifies, and smoke names the failure as the agent's, not the generated files'.
- **The `mova:instance` marker broke project-file frontmatter** — an HTML comment above
  `---` makes `status:`/`kind:` parse as null and fails four tests. Marker placement now
  has its stated exception.
- **Focus-mode values disagreed** (`writing` in the profile, `write` in two playbooks), so
  a writing-focus instance would have refused its two core verbs.
- Per-step `npm test` validation was unsatisfiable at steps 5–7 (the adapters suite arms
  at step 1); steps now validate the suites they own, with full green as step 8's gate.
- `docs/reference/resources.md` had no generation step and would never have existed.
- `scripts/dictionary.mjs` gained the reachability CLI the probe and smoke both call for
  (a silent exit 0 read as a pass); `visualcheck --all` now covers the reference pages CI
  checks and stops calling a generated instance "template mode"; `setup` no longer ships
  an adapter for the one verb that refuses to run; deck's status line, and profile.mjs's
  documented key list, both fixed.
- Two pedagogy findings from the monolingual case, now in the templates: with one held
  language the "never stack anchors" rule inverts into *never manufacture an anchor to
  fill a silence*, and an error a language **failed to prevent** needs a different repair
  than one it **caused**.
- instance-impact: none (no instances exist yet); once they do, engine files auto-update
  and the setup/ templates only affect future generations.

## 0.2.0 — 2026-08-13

- Engine extracted from limba per `upstream/map.md`: mechanics docs (srs, session_format,
  teaching, media + new verification.md), all generic scripts (queue, tally, leakcheck,
  log-append, feedback, inline-md, deck, hub, tts-embed/warm, speak, pronounce), contract
  tests with template-mode skip guards, state/logs skeletons.
- Language-pack layer: `scripts/pos.mjs` generic driver (`createClassifier(tables)`),
  `packs/ro/` reference pack (behavior-identical to limba — 475 ledger comparisons, 0
  diffs), `packs/SPEC.md`, `packs/_template/` + GENERATE.md, `scripts/packcheck.mjs` with
  golden fixtures in CI.
- Visual system in `docs/visual/`: SPEC.md, Hallmark-informed tokens.css (OKLCH, spacing
  scale, type pairs; MIT attribution), starter.html, gallery.html, `scripts/visualcheck.mjs`.
  Consistency deliberately overrides Hallmark's layout variation.
- New engine seams: `scripts/profile.mjs` (the one reader of the profile config block),
  `scripts/pack.mjs` (active-pack loader), `scripts/dictionary.mjs` (adapter interface +
  null adapter), `scripts/factcheck.mjs`, `scripts/manifest.mjs` (engine-file hashes).
- Fixes found while porting, not yet upstreamed to limba: hub.mjs review tile printed
  `undefined to produce`; tts-embed re-run miscounted clips; teaching.md beat
  cross-reference (② precedes ④) and list numbering.
- instance-impact: none (no instances exist yet).

## 0.1.0 — 2026-08-13

- Bootstrap: repository skeleton, `upstream/map.md` (total map of limba → mova),
  `upstream/limba.lock` at PORT-001. limba adopted the porting convention
  (`docs/logs/porting_log.md`) in the same change.
- instance-impact: none (no instances exist yet).
