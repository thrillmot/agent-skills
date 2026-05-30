---
name: dtcg-format
description: |
  Use when authoring, validating, or transforming W3C Design Tokens Format (DTCG) JSON. Names the `$type` / `$value` / `$extensions` separation, alias-reference syntax (`{path.to.token}`), group inheritance for shared `$type` / `$description`, composite token shapes (shadow, typography, transition), and the UDTS-specific `$extensions.udts` schema (catalog-level `mode-axis` + token-level `class` / `kind` / `stop` / `mode-behavior` / `valid-pair-rule`). Cite when an agent proposes bare `value` / `type` keys without `$`, invents a top-level `$modes` key (themes/modes aren't in DTCG draft), or omits the `$extensions.udts` block on a UDTS-conformant catalog.
---

# DTCG format

W3C Design Tokens Community Group format (DTCG) is the canonical interchange format for design tokens. UDTS exports DTCG as its source of truth; every other emitted format (CSS variables, Tailwind, TypeScript, iOS, Android, Flutter) derives from a DTCG snapshot.

The DTCG spec is in draft (currently 2025.10) — practitioner adoption is wide enough that breaking changes face strong pushback, but the format is a Community Group product, not a W3C Recommendation.

## When to use

- Authoring a new DTCG file from scratch.
- Migrating an existing token system to DTCG.
- Validating a DTCG file against the spec.
- Transforming DTCG into another format (Style Dictionary, CSS variables, etc.).
- Code review on PRs that change DTCG files.

## When NOT to use

- Quick prototypes / single-file tokens that won't be exported across tools. The DTCG ceremony isn't earned at that scope.
- Tools that have their own native format (Tailwind config, native CSS variables in a one-product codebase). DTCG is the *interchange* format; you don't have to author in it.

## The reserved-key separation

Every DTCG token is an object with reserved `$`-prefixed keys:

| Key | Required? | Meaning |
|---|---|---|
| `$value` | yes (on tokens; groups omit it) | The token's data — a primitive value or an alias reference |
| `$type` | yes on leaves, optional on groups | The semantic type (`color`, `dimension`, `fontFamily`, `duration`, `shadow`, `typography`, `transition`, `cubicBezier`, etc.) |
| `$description` | no | Free-form description; carried into doc generation |
| `$extensions` | no | Namespaced bag for tooling-specific metadata. UDTS lives at `$extensions.udts` |

Bare keys (no `$` prefix) are **group children**, not properties. A token named `value: "#ff0000"` (no `$`) is a child token named `value`, not a token with a value of `#ff0000`. Validators that don't strictly enforce this silently accept it and produce empty output.

```json
{
  "color": {
    "primary": {
      "500": {
        "$type": "color",
        "$value": "#1d4ed8",
        "$description": "Primary brand color"
      }
    }
  }
}
```

## Aliases

Reference another token's `$value` with `{path.to.token}` syntax. Aliases inherit `$type` from the target — **do not redeclare it**:

```json
{
  "color": {
    "primary": { "500": { "$type": "color", "$value": "#1d4ed8" } },
    "action":  { "default": { "$value": "{color.primary.500}" } }
  }
}
```

Aliases resolve transitively. **Alias cycles aren't caught by the spec** — add a resolver check in CI.

## Groups + inheritance

Any object without a `$value` is a group. Groups carry `$type` and `$description` that children inherit — so you can drop `$type` on every leaf in a ramp:

```json
{
  "color": {
    "$type": "color",
    "$description": "Core palette",
    "neutral": {
      "100": { "$value": "#f5f5f5" },
      "900": { "$value": "#171717" }
    }
  }
}
```

## Composite tokens

DTCG defines composite types where `$value` is an object, not a primitive:

```json
{
  "shadow": {
    "md": {
      "$type": "shadow",
      "$value": {
        "color": "{color.neutral.900}",
        "offsetX": "0px",
        "offsetY": "2px",
        "blur": "4px",
        "spread": "0px"
      }
    }
  },
  "typography": {
    "body-md": {
      "$type": "typography",
      "$value": {
        "fontFamily": "{font.sans}",
        "fontSize": "{font.size.md}",
        "fontWeight": "{font.weight.regular}",
        "lineHeight": "{font.lh.body-md}",
        "letterSpacing": "0"
      }
    }
  }
}
```

Composite tokens can alias individual sub-values — useful for systems that share a font family across many typography tokens.

## The UDTS `$extensions.udts` schema

UDTS extends DTCG via `$extensions.udts`. Tooling that doesn't understand UDTS ignores the namespace; UDTS-aware tooling (the linter, the Tokenomics CLI, AI generators) reads it.

### Catalog-level

```json
{
  "$extensions": {
    "udts": {
      "version": "1.2.3",
      "spec": "1.0",
      "mode-axis": {
        "modes": ["light", "dark"],
        "sources": ["auto", "forced"],
        "default-mode": "light",
        "states": ["auto-light", "auto-dark", "forced-light", "forced-dark"]
      },
      "stop-ladder": [50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950],
      "max-stop": 1000
    }
  }
}
```

`mode-axis.modes` is open-ended; catalogs extend with `hc-light`, `hc-dark`, `sepia`, tenant-themed modes.

### Token-level (contrast-bound)

```json
"$extensions": {
  "udts": {
    "class": "contrast-bound",
    "kind": "text",
    "stop": { "light": 900, "dark": 100 },
    "mode-behavior": "varies",
    "apca-target": 90,
    "valid-pair-rule": "stop-distance >= 700 OR pair-apca-lc >= apca-target",
    "min-font-size-px": 14,
    "pairing-background-kind": "surface"
  }
}
```

### Token-level (free)

```json
"$extensions": {
  "udts": {
    "class": "free",
    "kind": "brand-spot",
    "mode-behavior": "fixed"
  }
}
```

Free tokens deliberately omit `apca-target`, `valid-pair-rule`, `stop`, and `pairing-background-kind` — they carry no pairing obligation.

## Common pitfalls

1. **Bare `value` / `type` keys.** Legacy systems use unprefixed names; DTCG ignores them as group children. Codemod before migration.
2. **Top-level `$modes` key.** Multi-mode theming isn't in the DTCG draft. UDTS puts modes in `$extensions.udts.mode-axis`; other approaches (Style Dictionary's `$extensions["studio.tokens"].modes`, Tokens Studio's sets, per-mode files) are also valid. Pick one explicitly.
3. **Alias cycles.** Not caught by the spec; CI must verify.
4. **Composite-token sub-aliasing with wrong type.** `shadow.color` must reference a color token, not a dimension. Validators flag this; if your validator doesn't, your runtime will.
5. **`$type` repeated on leaves under a typed group.** Redundant but not wrong; clean DTCG omits it.

## Cross-references

- **REQUIRED BACKGROUND:** `design-token-naming` — the prefix conventions that drive the `$extensions.udts.class` and `$extensions.udts.kind` fields.
- **For the version-bump policy when DTCG files change:** `semver-design-tokens`.

## Verification

For each DTCG file:

1. **Reserved-key discipline:** every leaf is an object with `$value`; every group has children with `$value`.
2. **Alias resolution:** every `{path}` reference resolves to an existing token.
3. **Type inheritance:** leaves under a typed group don't redeclare `$type`.
4. **No cycles:** alias chains terminate.
5. **`$extensions.udts` present** on UDTS-conformant catalogs at the catalog level and every token.
6. **Class-prefix match:** every token's prefix matches its `$extensions.udts.class`.

## Sources

- [W3C Design Tokens Format Module](https://tr.designtokens.org/format/) — the spec.
- [Design Tokens Community Group](https://www.designtokens.org/) — group home + discussion archive.
- [Style Dictionary](https://styledictionary.com/) — the reference transformer pipeline.
- The UDTS / token.design integration spec at `docs/integrations/code-exports.md`.
