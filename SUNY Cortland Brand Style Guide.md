# SUNY Cortland Brand Style Guide (Reference)

> pulled from: <https://www2.cortland.edu/offices/publications/big/colors> & <https://www2.cortland.edu/offices/publications/big/fonts>

## Colors

SUNY Cortland's official colors are red and white and must be used in all internal and
external materials. Black is part of the primary palette because it appears in the logo.

Color value used depends on medium:

* **Print:** PMS or CMYK
* **Web/digital:** RGB or web (hex) values

### Primary colors

Brand collateral should lean heavily on primary colors (red, white, black). Primary red
should be used in a significant and meaningful way as the identifying color of the
university.

| Name | PMS | CMYK | RGB | Web (hex) | Paint (Benjamin Moore) |
|----|----|----|----|----|----|
| Red | PMS 186 | 0-100-81-4 | 176, 11, 0 | `#B00B00` | Bonfire 2001-20 |
| White | PMS White | 0-0-0-0 | 255, 255, 255 | `#FFFFFF` | Super White OC-152 |
| Black | PMS Black | 0-0-0-100 | 0, 0, 0 | `#000000` | — |

### Secondary colors

Softer, more muted colors that reinforce the brand while complementing the saturated
primary red (PMS 186). May **not** replace the primary colors or become the predominant
color for a school/center/institute/department/office/program. Use with restraint —
maintain hierarchy, balance, and harmony.

| Name | PMS | CMYK | RGB | Web (hex) | Paint (Benjamin Moore) |
|----|----|----|----|----|----|
| Deep Red | PMS 7623 | 0-100-81-54 | 135, 42, 43 | `#8A2A2B` | Caliente AF-290 |
| Cool Gray 1 | PMS Cool Gray 1 | 7-5-4-3 | 239, 238, 237 | `#EFEEED` | Gray Cloud 2126-60 |
| Cool Gray 8 | PMS Cool Gray 8 | 44-36-32-1 | 149, 150, 156 | `#95959C` | Sweatshirt Gray 2126-40 |
| Warm Gray 1 | PMS Warm Gray 1 | 0-2-7-9 | 233, 227, 216 | `#E7E3DB` | Alaskan Skies 972 |
| Deep Navy | PMS 5395 | 91-30-0-82 | 4, 32, 46 | `#04202E` | Deep Royal 2061-10 |

### Tertiary colors

A palette of warm and energetic tertiary colors is also available, intended to
complement the primary and secondary palettes. Use sparingly — may not function as the
predominant color in any print or digital materials.

Specific tertiary color values were not provided by the user — do not fabricate hex/RGB/
CMYK/PMS values for this tier; ask before using a tertiary color if one is needed.

## Typography

Two official university typefaces, downloadable for campus use: **Agenda** (sans-serif)
and **Utopia** (serif). Campus departments/offices must use official university typefaces
or their approved replacement options in all communications and merchandise/apparel
ordering. Contact the Marketing Office for further typeface assistance
(607-753-2519 / marketing@cortland.edu).

### Primary typefaces

* **Agenda** — sans-serif (no serif flourishes at stroke ends). Ideal for subheads, body
  copy, captions. Downloadable from the Tech Help tab in myRedDragon.
* **Utopia** — serif (small decorative "serifs" at stroke ends). Ideal for headlines,
  subheads, or body copy. Request from the Marketing Office (607-753-2519 /
  marketing@cortland.edu).

### Secondary typefaces

* **Abolition** — accent font only; works well for headlines, subheads, pull-quotes, and
  environmental graphics. Never use for body copy. Limited use by the Marketing Office
  only — not for general campus/department use.
* **Utopia Caption** — preferred variant of Utopia at smaller point sizes.

### Replacement options

| Context | Agenda replacement | Utopia replacement |
|----|----|----|
| Internal communications | Calibri | Times New Roman |
| Web-based platforms | Open Sans | Times New Roman ("Times Neue Roman" per source doc) |

## Notes for future CTL site application

* Resolved: `--ctl-maroon` in `styles/ctl-widgets.css` (the `.ctl-btn` outline, hover
  fills, active pills, featured borders, etc.) is set to Secondary Deep Red `#8A2A2B`.
  The site's ad hoc UI grays were also consolidated onto Cool Gray 1 (`#EFEEED`, light
  backgrounds), Warm Gray 1 (`#E7E3DB`, hairline borders — reused as `--ctl-border`),
  and Cool Gray 8 (`#95959C`, secondary/meta text — `--ctl-gray-text`). Two body-copy
  text colors (`#333`, `#444`) were deliberately left alone: none of the three brand
  grays are dark enough to clear WCAG AA contrast (4.5:1) for paragraph text on white.
  Bootstrap's default status colors were left as-is for genuinely functional UI states
  (e.g. the "Closed"/"Past Event" red badge, `--ctl-closed-*`) — out of scope for a
  brand-compliance pass. FIXED 2026-08-07: the Registration/Application status badges on
  Activity Detail were a different case, not a functional-state color — they were
  Bootstrap's info-blue/warning-yellow standing in for what's really an emphasis/CTA
  badge, so those were brought onto brand maroon (`--ctl-maroon-tint`/`--ctl-maroon` for
  Registration, solid `--ctl-maroon`/`#fff` for the more selective Application) instead
  of being left as-is. See `styles/ctl-widgets.css`'s design-tokens comment.
* Resolved 2026-08-07: the homepage carousel's Event/Opportunity/"Event & Opportunity"
  type badges (`ctl-widget_upcoming-activities-carousel.html`) used Bootstrap's
  `text-bg-success`/`text-bg-primary`/`text-bg-dark` — same non-brand-color issue as above,
  flagged separately in dev-checklist's "Side scroll cards" item. Fixed by extending the
  same tint/solid maroon pattern with one additional brand color, Secondary "Deep Navy"
  (`#04202E`) as `--ctl-navy`/`--ctl-navy-tint`, since two independent type flags on one
  card need 3 distinguishable states and maroon tint/solid alone only cleanly covers 2:
  Event = maroon tint, Opportunity = navy tint, Event & Opportunity (combined) = solid
  maroon/white (most emphasis, same logic as Application Required above).
* Still open: `.ctl-ir-framework-chip`'s tan/maroon tint (`#f0ece9` background,
  `#e0d6d2` border) was hand-tuned to the old maroon and hasn't been recalculated for
  the new one — a lingering, minor mismatch, not yet addressed.
* Web typefaces for the CTL site should draw from Agenda/Open Sans (sans) and
  Utopia/Times New Roman (serif) per the web-platform replacement row above, pending an
  explicit decision on which to apply where.


