# Making retromorphism reproducible by an agent

Written 2026-07-31, from a failed attempt and a successful one on the same
page. The failure is the useful part.

## The experiment

An agent was asked to build a public landing page in retromorphism, with
access to everything: the two source codebases, this repository, and the
rendered reference.

**First attempt — read the codebase.** The agent opened `hub.html`, one of the
two implementations, and copied what it found there. Result: the palette was
exactly right — every hex matched — and the grammar was entirely wrong.

| got | should have been |
|---|---|
| `border: 2px` | `--rm-border: 1px solid #000` |
| one uniform radius | asymmetric radii by position |
| one type register | two — body and label |
| no diodes | the triad |
| media query for theme | `[data-rm]` scope |

**Second attempt — read `tokens/core.css` and `docs/anatomy.md`.** Correct on
all five counts, first try.

## What that tells us

A codebase carries values in a form an agent can copy but cannot generalise
from. It sees `border: 2px solid var(--outline)` on one class and reproduces
it; it has no way to know that the system's rule is 1px and that this file is a
local variant. **Colour survives the copy. Grammar does not.**

The token file is what makes the difference, because it names the things that
are not colours: radii, border width, the two font stacks. That is the part
worth investing in for agent use — not more examples, more *named constants
for non-colour decisions*.

## Concrete implications for this repository

1. **Every non-colour decision needs a token.** Radii and border width already
   have one. Type sizes, letter-spacing on labels (`.18em`), and the capped
   hover luminance do not — they live in prose. An agent reproduces tokens and
   paraphrases prose.

2. **State what a value is NOT.** `--rm-border: 1px` did not stop the agent
   from writing 2px, because it had seen 2px in a real file. A line in the
   token file — *"2px appears in hub.html; that file predates the system"* —
   would have.

3. **The rules document is read after the mistake, not before.** `rules.md` is
   excellent and was not consulted until the review. Anything load-bearing
   belongs in the token file or in `anatomy.md`, which is what gets opened
   first.

4. **A rendered reference beats both.** The agent produced three unusable
   drafts from files alone. Opening the running interface changed the diagnosis
   in one screenshot: the system reads as an *instrument* — near-black ground,
   monospace throughout, gold hairlines above and below every panel, hex
   badges, a full-width status bar. None of that is deducible from a token
   list. Consider shipping annotated screenshots as a first-class artifact.

## Addendum, same day: looking is not measuring

Point 4 above stops one rung short. The rendered reference *was* opened, and the
next draft was rejected too. Four went that way. The one that worked was not
written from a screenshot — it was written from `getComputedStyle` run against
the live page.

    measured  >  rendered  >  tokens  >  source

A screenshot carries intent and hides mechanism. It shows a control sitting in a
bar; it does not distinguish a raised chip with a pale border from a recess cut
into a tray — and that distinction is the whole style. Reading the reference's
stylesheet does not settle it either: a stylesheet does not say which rule won
the cascade, what a shorthand actually set, or what a pseudo-element composes at
paint time. Only the computed value says what the browser did.

```js
const c = getComputedStyle(el, '::after');   // pseudo-elements too — that is
                                             // where the grain usually lives
```

**Never truncate a composite while measuring.** In this run the probe cut
`box-shadow` at 220 characters, which dropped the tail — an amber glow on the
tray's bottom groove. A whole iteration shipped without it, and the omission read
as a flat bar. Composite properties carry their meaning at the end as readily as
at the front: `box-shadow`, `background`, `transition`, `grid-template`. Log them
whole or do not log them.

What to lift, in this order: radii (all four, per element), border width **and
colour**, the full `box-shadow`, `background-image` on the element and on both
pseudo-elements, then font stack, size, weight and letter-spacing.

### Checklist, answered by looking at the rendered page

Each of these is settled by the page, not by the code that produced it.

- Is every control a **hole** in its container, or a **chip** on top of it?
- Does the casing sit **lighter** than the surface it holds?
- Do the radii of the top, middle and bottom members **differ**, and in a
  direction that reads as one shell closing?
- Is the gold **solid**, and is the only fading thing the accent rail?
- Does any strip **overhang** a curve it is supposed to follow? (rule 17)
- Is the drop shadow **tinted** with the family hue rather than black?

## Proposed addition to the anatomy: the specification plate

Absent from `anatomy.md`, present on three of four physical references
(the Game Boy blueprint's spec block, `MODEL NO. SNS-005` on the back of the
SNES controller, the silkscreen at the foot of the SNES shell).

What no other part covers: **a place where the object declares what it is.**
Every other element shows a *state* — diodes, counters, slots. This one states
an *identity*: model, revision, provenance. On an interface it is where a
version number, the source of a figure, or the date of a computation belong.

```css
.plate {
  border-top: 1px solid var(--rm-rule-2-dark);
  padding-top: 10px;
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 3px 14px;
  font: 600 9px/1.4 var(--rm-font-label);
  letter-spacing: .12em;
  text-transform: uppercase;
}
.plate dt { color: var(--rm-ink-faint); }
.plate dd { color: var(--rm-ink-muted); margin: 0; }
```

Rules: monospace only, two ink levels and no more, a 1px rule above and nothing
else. **No tonic colour** — an engraved plate is not an indicator light.
