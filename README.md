# gradients

A portable AI skill for generating perceptual gradients with a preset-first workflow based on the [`wrap-gradient`](https://github.com/BIAsia/wrap-gradient) palette library.

It is designed to work as a generic skill for agents such as Codex, Claude Code, and OpenClaw.

## What it does

- prefers curated presets before inventing new gradients
- maps requests to color families such as `RedYellow`, `BluePurple`, `Dark`, and `Light`
- defaults to perceptual CSS interpolation
- uses `oklab` for 3+ stop gradients
- uses `oklch` for 2-stop gradients
- **supports multiple platforms**: CSS, Swift/Metal, Kotlin/Android, Flutter, Canvas API
- **fits oklch/oklab to RGB** when needed for platforms without native support

## Platform Support

This skill can output gradients in multiple formats:

- **CSS** (default): `linear-gradient(in oklab, ...)` or `linear-gradient(in oklch, ...)`
- **Swift/Metal**: `Gradient.Stop(color: Color(red: ..., green: ..., blue: ...), location: ...)`
- **Kotlin/Android**: `intArrayOf(Color.rgb(...), Color.rgb(...))` with positions
- **Flutter**: `LinearGradient(colors: [Color(0xFF...), Color(0xFF...)], stops: [...])`
- **Canvas API**: `ctx.createLinearGradient(...)` with `addColorStop(...)`

When outputting to platforms that don't support oklab/oklch natively, the skill uses the wrap-gradient color space conversion utilities to:

1. Interpolate colors in oklab/oklch
2. Sample the gradient at multiple points
3. Convert each sampled color to RGB/hex
4. Output the platform-specific format

This ensures perceptual smoothness even on platforms without native oklab/oklch support.

## Install

### Install from local directory

```bash
npx skills add ~/.openclaw/workspace/skills/gradients -y -g
```

### Install from GitHub repo (after publishing)

```bash
npx skills add biasia-sudo/gradients-skill -y -g
```

## Usage

Ask your agent things like:

- `make me a soft pastel hero gradient`
- `give me a dark cinematic gradient for a landing page`
- `convert this to an oklab gradient`
- `pick a blue-purple gradient preset for a poster`
- `make a 2-color gradient in oklch using mint and coral`
- `give me a Swift gradient for iOS`
- `create a Flutter gradient with warm colors`
- `make an Android gradient for a sunset theme`

## Skill behavior

The skill uses this workflow:

1. choose the closest preset family first
2. pick a close preset from the bundled references
3. adapt the preset only if needed
4. output compact code in the requested format (CSS by default)

## Repository layout

```text
.
├── SKILL.md
├── README.md
└── references/
    ├── presets.md
    └── oklch-two-stop-presets.md
```

## Compatibility notes

- `SKILL.md` is at the repo root for maximum installer compatibility
- bundled references are relative to `SKILL.md`
- the skill is not tied to OpenClaw-specific tools
- the output format is standard CSS by default, but can be adapted to any platform
- color space conversion is based on wrap-gradient's utilities

## Credit

Preset source data comes from the `wrap-gradient` config set:

- <https://github.com/BIAsia/wrap-gradient/tree/main/config>

Color space conversion utilities are based on wrap-gradient's implementation:

- <https://github.com/BIAsia/wrap-gradient/blob/main/utils/color.ts>
