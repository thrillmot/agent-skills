---
name: spacing-system
description: Use when designing or auditing a spacing scale for padding, gaps, icon sizes, component heights, or border radii. Names the two-unit primitive model UDTS uses (minor + major, where major is divisible by minor — typically 4+8, 2+4, or 4+16), the derivation rule (padding / gap / icon / height ladders are all derived from the unit primitive, not independently invented), the 24 CSS px WCAG 2.5.5 AA Pointer Target floor for interactive heights, and the T-shirt-vs-numeric naming options (UDTS supports both). Cite when an agent proposes a single-unit grid for a mixed-density system, or invents off-grid spacing values for "this one specific case."
---

# Spacing system

Spacing in a token-driven design system is a **two-unit primitive** problem, not a one-number guess. UDTS uses a **minor unit** (the smallest legal increment) and a **major unit** (the dominant rhythm), with the constraint that the major divides cleanly by the minor. Everything downstream — padding, gap, icon size, component height, border radius — derives from those two numbers.

## When to use

- Designing a new spacing scale from a density brief.
- Auditing an existing scale with one-off off-grid values.
- Picking unit primitives for a density mode (dense / balanced / spacious).
- Code review: flag raw px values for `padding` / `gap` / `margin` outside the system's declared scale.

## When NOT to use

- One-off marketing surfaces where the layout is hand-tuned and not part of the system's grid.
- Print-design contexts where the grid math is pt-based and a different system applies.

## The two-unit primitive model

UDTS declares two unit primitives per foundation theme:

| Slot | Typical value | Role |
|---|---|---|
| **minor** | 4, 2, 4 | Smallest legal increment — used for tight inline gaps, sub-pixel-but-aligned spacing |
| **major** | 8, 4, 16 | Dominant rhythm — used for padding, gap, vertical stack rhythm |

Constraint: `major mod minor == 0`. Both default to 4 when not split (single-unit systems collapse to `minor == major`).

| Density mode | minor | major | Used for |
|---|---|---|---|
| Dense | 2 | 4 | Admin consoles, data-heavy tools, compact tables |
| **Balanced** (UDTS default) | 4 | 8 | Most product UI |
| Spacious | 4 | 16 | Marketing surfaces, editorial content, accessibility-first systems |

## What derives from the unit primitives

Every secondary scale derives from the two units. Designers and developers don't invent these — the system generates them.

### Padding ladder

Generated from the major unit, with minor steps for tight cases:

```
padding-0       = 0
padding-2xs     = minor                  (e.g. 4)
padding-xs      = major                  (e.g. 8)
padding-sm      = 1.5 × major            (e.g. 12)
padding-md      = 2 × major              (e.g. 16)
padding-lg      = 3 × major              (e.g. 24)
padding-xl      = 4 × major              (e.g. 32)
padding-2xl     = 6 × major              (e.g. 48)
padding-3xl     = 8 × major              (e.g. 64)
```

### Gap ladder

Same ladder as padding, separately labeled (`gap-*`) because horizontal and vertical gaps may diverge in some themes. The major-unit base ensures stacked rhythm preserves baselines.

### Icon-size ladder

**Curated**, not formula-derived. UDTS's default icon sizes are `12, 16, 24, 32, 40, 48` — these are the sizes where rendered glyphs (Material, Lucide, Heroicons families) hit pixel boundaries cleanly. Formula-derived sizes (e.g. 14, 18, 22) produce hairline mis-renders on a lot of icon families. See `component-sizing` for the rule that pairs an icon size with a control height.

### Component-height ladder

Curated per density mode, with the **24 CSS px WCAG 2.5.5 AA Pointer Target floor** for interactive controls (anything clickable / tappable / focusable). See `component-sizing` for the per-density ladder.

### Border-radius ladder

```
radius-0     = 0
radius-sm    = minor                     (e.g. 4)
radius-md    = major / 2                 (e.g. 4)        — same as sm at balanced; diverges at dense
radius-lg    = major                     (e.g. 8)
radius-xl    = 1.5 × major               (e.g. 12)
radius-2xl   = 2 × major                 (e.g. 16)
radius-pill  = 9999                      (infinite — for fully-rounded)
radius-circle = 50%                      (relative — for circular elements)
```

## Naming: T-shirt OR numeric (UDTS supports both)

UDTS emits **both** sets of names; consumers pick the family that fits their codebase convention:

| T-shirt | Numeric | px (balanced) |
|---|---|---|
| `padding-2xs` | `space-1` | 4 |
| `padding-xs` | `space-2` | 8 |
| `padding-sm` | `space-3` | 12 |
| `padding-md` | `space-4` | 16 |
| `padding-lg` | `space-5` | 24 |
| `padding-xl` | `space-6` | 32 |
| `padding-2xl` | `space-7` | 48 |
| `padding-3xl` | `space-8` | 64 |

Both are emitted in DTCG with cross-aliases. Pick the family that fits your codebase, not "the correct one" — there isn't one.

## The WCAG 2.5.5 AA Pointer Target floor

Any interactive control's minimum **height** is **24 CSS px** for AA conformance. The `xs` rung in the component-height ladder is reserved for non-interactive elements (badges, read-only chips, density tags). Anything clickable starts at the `sm` rung — typically 32 px in balanced density.

Failing this is one of the most common WCAG 2.2 misses in design systems. The skill exists partly to make the rule load-bearing.

## Cross-references

- **REQUIRED BACKGROUND for height + font + icon pairing:** `component-sizing` — the curated per-density component-height ladder + font-pairing + icon-size pairing.
- **For the typography scale that pairs with spacing:** `type-scale` and `line-height-grid` — line-heights snap to the minor unit declared here.

## Verification

After picking unit primitives + emitting ladders:

1. **Divisibility:** `major mod minor == 0`. If not, the system is malformed.
2. **Grid alignment:** every padding, gap, and component-height token is a multiple of the minor unit.
3. **Pointer target:** every interactive component-height rung is ≥ 24 CSS px.
4. **Naming sync:** if both T-shirt and numeric families are emitted, every value has a matching pair in both. No orphans.
5. **Icon-size curation:** icon sizes match the curated set (12, 16, 24, 32, 40, 48), not a formula output.

## Sources

- [WCAG 2.5.5 — Target Size (AA, added in 2.2)](https://www.w3.org/TR/WCAG22/#target-size-minimum) — the 24 CSS px floor.
- [Material Design's icon sizing](https://m3.material.io/styles/icons) — informs the curated icon set.
- The UDTS / token.design spacing-and-sizing foundations spec.
