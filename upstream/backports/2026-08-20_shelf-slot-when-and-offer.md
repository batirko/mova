<!-- mova:engine -->
# Nothing ever tells the learner the shelf is there

**Owed to limba.** Found porting PORT-028 into mova 0.17.0.

> **Half paid, 2026-09-08.** limba took part 1 as **PORT-030** — the shelf now renders through
> `when()`, and its hub prints `skipped()` for the first time. Part 2, the offer, is still owed
> and is what this file now waits on. Part 1 is kept below for the record; do not re-port it.

## 1. The shelf renders through an inline ternary, not through `when()` — TAKEN (PORT-030)

`scripts/hub.mjs` renders the shelf as `${shelf.length ? `…` : ""}`. `scripts/sources.mjs:545`
already exports `when()` for this shape, and its own docstring gives the reason: a section that
can vanish can also vanish by accident, when a parser breaks on a mature workspace, and that
failure is silent by construction — so every skip is recorded and every caller prints
`skipped()`.

Nothing is broken today. But the shelf is the *most* likely section in the file to disappear
wrongly, because its whole membership test is a string match on one index cell. Widen the row
schema, rename the column, change how `visuals()` splits `Units`, and the shelf empties with no
signal anywhere — the same class of failure PORT-028 itself closed for orphaned pages, one
function call away from being covered.

The hub already calls `when()` once, so the import and the `skipped()` print are both in place.

## 2. Nothing tells a learner the shelf exists

This is the part that is not a fix to something limba has.

The shelf is invisible until a cheat sheet exists, and a cheat sheet exists only if someone asks
for one. Upstream that gap was closed by the learner happening to say *"all of these tables are
starting to mix in my head"* — which is a good outcome and not a mechanism. A second learner, or
this one on a different week, gets nothing.

## What mova did

The slot has **two faces and never an empty one**, the same one-slot-two-states rule the band
above it already runs on. When sheets exist it is the shelf. Before that it is an offer, and the
offer fires on evidence rather than on a counter: live mistakes spread across three or more
**taught** zones, with the zone names in the sentence so the learner reads why it is being
suggested now. That is the symptom a cheat sheet answers — tables being confused for one another
rather than simply not known. When neither holds, `when()` skips and names the skip.

Three zones is a floor, marked `derived from` limba's one case (the learner asked unprompted
with ten systems delivered). Below three there is nothing to hold side by side.

## What is left to limba

**Whether the offer belongs upstream at all, and if so what fires it.** limba has one learner who
already knows the shelf exists, so the discoverability problem it solves is mova's problem more
than limba's — the template ships to people who have never seen the feature. Taking it anyway
would give limba the one thing mova cannot supply: a measurement of where the confusion actually
starts, which would replace the floor rather than defend it.

Also left open, and mova took no view: whether the shelf should carry any of the board's
arithmetic at all — a cheat sheet re-prints delivered material, so it moves no counts, but limba's
record may show whether re-reading one changes retention, which would make it an event the
ledgers want to know about.
