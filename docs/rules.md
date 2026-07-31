# Rules

Seventeen rules, each stated with the mechanism that makes it true. The incidents
that produced them are omitted; only what generalises is kept.

Ordered from the most structuring to the finest. Selectors are neutral names:
`.dark` is a dark-theme scope, `.mode-a` and `.mode-b` two colour variants of
one interface.

---

## Diagnosis

### 1. A defect of mechanism is not fixed by reducing the symptom

A rounded corner shared by two border sides of **different colours** is rendered
as a blend across the arc. Every corner therefore reads lighter than the
straight run beside it. Lowering the alpha of the weaker side shortens the fade;
it cannot remove it, because two colours still meet.

```css
/* one constant colour on all four sides — the corner blends a colour with itself */
border: 1px solid rgba(44, 35, 65, .92);
```

The general form: when a defect comes from a mechanism, only removing the
mechanism fixes it. An artifact that survives every viewport, theme and density
is structural, not an anti-aliasing accident — establish which you have before
choosing a fix.

### 2. When a crisp artifact appears, look first at what was added to hide something else

A sharp-edged rectangle is rarely emergent — something is painting it. The
usual cause is a patch: `box-shadow: 0 4px 0 0 <page-background>` under a
sticky bar, added to fill the hairline gaps that open during momentum scrolling.
A 4px shadow in the page colour *is* a 4px band.

```css
/* opaque only above the silhouette that needs backing, transparent below */
background: linear-gradient(to bottom, var(--app-bg) 30px, transparent 30px);
```

Audit what was added to stop something showing through, before investigating
what might be showing through.

### 3. A material fix must sweep every copy and every level of nesting

A copied recipe carries its bugs sideways, and a fix applied at one level of a
nesting stack does not travel downwards — retint a panel and its nested tiles
stay bright, so the same complaint returns one layer down.

Enumerate every copy of the recipe and every level that shares it, and fix them
in one pass. Or centralise so there is only one. Reacting to the level that was
reported guarantees a second report.

---

## Tokens and cascade

### 4. Never edit a shared token to fix one context

Feedback always arrives about one place. Editing the shared token satisfies that
report and silently changes every other consumer. Use a dedicated variable, left
undefined everywhere else, read through `var()` with the shared token as
fallback.

```css
.mode-b .figure { color: var(--mode-b-gold, var(--gold)); }
```

Every untouched scope is then *literally* unchanged, not probably unchanged. A
seam deliberately left undefined in one theme should say so, or the next reader
will complete it.

### 5. Never invent a value to fill a gap

Every invented colour is one more identity to keep in sync across themes and to
age. Search the family for an already-approved value and reuse it unchanged, at
the same alpha, so the palette does not grow.

One qualification: **an opaque value transposes across themes, a translucent one
does not.** A small opaque fill can reuse the same hex in light and dark; a wash
sensitive to page luminance keeps a per-theme alpha. Reuse the hex, re-derive
the alpha.

### 6. Never set one sub-property through a shorthand

A shorthand does not set what you name in it — it sets everything it governs and
resets the rest, at the specificity of its selector. Writing `border-top: 2px
solid #3E3C4A` to change a colour also re-sets the width, and can resurrect a
bug another rule had just fixed.

```css
.dark .tabbar { border-top-color: #3E3C4A; }   /* colour only */
```

This class of defect is expensive for one reason: it is invisible in the theme
where the fix was verified. Corollary — one rule is the single width-setter,
every other rule touches colour only.

### 7. Make mutually exclusive scopes exclusive by selector, not by specificity arithmetic

Two scopes that tie on specificity are resolved by cascade **order**, which
depends on the order stylesheets happen to load in. Exclude explicitly.

```css
.mode-b:not(.dark) .tabbar { /* … */ }
```

Specificity arithmetic is a calculation you redo every time either rule changes.
An explicit exclusion is a structural guarantee.

### 8. A composite property is not partially inherited

Routing colour through a custom property is a gift: the dark theme resolves it
automatically, with no duplicated literal and no future divergence. But that
inheritance is per-property, and a composite is all-or-nothing. A dark rule that
sets a background and a border but omits `box-shadow` keeps the light one
*entire* — including hairlines calibrated for a light surface.

Audit rule: for every `box-shadow`, `background`, `transition` and `border`,
list the scopes that override any part of the component and confirm each
restates it in full or inherits it deliberately.

---

## Colour

### 9. A tonic colour is never a text colour

Saturated accents fail as text on real light surfaces — measured here between
1.6:1 and 3.6:1. The failed instinct is to substitute a different colour, which
loses the identity and stops the palette being a palette.

Hold hue and saturation, reduce lightness until contrast clears.

```css
--gold-text:   #735816;
--green-text:  #22673A;
--blue-text:   #345B9D;
```

Two transferable points. **Measure against the darkest real surface, not against
white** — theoretical white is not a surface the interface uses, and the darkest
member of the light family fails first. And **the problem is the surface, not
the colour**: dark mode restores every tonic unchanged, so deriving one
"accessible" replacement for both themes discards a colour that was always fine
in half of them.

### 10. To remove an unwanted hue, remove the chroma and keep the luminance

Picking a neutral that looks right throws away contrast that was measured.
Changing only the chroma keeps the ratio true by construction — no
re-measurement, no possible accessibility regression.

A constraint worth borrowing alongside it: one chromatic accent per colour
world, everything else neutral.

### 11. A pure-white wash on a tinted canvas reads as a floating card

The standard frosted-glass recipe works on a neutral background and fails on a
tinted one. Lowering the alpha yields a paler white — still a foreign colour.
Build the wash **from the canvas hue**, then lower the alpha.

```css
.panel {
  background:
    linear-gradient(180deg, rgba(227,224,234,.45), rgba(227,224,234,.18)),
    #EAE7EE;                                   /* the canvas hex itself */
  border: 1px solid rgba(60,54,80,.10);
  box-shadow: inset 0 1px 0 rgba(255,255,255,.9), 0 8px 24px rgba(60,54,80,.08);
}
```

---

## Geometry

### 12. One stroke per seam, and cancel the other by width

Where a card sits flush on the bar below it, two 2px borders stack and read as a
4px line belonging to nothing. Only one survives — and *how* the other is
cancelled matters.

```css
.tabbar--docked { border-top-width: 0; }   /* not `border-top: none` */
```

In a multi-theme system each variant sets its own `border-top-color`.
Neutralising with `none` puts all those overrides in conflict, and they win
wherever they out-specify. Cancelling the **width** leaves them inert: they go
on painting a border zero pixels wide.

### 13. Two curves that meet flush must share a radius

With unequal radii the shorter arc has not reached the flat edge when the longer
one has, so a crescent of background shows at both ends of the seam. Equalise
the radius; do not fill the hole.

Align only the corners **in contact**. Aligning the others is symmetry for its
own sake. And where two strokes meet they need comparable weight — a 1px border
reads as faint next to a 2px one.

### 14. A tile nested in an already-framed panel takes no border

A framed tile inside a framed box gives two parallel strokes a few pixels apart.
Relief comes from material, not from a second frame.

```css
.tile {
  background: rgba(227,224,234,.55);   /* above the parent wash, still not white */
  border: none;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.95);
}
```

The containing level sets the value; a tile is not styled in isolation.

### 15. A selected state is a diffuse illumination filling the notch, top and bottom

A flat wash composites into a muddy flood on a dark surface. A single underline
is a different signal entirely.

```css
.tab.active {
  box-shadow:
    inset 0  1px 0    rgba(255,255,255,.35),
    inset 0 -1px 0    rgba(255,255,255,.25),
    inset 0  5px 6px -4px var(--accent-glow),
    inset 0 -5px 6px -4px var(--accent-glow);
}
```

Four constraints generalise. **Top and bottom, never one side.** **Inset only** —
a structural guarantee the light cannot spill outside the component, rather than
a tuned one. **Transitioned**, so the state fades instead of jumping; no
transform, no scale, the element must not move. And the geometry — offsets,
blur, spread — stays identical across variants and themes: only the colour
changes, through a token that resolves itself.

### 16. A percentage-based fade mask does not transpose across widths

What stays constant to the eye is the *perceived length* of a fade, not its
ratio. 6% of 1000px is a 60px gradient; 6% of 90px is 5px, which reads as no
fade at all. Recalibrate the mask per component family.

```css
.topline {
  left: 14%; right: 14%;                       /* narrower element, wider margin */
  mask: linear-gradient(90deg, transparent, #000 25%, #000 75%, transparent);
}
```

A line that belongs to an edge should sit **on** that edge (`top: -1px`), not
float in the padding above it.

Where a value has been calibrated by eye across a family, the calibration is the
asset — an unmotivated tidy-up destroys it silently.

### 17. A box too short to carry the family radius must leave the curve, not equalise it

Rule 13 says two curves meeting flush share a radius. It does not say what to do
when one of them *cannot hold* the shared value.

A 4px-tall strip lying on the bottom lip of a 22px-radius shell, given
`border-radius: 0 0 22px 22px`, does not get 22px corners. When a radius exceeds
the box that carries it, the browser scales **all four** radii by the same factor
until they fit — here to roughly 4px. The strip keeps near-square corners while
the shell curves away beneath it, and overhangs the curve at both ends. Raising
the strip's radius cannot fix this; the clamp recomputes from the same height.

```css
/* the strip lives in the FLAT run, stopping at the radius value */
left: 24px; right: 24px;          /* 24px = the shell's bottom radius */
border-radius: 2px;               /* its own ends, not the shell's */
```

The general form: a family value is only inheritable by members large enough to
express it. Where a member cannot, withdraw it from the shared geometry rather
than assigning a value the renderer will silently rewrite. A moulded vent groove
does the same thing physically — it never runs into the corner of the mould.

Corollary for review: **a radius that exceeds its own box is not a value, it is a
request.** Read back what was computed, never what was written.
