# Retromorphism

**A UI that behaves like a device, not like paper.**

Not skeuomorphism — nothing here pretends to be leather or brushed steel. Not
neumorphism either: no soft extruded blobs, no shadow-only hierarchy. The
premise is narrower and older. A screen is a **panel set into a moulded shell**:
it has frames, notches, status lights and a bezel, and depth comes from
structure rather than from blur.

The values here are read out of two codebases that use them, rather than
picked for a palette. Where the two disagree, that is written down rather than
averaged away.

![Memoria — panels, grooves, phosphor screen](assets/memoria-constellation.png)

---

## The measured core

Thirteen values appear in both codebases at the same hex, reached separately
over several months. Eight in the light themes — and five more in the dark ones,
which is the stronger signal: dark palettes are where most systems drift apart.

**Light**

| Token | Value | NDEX | Memoria |
|---|---|---|---|
| `--rm-amber` | `#E2B84E` | `retro-accent` | `--amber` |
| `--rm-gold` | `#D4A84C` | `accent-gold` | `--kraft` |
| `--rm-violet-muted` | `#7C6B9E` | `accent-muted` | `--accent` |
| `--rm-alert` | `#FF6B6B` | `diode-red` | `--warn` |
| `--rm-ink` | `#2A2832` | `text-1` | `--text` |
| `--rm-ink-muted` | `#5A5370` | `text-3` | `--muted` |
| `--rm-ink-faint` | `#7C7890` | `text-2` | `--faint` |
| `--rm-rule` | `#A8A0BC` | `border-2` | `--line` |

**Dark**

| Token | Value | NDEX | Memoria |
|---|---|---|---|
| `--rm-bg-dark` | `#1E1D24` | `app-bg` | `--bg` |
| `--rm-rule-dark` | `#4F4764` | `border-1` | `--line` |
| `--rm-rule-2-dark` | `#3A3845` | `border-2` | `--line2` |
| `--rm-accent-dark` | `#9B74F2` | `retro-accent` | `--accent` |
| `--rm-ink-dark` | `rgba(255,255,255,.90)` | `text-1` | `--text` |

## The four principles

**Matter over shadow.** Depth is built from surface levels and 1px frames, not
from drop shadows. Four levels, and the inversion matters: dark shells sit
*inside* a light canvas, because the device is light and its screens are dark.

```
canvas #E3E0EA  ← the page
  s1   #D9D4E0  ← cards, panels
    s2 #36353A  ← dark shells: search, tabs, footer
      s3 #27262A ← grooves, dividers
```

**Two type registers, never three.** A grotesque to read; a monospace in small
caps with wide tracking (0.12–0.22em) to label. The monospace label is what
says *instrument* rather than *website*.

**Asymmetric radii by position.** A module on the left is not a module on the
right. `16px 8px 32px 16px` on the left, `8px` in the centre, mirrored on the
right — the shell is moulded, not tiled.

**Status before text.** Diodes (12px, 8px apart) carry state at a glance:
gold / red / blue / off. The label explains; the diode is read first.

## Two worlds, one frame

Both interfaces permute a **hue** per context and change nothing else — not the
radii, not the type, not the structure. That constraint is what makes the two
modes read as the same device:

| | Games | Pokémon |
|---|---|---|
| chrome | `#85799C → #513F75` | `#C62828 → #B03030` |
| accent | `#7C6B9E` | `#ED2222` |

| Games | Pokémon | Detail |
|---|---|---|
| ![](assets/ndex-games.png) | ![](assets/ndex-pokemon.png) | ![](assets/ndex-detail.png) |

## Anatomy

Six pieces — slot, module, diodes, moulded shell, identity zone, box variants —
each cropped from a running interface and followed by the recipe that produces
it. Then the five **motifs** that recur at every scale and make it a language
rather than a component list: the fading rail, the tint ladder, the double
frame, the bevel pair, asymmetric radii.
→ [docs/anatomy.md](docs/anatomy.md)

![](assets/part-slot-dark.png)
![](assets/part-module-dark.png)

## Dark

Both interfaces run dark, and dark is where the language is most itself: the
shell recedes, the screens carry the signal, the diodes do the talking.

| Memoria | NDEX · Pokémon | NDEX · Games |
|---|---|---|
| ![](assets/memoria-console-dark.png) | ![](assets/ndex-pokemon-dark.png) | ![](assets/ndex-games-dark.png) |

## Rules that cost something to learn

Sixteen of them, each with what was tried first and why it failed. →
[docs/rules.md](docs/rules.md)

**Cap the highlight.** A selected state never exceeds **1.4×** the resting
luminance, and nothing flashes. An element that pulses is looked at once and
then filtered out permanently.

**Contrast by warmth, not by brightness.** Making an element lighter to detach
it turns it into a sticker. Detach it with warm rim-light in the accent family
and keep it dark — three failed attempts confirmed this in a single evening.

**`letter-spacing` breaks centring.** SVG adds a gutter after *every* glyph,
including the last, so `text-anchor="middle"` lands half a gutter left. Uneven
tracking across lines makes a block look chipped rather than off-centre.
Compensate with `dx` equal to half the tracking, per line.

**Structural devices must encode something true.** Numbered markers only if the
content is a sequence. A groove only where two modules actually meet.

**Screens are the one place saturation is allowed.** Deep navy ground, phosphor
text. Everywhere else, saturation is a mistake.

## Files

```
tokens/core.json     the measured core, W3C design-tokens shape
tokens/themes.json   per-product, per-theme values
tokens/core.css      custom properties, generated — never hand-edited
docs/anatomy.md      the six pieces, each with its recipe
docs/rules.md        sixteen rules, each with the failure that produced it
docs/principles.md   what this is and is not
assets/              parts cropped from the running interfaces
```

## Take it

The tokens are MIT. Copy them, fork them, ship them in something of your own —
no permission needed and none expected back.

Anyone can screenshot an interface and rebuild its look; that has always been
true and tooling has only made it faster. So the useful thing to publish is not
the pixels but the **vocabulary**: named values, the reasons behind them, and a
date. What is worth keeping is the name and the origin, which is why both are
written down here rather than defended.

If you build something in this language, `retromorphism` is what to call it.

## Where this comes from

The name and the first written rules date from February 2026, in one of the two
codebases; the other reached the same core on its own. This repo is the
extraction, dated 2026-07-29: values were read from the source and from computed
styles, not retyped from memory.

Both codebases are private, so the tokens travel and the implementations do not.
That is the limit of what this can be, and it is stated here rather than
implied.

— Yyunozor, 2026-07-29
