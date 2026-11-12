# Anchor Design System — Preview

Web visual specification for the **Anchor DS** (Cardioline). Modeled on **Beat DS 2** (shadcn/ui). Production target is **WPF/XAML** on top of **MaterialDesignThemes.Wpf**.

> **This folder is a specification, not the production DS.** It renders in a browser so humans and AIs can agree on what the DS *looks like* before the WPF implementation is written.

---

## Quick start

```bash
cd DS-Preview
python -m http.server 8765
```

Open **http://localhost:8765/**. Toggle dark/light from the top-right.

---

## What's here

| File | Purpose |
|---|---|
| [`index.html`](./index.html) | Showcase page — all components in all states. |
| [`tokens.css`](./tokens.css) | CSS variables for both themes (dark primary). Generated from `tokens/tokens.json`. |
| [`styles.css`](./styles.css) | Component styles — pure CSS, no framework. |
| [`tokens/tokens.json`](./tokens/tokens.json) | **Machine-readable token source of truth.** Each token has light + dark values and a planned WPF resource key. |
| [`components.json`](./components.json) | **Machine-readable component manifest.** Each component has shadcn name, variants, MDIX base, WPF key, Figma status, usage rules. |
| [`AGENTS.md`](./AGENTS.md) | Instructions for AI assistants working here. Read first. |

---

## Design principles

1. **Shadcn-shaped.** Token names and component variants match shadcn/ui exactly (`--primary`, `Button variant="ghost"`, etc.) so any AI that knows shadcn is instantly productive.
2. **Beat 2 is the visual reference.** Dark theme first, orange brand accent, HSL + oklch palette.
3. **Figma is the scope.** Components not present in the Anchor Figma are not in the DS, even if shadcn has them.
4. **Production = WPF + MDIX.** The web preview is translated into a ResourceDictionary pack that overrides MDIX tokens and provides Beat 2–shaped styles. MDIX is not replaced.

---

## Theming

Themes are swapped via `[data-theme="dark"]` / `[data-theme="light"]` on `<html>`. Dark is the primary product theme.

In WPF, themes will be swapped via merged dictionaries at runtime. All screens must use `DynamicResource` so theme changes are live.

---

## Status

**v0.1 — preview with primitives + product-specific components.**

Covered:
- Foundations (colors, typography, spacing, radii, elevation)
- Primitives (Button, Alert, Badge, Card, Input, Select, Checkbox, Radio, Switch, Slider, Dialog, DropdownMenu, Tabs, SegmentedControl, Tooltip, Toolbar, Table)
- Product (PatientHeader, SettingsList)

Pending verification against Figma (see `components.json → candidates_to_verify_or_remove`):
- Breadcrumb · Pagination · Progress · Empty state

Pending (will be added once their Figma frames are read via Figma MCP):
- ECG strip viewer shell · Measurement panel · Lead-placement card · Numeric keypad modal

---

## Contributing

Any change must touch **both** the CSS files and the JSON spec files:
- Token change → `tokens.css` + `tokens/tokens.json`
- Component change → `styles.css` + `index.html` + `components.json`

See [`AGENTS.md`](./AGENTS.md) for the full rules.

---

## Credits

- Visual language: **Beat DS 2** (Cardioline) — https://beat-ds-2-cardioline.netlify.app/
- Underlying system: **shadcn/ui**
- Source Figma: `Anchor — Windows` (file key `q8E6pYovX5cHWUkr26ogdd`)
