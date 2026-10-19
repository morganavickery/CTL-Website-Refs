# CTL Website Rebuild — Project Context

## Documentation & Comment Style

Keep this file, `dev-checklist.md`, and `dotCMS Variable Map.md` — and comments inside
content-block files — free of editing history. No dates, no "per user request" /
"user feedback" / "user-confirmed" phrasing, no bug-narrative writeups, no changelog-style
entries. Document current state and need-to-know facts only:

* **This file (CLAUDE.md):** project context, infrastructure, and dev notes that are
  strictly necessary for the project to function or to debug it — written so a developer
  (or a different LLM with no memory of past sessions) has everything needed to pick up the
  project cold.
* **`dotCMS Variable Map.md`:** a pure index — content types, fields/variables, field
  types, and category trees. No narrative.
* **`dev-checklist.md`:** a to-do list only. Checkbox + one line. No explanations.
* **Content-block file headers and inline comments:** concise "what is this, how does it
  work, what does a new dev need to know" — not a record of how it got that way.

## Overview

Building the SUNY Cortland CTL (Center for Teaching and Learning) website rebuild in dotCMS,
at /centers/ctl/. Built from Figma mockups using Cortland's Bootstrap 5.3 theme. Primary
builder/content editor is the project owner; CTL colleagues (Jennifer Hall, Kristina
Maricle, Lisa Sieg, Heather Hammond) will author content later. IT/web team handles
elevated permissions.

CTL does not have its own custom advanced template — CTL pages use one of dotCMS's
built-in templates (`DEPRECATED/ctl-site_page template.html` is the old custom template,
kept only as a historical reference; it has no effect on live pages). Detail-page logic
lives in the listing content blocks, not the template. Consequence: there is no single
shared `<head>` to hook sitewide assets into. Workaround: a sitewide content block (contact
info/hours, rendered in the nav area on every CTL page) carries a
`<link rel="stylesheet" href="...">` to the shared stylesheet, `styles/ctl-widgets.css` —
editing that one content record updates the CSS link on every page.

All site code — both static copy and content that pulls/filters live data — is authored as
a dotCMS content block (content type/Code asset), not the older Widget content type. Content
blocks evaluate Velocity in this instance.

Content types (10 total, prefixed `Ctl`): CtlActivity (unified events/opportunities),
CtlActivitySession, CtlAttachment, CtlInstructionalResource, CtlPersonnel, CtlService,
CtlReport (proposed), CtlPastRecipient (proposed), CtlTechSupport (created in dotCMS —
don't build against `CtlTechResource`, that name was deprecated in favor of
`CtlTechSupport`), and others. Full field-level reference lives in
`dotCMS Variable Map.md` — treat it as the source of truth for field/variable names; don't
assume from memory.

## Site Information Architecture

All under `/centers/ctl/`:

1. **Welcome to the CTL** (Level 1 – Landing Page) — `/centers/ctl/`
2. **Request Support** (Level 1 – Landing Page) — `/centers/ctl/request-support`
   * 2.1 **Request a GIFT** (standalone service detail page, not in main nav) — `/centers/ctl/pages/request-a-gift`
3. **Events** (Level 1 – Landing Page) — `/centers/ctl/events`
   * 3.1 **Activity Detail Page** (Level 3 / Dynamic) — `/centers/ctl/activities/{urlMapPattern}` (shared content block)
4. **Opportunities** (Level 1 – Landing Page) — `/centers/ctl/opportunities`
   * 4.1 **Awards** (Level 2) — `/centers/ctl/opportunities/awards`
     * 4.1.1 **Activity Detail Page** (Level 3 / Dynamic) — same shared content block as 3.1
   * 4.2 **Grants & Funding** (Level 2) — `/centers/ctl/opportunities/grants-funding`
     * 4.2.1 **Activity Detail Page** (Level 3 / Dynamic) — same shared content block as 3.1
   * 4.3 **Programs & Communities** (Level 2) — `/centers/ctl/opportunities/programs-communities`
     * 4.3.1 **Activity Detail Page** (Level 3 / Dynamic) — same shared content block as 3.1
   * 4.4 **Past Recipients** (Level 2) — `/centers/ctl/opportunities/past-recipients`
5. **Resources** (Level 1 – Landing Page) — `/centers/ctl/resources`
   * 5.1 **For Instructional Design** (Level 2) — `/centers/ctl/resources/instructional-design-resources`
     * 5.1.1 **Instructional Resource Detail Page** (Level 3 / Dynamic) — `/centers/ctl/resources/instructional-design-resources/{urlTitle}`
   * 5.2 **For Technologies** (Level 2, not yet built) — `/centers/ctl/resources/technologies`
6. **About the CTL** (Level 1 – Landing Page; carries mission/vision copy directly) — `/centers/ctl/about`
   * 6.1 **Meet the CTL Staff** (Level 2) — `/centers/ctl/about/staff`
   * 6.2 **Faculty Development Committee** (Level 2) — `/centers/ctl/about/faculty-development-committee`
   * 6.3 **Impact & Reports** (Level 2, not yet built) — `/centers/ctl/about/impact-reports`

Live URL paths above are the intended targets throughout this repo's code/docs — actual
dotCMS page creation/renaming/URL-Map-Pattern updates to match are tracked in
dev-checklist.md. Events and Opportunities > Awards / Grants & Funding / Programs &
Communities all link to one shared Activity Detail content block
(`ctl-content_activity-detail.html`, unified CtlActivity content type) via vanity URL
forwarding, distinguishing which listing a request came from — see that file and the
vanity-URL gotchas below.

Standing IA decisions:

* "Other Affiliates" is not part of the IA — the FDC/personnel grid content block is
  FDC-only.
* Request Support has its own duplicate "Make an Appointment" staff-cards content-block
  instance, separate from About the CTL > Meet the CTL Staff (same content-block content
  type/Code, a second content record with a different `sectionHeading`).
* "Our Mission & Outcomes" is not a standalone subpage — that copy lives directly on the
  About the CTL landing page.
* "Request a GIFT" is a standalone static page reached only via the Our Services tile's
  `serviceAccessLink` field on its CtlService record (not part of primary site nav) — see
  `ctl-content_request-a-gift.html`.
* Opportunities splits CtlActivity opportunity records by category into three listing pages
  — Awards (`Award` category), Grants & Funding (`Grant`/`Fellowship`/`Teaching Grant`/
  `Research Grant`), Programs & Communities (any other category, e.g. Book Club, Community
  of Practice, Working Group, Workshop). A multi-category record can appear on more than one
  page. Each of those three pages hosts both its open calls and a collapsible "Missed
  Connections" section — there's no separate "current" vs. "expired" subpage anymore. An
  item lands in Missed Connections if its `signupDeadline` has passed OR
  `signupCapacityReached` is manually checked (same open/closed logic as
  `ctl-content_activity-detail.html`'s own signup-status badge). Past Recipients is a fourth,
  distinct page: one row per honor (Relationship on CtlPersonnel -> CtlActivity) whose
  linked activity carries an Award/Grant/Fellowship/Teaching Grant/Research Grant category —
  actual winners, not open calls. The landing page carries a tile grid to all four subpages
  plus a separate "master list" content block showing every opportunity regardless of
  category, labeled Open/Closed.

## Content Block Map

All files below live in `content blocks/`, prefixed `ctl-content_`.

| Page | File(s) | Content Type(s) |
|----|----|----|
| Welcome to the CTL (home) | `ctl-content_home-nav-tiles.html`, `ctl-content_upcoming-activities-carousel.html`, `ctl-content_featured-instructional-resources.html` | CtlActivity, CtlInstructionalResource |
| Request Support | `ctl-content_our-services.html`, `ctl-content_ctl-staff-profiles.html` (2nd instance) | CtlService, CtlPersonnel |
| Request Support > Request a GIFT | `ctl-content_request-a-gift.html` | — |
| Events | `ctl-content_events-overview_events.html`, `ctl-content_happening-now-events.html`, `ctl-content_upcoming-events.html`, `ctl-content_past-events_events.html` | CtlActivity |
| Events / Opportunities > Activity Detail | `ctl-content_activity-detail.html` (shared) | CtlActivity |
| Opportunities | `ctl-content_opportunities-overview_opportunities.html`, `ctl-content_all-opportunities_opportunities.html` (master list) | CtlActivity |
| Opportunities > Awards | `ctl-content_awards_opportunities.html` | CtlActivity |
| Opportunities > Grants & Funding | `ctl-content_grants-funding_opportunities.html` | CtlActivity |
| Opportunities > Programs & Communities | `ctl-content_programs-communities_opportunities.html` | CtlActivity |
| Opportunities > Past Recipients | `ctl-content_past-award-recipients_opportunities.html` | CtlPersonnel, CtlActivity |
| Resources | `ctl-content_resources-overview.html` | — |
| Resources > For Instructional Design | `ctl-content_instRes-parent.html` | CtlInstructionalResource |
| Resources > For Instructional Design > Detail | `ctl-content_instRes-detail` | CtlInstructionalResource |
| Resources > For Technologies | `ctl-content_tech-support-listing_technologies.html` | CtlTechSupport |
| About the CTL | `ctl-content_about-the-ctl.html` | — |
| About the CTL > Meet the CTL Staff | `ctl-content_ctl-staff-profiles.html` | CtlPersonnel |
| About the CTL > Faculty Development Committee | `ctl-content_fdc-member-grid.html`, `ctl-content_fdc-description.html` (empty stub) | CtlPersonnel |
| About the CTL > Impact & Reports | `ctl-content_impact-reports-intro.html`, `ctl-content_outcomes-overview.html`, `ctl-content_reports-grid.html` | CtlReport (proposed) |

Some content blocks are static copy with no `$dotcontent.pull()` — authored as dotCMS
content blocks with no data pull. Others pull/filter live content — authored the same way,
as dotCMS content-type/Code assets, just with Velocity that queries content. Each content
block is self-contained (own CSS/JS, no shared include mechanism between them in this dotCMS
instance) — the shared stylesheet (`styles/ctl-widgets.css`) is the one exception, loaded
sitewide via the `<link>` described in Overview.

**Naming convention:** file `ctl-content_{description}_{page}.html` (page suffix omitted
when the file name is already unambiguous). dotCMS content-type name: `CTL content_{description}`;
instance name adds `_{page}` for the same pattern reused on multiple pages/records (e.g. the
staff-profiles content block on both Meet the CTL Staff and Request Support).

## Critical Instance-Specific Gotchas (this dotCMS instance specifically)

* `$dotcontent.pull()` sort-by-field returns empty results — always sort by "score", do real sorting in JS/Velocity.
* `$navTool` unavailable.
* Page editor preview uses a different internal API path than the live URL — always test via live URL + view-source, not the editor preview.
* Don't use /dA/, /dotAsset/, /convert/image?id=, /contentAsset/raw-data/ for images — none work here.
* Image field → FileAssetMap: `src="$item.field.path$item.field.fileName"`, check existence with `#if("$!item.field.fileName" != "")`.
* **File**-type field (dotCMS field type "File") → also a FileAssetMap, same as Image fields — use `.path`/`.fileName`, NOT `.rawUri`/`.thumbnailUri`/`.resizeUri` (those are BinaryMap accessors and silently resolve empty on a File field).
* **Binary**-type field (dotCMS field type "Binary", a genuinely different field type from "File") → BinaryMap: use `.rawUri`, `.name`, `.thumbnailUri`, `.resizeUri`. Confirm which type a field actually is before choosing an accessor — don't assume from the field's display name.
* CheckboxMap → `.selectedValues` (parallel `.values`/`.options` arrays for display labels).
* RadioMap → `.selectValue`.
* Category fields → different access pattern than Checkbox/Radio (iterate the field, read `.categoryName` per item) — verify in isolation before assuming a `$item.fieldName` direct-attribute read will work.
* Date fields are `java.sql.Timestamp` — use `.getTime()` for epoch comparisons.
* Velocity's `>`/`<` operators only reliably work Number-vs-Number in this engine — comparing two Strings silently never evaluates true (a bubble sort built on String keys just never swaps, with no error). A field labeled "Text ... whole number" in the dotCMS editor may already be a `java.lang.Long` at the Velocity layer — check `$item.field.class.name` before assuming you need to coerce a String to a Number; if it's already numeric, use it directly and skip any String round-trip. Treat blank as a large sentinel Number (e.g. `999999`), not a padded string.
* `$!` quiet reference hides empty fields AND wrong variable names identically — a typo'd variable name is silently indistinguishable from an empty field. Always verify field/variable names against a fresh REST API pull, not memory or older docs.
* A literal `\$` inside a DOUBLE-quoted Velocity string (e.g. a Java regex passed to `.matches("^\$?...")`) is unreliable — VTL's own `$`-escaping can interfere with what reaches the underlying Java string. Use a SINGLE-quoted string instead (`.matches('^\$?...')`) — VTL does not parse single-quoted strings for references/escapes at all, so the backslash reaches Java's regex engine intact. This is what `oppAmountPaid`'s numeric-vs-non-numeric check (in the three Opportunities tag-page content blocks and `ctl-content_activity-detail.html`) relies on.
* This Velocity engine treats `-` as a valid identifier character, so a hyphen glued
  directly onto a reference with no space (e.g. `"$!p.startYear-$!p.endYear"`) gets parsed
  as part of that reference's property name instead of as a literal hyphen — a nonexistent
  property that `$!` then silently renders as blank, leaving only the second reference
  visible. Always put a space on both sides of a hyphen between two references (e.g.
  `"$!p.startYear - $!p.endYear"`).
* Checkbox/Category option VALUES can be case-sensitive against `.contains()` checks (e.g. CtlPersonnel's `affiliations` stores `"CTL"`/`"FDC"`/`"Affiliate"`/`"Recipient"`/`"Other"`, capitalized, multi-select — a record can hold more than one). Confirm actual option values via REST pull, don't guess casing.
* A Radio/Checkbox field can have a real selectable option whose meaning is "show nothing" (e.g. CtlPersonnel's `pronouns` has a `"dont show pronouns"` option) — this looks identical to blank/unset at the template layer unless checked for explicitly.
* CtlPersonnel has no per-honor date field on `honorRecieved` (the granting-body `affiliation` and one-per-person `yearAwarded` fields were removed). Any CtlActivity meant to be linked as a past-recipient honor must end its `title` with a literal `(YYYY)` — `ctl-content_past-award-recipients_opportunities.html` parses that suffix to get the year and sort. An honor without it just won't show a year on that page.
* The old custom page template's secondary nav (no longer in use) was data-driven: it pulled `+contentType:htmlpageasset +path:/centers/ctl/*` and included a page only if its own `showOnMenu` Checkbox field (a page-level field) contained `"true"`. Reproduce the same `showOnMenu`-gating if the built-in template's nav ever needs equivalent behavior.
* `urlMapPattern` is not indexed for Lucene — pull-all + Velocity-side filter instead.
* URL Map Pattern fields (the field referenced in a content type's `{fieldVar}` URL Map Pattern, e.g. `urlTitle`) must have **"System Indexed" AND "Unique" checked** in the field's own settings, or dotCMS's URL Map resolution silently fails to bind — matching URLs 404 at the routing layer even though the URL Map Pattern + Detail Page look correctly configured. Check this first for any URL-mapped content type.
* Hyphenated slugs can fail field-scoped Lucene queries — use a Velocity-side filter instead.
* Use `+path:` not `+url:` in content queries.
* `$uri.substring($uri.lastIndexOf('/') + 1)` silently kills template render (integer boxing bug). Fix:
  ```
  #set($idx = $uri.lastIndexOf('/'))
  #set($idxPlus = $idx.intValue() + 1)
  $uri.substring($idxPlus)
  ```
* Relationship fields are pre-hydrated — no separate pull needed.
* Velocity array assignment: `#set($list[$i] = $value)`.
* This Velocity engine does NOT support map literals (`#set($x = {"k": v})`) — one blanks the entire page render with no error message. Use parallel arrays instead.
* `$foreach.count` is NOT supported (a Velocity 2.x feature) — use a manually incremented counter (`#set($i = $i + 1)`).
* Inline arithmetic directly inside a `.get(...)` call or `[...]` index (e.g. `$list.get($i - 1)`) silently fails to render past that point. `#set` the computed index to its own variable first, then use that plain variable in the call. Arithmetic directly in a `#set` right-hand side or an `#if` condition is fine.
* Velocity's `[m..n]` range operator produces a DESCENDING list when `m > n` (e.g. `[1..0]` evaluates to `[1, 0]`, not an empty range). A hand-rolled bubble sort deriving its range from a shrinking bound (e.g. `[1..($n - $i)]`) hits this on its last outer pass and produces an out-of-bounds `.get(-1)` call that crashes the render. Fix: use fixed ranges (e.g. `[0..99]`) with explicit `#if` bounds guards instead of deriving range bounds from a value that can reach zero or go negative.
* CtlEventItem/CtlOpportunity are deleted, replaced by unified CtlActivity (`isEvent`/`isOpportunity` checkboxes).
* Combine listing+detail Velocity logic into one content block (slug switches view) — Constant Field widgets don't evaluate Velocity on URL-mapped detail pages.
* Vanity URL routing across multiple listing prefixes into one detail page: use `$request.getAttribute("javax.servlet.forward.request_uri")`.
* Vanity URL regex: `(.+)` not `(.*)`, plus negative lookahead `(?!index\.dot$)` to avoid catching bare listing URLs.
* Unicode bold/circled numbers/emoji/Braille Pattern Blank (U+2800 as invisible spacer for required-but-empty Line Divider fields) all work in field labels/hints.
* Use Category fields (not Tag fields) for vocabularies — Tag fields have limits.
* A required Category field (e.g. CtlActivity's `categories`) must be populated in the CSV
  content importer, or the row errors — leaving the column blank does NOT work even though
  the field is a multi-select. Use the category's Variable (not its display Name), one cell,
  multiple values comma-separated for a multi-category record (e.g. `teachingGrant,paid`) —
  same format the live export data already uses.
* Category import CSV needs name, key, variable, sort columns.
* dotCMS REST API: base `https://suny-auth.dotcms.cloud/api/v1/contenttype?per_page=500`; single type `/api/v1/contenttype/id/{variableName}`; being logged into admin in-browser is sufficient auth; fields nested in layout → columns → fields, check `fieldType == "Tab"` for tab boundaries; large JSON responses work well as RTF attachments.
* Never name a `#foreach` loop variable `$session` — it collides with dotCMS/Velocity's built-in HTTP session object, and every field reference inside the loop silently resolves against the wrong object instead of erroring. Same applies to `$request`/`$response`/`$user`.

## Working Approach

* Isolate before building: test with `$list.size()` debug pull first, add markup only after data confirmed.
* Debug sequentially/via bisection — narrow down before guessing fixes.
* Test via live URL + view-source, never editor preview.
* Prefer complete file outputs after major changes, not partial snippets.
* Prefer plain text output for copy-paste into existing docs (not generated files).
* Prefer existing Bootstrap classes over custom CSS; avoid `!important` — build standalone classes (like `.ctl-btn`) when theme conflicts arise.
* Prefer Velocity-side filtering over Lucene queries for unreliable-in-index fields.
* Use `$!` quiet reference syntax throughout, but verify field/variable names against a fresh REST API pull when something returns unexpectedly empty — don't assume it's just empty data.
