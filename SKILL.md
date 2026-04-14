---
name: gradients
description: Generate and refine gradients with a preset-first workflow grounded in the wrap-gradient palette system. Use when asked to create a gradient, pick a gradient for a UI/background/poster, translate a mood or color family into a gradient, or convert gradients to perceptual CSS using oklab/oklch. Prefer the bundled presets by family before inventing new stops.
---

# Gradient Workflow

Default to perceptual interpolation.

- Use `linear-gradient(in oklab, ...)` for gradients with 3 or more stops.
- Use `linear-gradient(in oklch, ...)` for 2-stop gradients.
- Keep stop positions explicit when the source preset already defines them.
- Output plain CSS unless the user asks for another format.

## Gradient Direction

When outputting gradients, consider the direction based on the container and color brightness:

- **Horizontal containers** (width > height): Use horizontal gradient (`90deg` or `to right`)
- **Vertical containers** (height > width): Use vertical gradient, with **dark colors at the top and light colors at the bottom** (`180deg` or `to bottom`)
  - If the preset has light colors first and dark colors last, reverse the gradient direction (`0deg` or `to top`)
  - This follows natural lighting patterns and creates more stable, comfortable visuals
- **Square containers** (width ≈ height): Use diagonal gradient (`135deg` or `to bottom right`)

When the user doesn't specify a direction, default to horizontal (`90deg`) for CSS output.

## Platform Support

This skill supports multiple output formats:

- **CSS** (default): `linear-gradient(in oklab, ...)` or `linear-gradient(in oklch, ...)`
- **Swift/Metal**: `Gradient.Stop(color: Color(red: ..., green: ..., blue: ...), location: ...)`
- **Kotlin/Android**: `intArrayOf(Color.rgb(...), Color.rgb(...))` with positions
- **Flutter**: `LinearGradient(colors: [Color(0xFF...), Color(0xFF...)], stops: [...])`
- **Canvas API**: `ctx.createLinearGradient(...)` with `addColorStop(...)`

When the user mentions a specific platform (e.g., "for Swift", "in Flutter"), output the appropriate format.

### Color Space Fitting

When outputting to platforms that don't support oklab/oklch natively (e.g., Swift, Kotlin, Flutter), the skill will:

1. Use the wrap-gradient color space conversion utilities to interpolate colors in oklab/oklch
2. Sample the gradient at multiple points (e.g., 10-20 stops)
3. Convert each sampled color to RGB/hex
4. Output the platform-specific format with the sampled stops

This ensures perceptual smoothness even on platforms without native oklab/oklch support.

## Preset-first selection

Start from a preset family before making a custom gradient.

- `RedYellow`: warm, sunrise, peach, coral, orange, sunset
- `BluePurple`: cool, dusk, dreamy, cosmic, violet, night-sky
- `GreenYellow`: spring, mint, meadow, lake, fresh, teal-green
- `Contrast`: loud, editorial, colorful, statement, high-contrast
- `Dark`: moody, luxury, cinematic, deep dark backgrounds
- `Light`: airy, pastel, soft product backgrounds, gentle hero sections

If the request clearly matches one of those families, read `references/presets.md` and pick 2-4 close candidates.
If the request is for a simple 2-color gradient, also read `references/oklch-two-stop-presets.md`.

## Adaptation rules

When adapting a preset:

- Preserve the family mood before changing individual stops.
- Prefer nudging lightness/chroma over replacing every hue.
- Keep multi-stop gradients multi-stop unless the user asks to simplify.
- If the user gives exact brand colors, use those colors but keep `oklab`/`oklch` interpolation.
- If a preset is close but not exact, say which preset you started from.

## Output pattern

Provide a compact answer in this order:

1. Best-match preset or family
2. Final gradient code (CSS by default, or platform-specific if requested)
3. Optional 1-line note about why it fits

Example (CSS):

```css
background: linear-gradient(in oklab, #DDDFEE 0%, #DAA1AF 42.3%, #5572B6 79.8%, #7C62A5 100%);
```

Example (Swift):

```swift
Gradient(stops: [
  Gradient.Stop(color: Color(red: 0.867, green: 0.875, blue: 0.933), location: 0.00),
  Gradient.Stop(color: Color(red: 0.855, green: 0.631, blue: 0.686), location: 0.423),
  Gradient.Stop(color: Color(red: 0.333, green: 0.447, blue: 0.714), location: 0.798),
  Gradient.Stop(color: Color(red: 0.486, green: 0.384, blue: 0.647), location: 1.00)
])
```

## Reference files

- Read `references/presets.md` for the full wrap-gradient preset catalog grouped by family.
- Read `references/oklch-two-stop-presets.md` for 2-stop pairings that should stay in `oklch`.
