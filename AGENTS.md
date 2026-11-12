# Anchor DS — AGENTS.md

Instructions for any AI assistant working in this folder (Claude Code, Copilot, Cursor, Codex, ChatGPT, etc.).

Read this file **first** before generating code, editing styles, or proposing components.

---

## 1. What this is

**Anchor Design System.** A WPF/XAML design system for the Anchor software (Cardioline), modeled 1:1 on **Beat DS 2** (which is **shadcn/ui**-based, in Vue). This folder (`DS-Preview/`) is the **web visual specification**. The production implementation ships as XAML `ResourceDictionary` files inside `Cardioline.Anchor.Stylesheet` (a WPF class library), layered on top of **MaterialDesignThemes.Wpf** (MDIX).

**Do not** rebuild the app in web tech. The preview is a spec; the app stays WPF.

---

## 2. The three source-of-truth files

1. **`tokens/tokens.json`** — every color, radius, spacing, type, shadow. Theme-aware (light + dark). Includes the planned WPF resource key per token (`wpf` field).
2. **`components.json`** — every component we ship: its shadcn name, variants, sizes, states, MDIX base, planned WPF resource key, when to use, when not to use.
3. **This file (`AGENTS.md`)** — the rules.

If you change a token or component, update **all three** (tokens.css + tokens.json; styles.css + components.json) in the same change.

---

## 3. Rules

### Tokens
- **Always** use semantic tokens: `var(--primary)`, `var(--card)`, `var(--muted-foreground)`.
- **Never** reference raw palette values from components (no hardcoded hex, no `oklch(...)` literals inside component rules).
- **Never** invent a new token without adding it to `tokens.json` and `tokens.css` with both light + dark values.
- Status colors live under `--status-{info|success|warning|destructive|neutral}-{dot|bg|fg}`.

### Components
- **Shadcn naming is canonical.** Button variants = `default | secondary | outline | ghost | destructive`. Sizes = `sm | default | lg | icon`. Alert variants = `info | success | warning | destructive` (Anchor extends shadcn's two to four — see Figma).
- **Only add components present in the Anchor Figma.** If shadcn has `Breadcrumb` or `Command` and Anchor doesn't use them, **do not add them**. The product's Figma is the source of truth for scope.
- Every new component gets an entry in `components.json` with `figmaStatus: confirmed|likely|unverified` and the WPF resource key it will map to.

### WPF production (when translating to XAML)
- Base MDIX is kept — do not remove or replace it.
- DS = a ResourceDictionary pack that **overrides MDIX tokens** and provides Beat 2–shaped styles via `BasedOn={StaticResource MaterialDesign…}`.
- Resource key naming: `DS.Brush.*`, `DS.Style.*`, `DS.Radius.*`, `DS.Space.*`, `DS.Font.*`, `DS.Shadow.*`. See `wpf` fields in `tokens.json` and `components.json`.
- Theme switching is runtime via merged dictionaries; all consumer screens use `DynamicResource`, never `StaticResource`.

### Figma reconciliation
- The Anchor Figma file key is `q8E6pYovX5cHWUkr26ogdd`. The "Components MVP3" board (`node-id=37646-28816`) is the primary reference.
- When Figma MCP is unavailable, reference the exported PNGs under `C:/Users/s.carboni/OneDrive - CARDIOLINE S.P.A/Documenti/ANCHOR SYSTEM/Figma Screens/`.
- If something in the DS and Figma diverge, **Figma wins**. Flag the divergence; don't silently "fix" the DS.

### Icons
- New icons follow the **Feather** icon set (a fork-lineage of Lucide, the shadcn/ui default): 24×24 grid, 2px stroke, round caps and joins, `currentColor`.
- Icons already exported from Figma stay as they are, even where they differ from Feather. Figma wins.
- Feather has no ruler icon; keep it as a custom asset until a substitute is chosen.

### Scope discipline
- Ignore shadcn components not seen in Anchor Figma (see `components.json` → `candidates_to_verify_or_remove`).
- Do not design for hypothetical future screens.

---

## 4. How to answer common prompts

| User asks… | You should… |
|---|---|
| "Add a date picker" | Check `components.json`. Not there. Confirm it exists in Figma before scaffolding. |
| "Change the primary color" | Edit `tokens/tokens.json` **and** `tokens.css` for both themes. Open the preview to verify. |
| "Make a settings screen" | Compose `SettingsList` + `Switch` + `Input` + `SegmentedControl` from components.json. Don't invent new primitives. |
| "Translate Button to XAML" | Produce a `Style TargetType="Button" BasedOn="{StaticResource MaterialDesignRaisedButton}"` per variant, using `DS.Brush.*` DynamicResource bindings. |
| "Delete the destructive alert" | Refuse — it's in Figma. Instead, ask what the user actually wants to change. |

---

## 5. Folder layout

```
DS-Preview/
├── AGENTS.md              ← this file — rules for AIs
├── README.md              ← human-facing overview
├── index.html             ← showcase (served on localhost:8765)
├── tokens.css             ← CSS variables (generated from tokens.json)
├── styles.css             ← component styles
├── components.json        ← component manifest (machine-readable)
└── tokens/
    └── tokens.json        ← token source of truth (machine-readable)
```

When the DS is ported to XAML, the WPF project under DevOps will mirror this:

```
Cardioline.Anchor.Stylesheet/
├── Themes/
│   ├── Tokens.Light.xaml
│   └── Tokens.Dark.xaml
├── Styles/
│   ├── Button.xaml
│   ├── Input.xaml
│   └── ... (one per component)
└── Controls/
    └── (custom controls only if MDIX has no equivalent)
```

---

## 6. Non-negotiables

1. **Don't break the shadcn contract.** Beat 2 is shadcn. Anchor DS is shadcn. Any AI reading this should produce code that still looks like shadcn.
2. **Don't rip out MDIX.** 80% of the app already uses it. The DS layers on top.
3. **Don't add what's not in Anchor Figma.** Scope is set by product, not by the library.
4. **Don't hardcode colors.** Ever.
