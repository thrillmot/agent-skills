---
name: wcag-contrast
description: Use when verifying a color pair meets WCAG 2.2 AA contrast requirements as a cross-check on APCA-driven generation, or when auditing a token catalog or design for legal-baseline accessibility compliance. Names the 4.5:1 normal-text rule, the 3:1 large-text rule, the size threshold for "large" (≥ 18 px or ≥ 14 px bold), the SC 1.4.11 non-text rule, and the role this check plays alongside APCA. Cite when an agent treats WCAG as the *primary* contrast model — UDTS uses it as a cross-check, with APCA as primary.
---

# WCAG 2.2 AA contrast

WCAG 2.2 Level AA is the **legal-baseline** contrast standard for most jurisdictions until WCAG 3 ships (≥ 2029). UDTS uses it as a cross-check alongside its primary model, APCA. Every UDTS-emitted contrast-bound token passes both.

## When to use

- Verifying a foreground / background pair after APCA composition.
- Auditing an existing token catalog or design for legal-baseline compliance.
- Code review on PRs that change color tokens, hover states, or focus indicators.

## When NOT to use

- As the *primary* contrast model when generating new colors. APCACH inverse composition against an APCA Lc target is the generator; this is the cross-check.
- For free-class tokens (illustration, decorative, brand-spot) — they carry no pairing obligation. See `apca-contrast` for the class system.

## The rules

| SC | Rule | Threshold | Applies to |
|---|---|---|---|
| 1.4.3 | Normal text | **4.5:1** | Text < 18 px regular OR < 14 px bold |
| 1.4.3 | Large text | **3:1** | Text ≥ 18 px regular OR ≥ 14 px bold |
| 1.4.11 | Non-text contrast | **3:1** | UI components, focus indicators, graphical objects |
| 2.4.13 | Focus appearance (AAA only) | Specific minimums | New in 2.2 — verify ring is ≥ 2 px and meets 3:1 |

The bold-text threshold is **14 px bold**, not 18 px bold — a common miss. The 18 px threshold is for regular weight only.

## Computing the ratio

```
L = 0.2126·R_lin + 0.7152·G_lin + 0.0722·B_lin   (sRGB-linearized)
ratio = (L_lighter + 0.05) / (L_darker + 0.05)
```

Always linearize the RGB channels before computing luminance (apply the sRGB gamma reversal: `c <= 0.04045 ? c/12.92 : ((c + 0.055)/1.055)^2.4`). Skipping linearization gives wrong answers — usually undercounting contrast for mid-tones.

For OKLCH-source colors, gamut-map to sRGB first (clamp out-of-gamut chroma at the (L, H) boundary), then apply the formula.

## When WCAG and APCA disagree

WCAG's relative-luminance model over-reports contrast for dark colors and under-reports for very light ones, so APCA and WCAG diverge at the extremes (very dark + very dark, very light + very light, very saturated mid-tones). UDTS finds the safe middle: a token passes only if **both** models pass.

In practice:

- **WCAG passes, APCA fails:** the pairing meets the legal threshold but is *perceptually* hard to read. Reject. (Most common in the dark-on-dark range.)
- **APCA passes, WCAG fails:** the pairing reads fine perceptually but doesn't meet the legal baseline. Reject. (Most common in the very-light range.)
- **Both pass:** ship.

## Verification

For each contrast-bound pairing:

1. Compute the WCAG ratio with sRGB-linearized luminance.
2. Apply the role-appropriate threshold (4.5:1 normal text, 3:1 large text or non-text).
3. Confirm the bold-text size rule — 14 px bold counts as large; 14 px regular does not.
4. Cross-check the APCA Lc (see `apca-contrast`).
5. Reject if either model fails.

## Cross-references

- **REQUIRED BACKGROUND:** `apca-contrast` — the primary contrast model. WCAG is the cross-check; APCA is the generator target.
- **For the underlying color space:** `oklch-color-space` — OKLCH source values must be gamut-mapped to sRGB before applying the WCAG formula.

## Sources

- [WCAG 2.2 Recommendation — SC 1.4.3](https://www.w3.org/TR/WCAG22/#contrast-minimum) — text contrast.
- [SC 1.4.11 — non-text contrast](https://www.w3.org/TR/WCAG22/#non-text-contrast).
- [SC 2.4.13 — focus appearance (AAA, new in 2.2)](https://www.w3.org/TR/WCAG22/#focus-appearance).
- [Contrast Checker, WebAIM](https://webaim.org/resources/contrastchecker/) — sanity-check tool that matches the spec.
