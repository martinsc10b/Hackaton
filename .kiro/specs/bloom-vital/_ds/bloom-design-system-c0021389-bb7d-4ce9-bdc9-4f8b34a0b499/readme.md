# Bloom Design System

Design system for **Bloom** — a platform for educational management ("Innovación en gestión educativa"). Bloom manages educational programs and schools around four core actors: **students, teachers, school owners/administrators, and families**, uniting technology and AI in the service of education.

This system encodes Bloom's brand foundations (color, type, spacing, motion), reusable UI components, and a recreation of its administrative platform, so designers and agents can produce on-brand Bloom interfaces and assets.

## Sources
- **Brand manual** — `uploads/Manual de Marca - Bloom (1)-comprimido.pdf` (Setiembre 2025, by dudesign.pe). Primary source of truth for color, type, logo rules, graphic resources and photography. Contact per manual: info@dudesign.pe.
- **Figma toolkit** — `https://www.figma.com/design/m8uTMc4zwT5iFXRnuhSXts/Bloom-Design-System-—-Working-Copy` (file "Bloom Design System"). Pages seen in a shared screenshot: *Admisión – Desktop*, *Admisión – Mobile*, *Estudiantes – Desktop*, plus modules **Secretaría, Tesorería, Psicología** and color groups Neutral/Primarios/Secundarios/Terciarios/Gradients and effect styles Popup/Tool bar/Cards/Elementos internos.
  ⚠️ **Figma was NOT connected** during this build (no read access to components/variables). Component values and the UI kit are grounded in the manual + one screenshot, not the live Figma. **Connect Figma via the Import menu** to verify the exact component inventory, variables and screen designs.

---

## CONTENT FUNDAMENTALS
- **Language:** Spanish (Peru). Product UI copy is in Spanish.
- **Voice:** warm, optimistic, human — "amigable y menos corporativa" is an explicit brand goal for the mark, and it extends to copy. Confident but not stiff.
- **Person:** speaks *to* and *about* its four actors ("Para los estudiantes… A los profesores… A las familias…"). Institutional "we" ("Unimos tecnología e inteligencia artificial al servicio de la educación").
- **Casing:** sentence case for UI labels and headings (e.g. "Gestión del postulante", "Nuevo postulante"). Not ALL-CAPS except small eyebrow/metadata labels.
- **Tone examples (from manual):** "Innovación en gestión educativa"; "inspirar y guiar proyectos que transformen vidas"; "para que sean participantes activos en el desarrollo de sus hijos".
- **Emoji:** none in the brand system. Do not use emoji as UI iconography.
- **Handwritten accent:** the secondary handwritten font is reserved to "resaltar términos de relevancia… con moderación" — one word or a short phrase, never body copy.

## VISUAL FOUNDATIONS
- **Color:** built on the manual's principal palette — cyan **#16AAD3** (primary), navy **#001f4b** (ink/deep), lima **#e0e657** (accent, used sparingly), celeste **#e9f4f8** (light surface tint). Full tints/shades and cool-grey neutrals are derived in `tokens/colors.css`. Semantic status colors (success/warning/danger) are derivations (see Intentional additions).
- **Type:** **Switzer** for everything (titles → captions), across weights 400–800 to build hierarchy. **Caption Handwriting** as a sparing manuscrita accent — substituted here with **Caveat** (see caveats). Scale is a 1.20 minor-third on a 16px base.
- **Spacing:** 4px base scale (`--space-*`).
- **Backgrounds:** clean and light — white/celeste surfaces, generous whitespace, "mucha luz y optimismo". No heavy textures. Brand gradient is a subtle **cyan→celeste** (`--gradient-bloom`), seen in the microilustraciones.
- **Corner radii:** friendly and soft — cards 16px, controls 8–12px, pills for badges/tags.
- **Cards:** white surface, 1px subtle border, soft diffuse shadow (`--shadow-sm`), 16px radius; optional cyan→celeste accent bar on top.
- **Shadows:** low-contrast, diffuse, tinted with navy (`rgba(0,31,75,…)`) — never harsh black.
- **Motion:** gentle — standard/out easing, 120–320ms, no bounce. Hover = subtle lift + deeper shadow (cards) or darker fill (buttons); press = darker fill (no shrink).
- **Hover/press:** buttons darken one step on hover, one more on active; soft/ghost controls fill with celeste. Focus = 3px cyan glow (`--shadow-focus`).
- **Imagery:** bright, warm, optimistic photography featuring the four actors in educational/tech settings (laptops referencing the platform). Logo on photos goes single-color for contrast.
- **Graphic resources:** *microilustraciones* (cyan-gradient spot icons with lima accents, navy details, soft shadows and transparent overlapping shapes) and *flores abstractas* (abstract flower motifs from the "Bloom" name — vector, not yet exported).

## ICONOGRAPHY
- The manual defines no built-in UI icon font. Bloom's distinctive iconography is its **microilustraciones** — cyan-gradient illustrated spot icons (see `assets/illustrations/`, exposed via the `Illustration` component).
- For small functional UI glyphs (toolbars, table actions, form affordances), no branded set exists in the sources. This system currently uses lightweight Unicode glyphs as placeholders in demos. **Recommended substitution: a thin-stroke open-source set (e.g. Lucide or Phosphor) via CDN** to match the light, friendly weight — FLAGGED, pending confirmation and/or the real Figma icon set.
- **No emoji** as iconography. **No hand-rolled brand SVGs** — real vector assets (logo, flowers) should be exported from the brand kit.
- Real illustrations copied in: `assets/illustrations/` (document, chat, laptop, calendar, smile, approved). Photography samples: `assets/photos/`.

---

## Index (root manifest)
- `styles.css` — global entry point (only `@import`s). Consumers link this.
- `tokens/` — `fonts.css`, `colors.css`, `typography.css`, `spacing.css`, `base.css`.
- `guidelines/` — foundation specimen cards (Colors, Type, Spacing, Brand).
- `components/` — reusable React primitives (see below).
- `ui_kits/plataforma/` — admin platform recreation (Secretaría view + shell).
- `assets/` — `illustrations/`, `photos/`.
- `thumbnail.html` — homepage tile.
- `SKILL.md` — Agent-Skills-compatible entry.

### Components
Namespace: `window.BloomDesignSystem_c00213`.
- **forms/** — `Button`, `IconButton`, `Input`, `Select`, `Checkbox`, `RadioGroup`, `Switch`
- **data/** — `Table`, `Badge`, `Tag`
- **layout/** — `Card`
- **feedback/** — `Alert`
- **brand/** — `Logo` (real vector), `Illustration`

### UI kits
- **Plataforma administrativa** (`ui_kits/plataforma/`) — sidebar + topbar shell with a working **Secretaría → Gestión del postulante** screen (stat cards, filters, data table, "Nuevo postulante" drawer form). Other modules (Admisión, Tesorería, Psicología) are stubbed. ⚠️ Screenshot-derived, not verified against Figma/code.

### Intentional additions
- **Neutral grey scale & tints/shades** — the manual gives four brand colors only; UI needs a full ramp. Derived harmoniously from the brand hues.
- **Semantic status colors** (success/warning/danger) — needed for an admin product; derived, not in the manual. Info = brand cyan.
- **Standard component set** — the exact Figma inventory wasn't readable, so a conventional admin-oriented set (forms, table, card, badge, alert) was authored from the manual + product screenshot. Verify/extend against Figma once connected.

## Caveats
- **Logo:** the real Bloom vector (hand-drawn ascending script) is inlined in `Logo` and recolors via `color`. The San Pío X school crest (for the tenant sidebar/login) is still pending.
- **Font substitution:** *Caption Handwriting* (secondary accent) is a paid font with no free webfont → substituted with **Caveat** (Google Fonts). *Switzer* is loaded from the Fontshare CDN. Provide the licensed font files to self-host both.
- **Flores abstractas** motif not exported (vector) — noted in the Brand cards.
- **UI kit fidelity** depends on connecting Figma.
