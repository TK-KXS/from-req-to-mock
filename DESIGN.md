# DESIGN.md - Mockup design system

Photo-first, quiet warm-cream UI. One red for the main action; everything else is black, white and warm grey.

## Tokens (paste into the mockup)

```css
:root {
  /* colors */
  --primary: #e60023;        /* main action only */
  --primary-pressed: #cc001f;
  --ink: #000000;            /* headings, button text */
  --body: #33332e;           /* paragraph text */
  --mute: #62625b;           /* captions, meta */
  --ash: #91918c;            /* placeholder, disabled */
  --hairline: #dadad3;       /* 1px dividers */
  --canvas: #ffffff;         /* page, nav, modal */
  --surface: #f6f6f3;        /* cards, search, chips */
  --secondary: #e5e5e0;      /* secondary button */
  --dark: #262622;           /* dark strip */
  --focus: #435ee5;          /* focus ring */
  --error: #9e0a0a;
  --success: #103c25;  --success-bg: #c7f0da;
  /* shape */
  --r-md: 16px;  --r-lg: 32px;  --r-full: 9999px;
  /* spacing */
  --s1: 4px; --s2: 8px; --s3: 12px; --s4: 16px; --s5: 24px; --s6: 32px; --s7: 64px;
  /* font */
  --font: Inter, -apple-system, system-ui, "Segoe UI", Roboto, Tahoma, sans-serif;
}
```

## Type

| Use | Size / weight | Note |
|---|---|---|
| Hero title | 44px / 700 | letter-spacing -0.8px |
| Page title | 28px / 700 | letter-spacing -1.2px |
| Section title | 22px / 600 | |
| Card title | 18px / 600 | |
| Body | 16px / 400 | line-height 1.4 |
| Small / meta | 14px / 400 | color `--mute` |
| Button | 14px / 700 | |
| Caption | 12px / 500 | |

## Components

| Component | Style |
|---|---|
| Primary button | bg `--primary`, white text, radius `--r-md`, height 40px, padding 6px 14px |
| Secondary button | bg `--secondary`, `--ink` text, same size |
| Icon button | 40px circle, bg `--surface` |
| Input | bg white, 1px `--ash` border, radius `--r-md`, height 44px; focus: 2px `--ink` + 4px `--focus` outline |
| Search bar | bg `--surface`, radius `--r-full`, height 48px |
| Chip | bg `--surface`, radius `--r-full`, padding 8px 16px; active: bg `--ink`, white text |
| Card | bg `--surface`, radius `--r-md`, no shadow, no border |
| Table | white, 1px `--hairline` row lines, header 14px / 700 |
| Top nav | white, height 64px, 1px `--hairline` bottom |
| Modal | white, radius `--r-lg`, padding 32px, 50% black scrim behind |
| Badge | radius `--r-full`, caption text; success: `--success-bg` / `--success` |

## Rules

- Red (`--primary`) only on the main action - at most one per screen area.
- Radius: 16px for buttons, inputs, cards; 32px for modals and big cards; pill for search, chips, avatars. Nothing sharp, nothing in between.
- No shadows (except under a modal). No gradients.
- Hierarchy by size and weight, not by color.
- Spacing: 64px between sections, 8px gaps in grids, 16-24px inside cards.
- Responsive: grid 4 cols desktop -> 2 tablet (768px) -> 1 phone (480px); nav becomes a menu icon on phone; section gap 64 -> 32px.
- Touch targets at least 44px.
