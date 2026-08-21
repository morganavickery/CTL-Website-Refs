# CTL Website Rebuild — Project Context

## Overview

Building the SUNY Cortland CTL (Center for Teaching and Learning) website rebuild in dotCMS,
at /centers/ctl/. Built from Figma mockups using Cortland's Bootstrap 5.3 theme. I'm the
primary builder/content editor; CTL colleagues (Jennifer Hall, Kristina Maricle, Lisa Sieg,
Heather Hammond) will author content later. IT/web team handles elevated permissions.

Site sections (current IA, most recently revised 2026-08-04 — see "Site Information
Architecture" below for the full hierarchy and old→new mapping): Welcome to the CTL,
Request Support, Events, Funding (Current Opportunities, Past Recipients), Resources
(folder: For Course Design, For Technologies), About the CTL (Meet the CTL Staff, Faculty
Development Committee, Impact & Reports). Events and Funding > Current Opportunities /
Past Recipients are listing pages sharing one URL-mapped Activity Detail page (unified
CtlActivity). Detail page logic lives in listing widgets, not a template — this matters
more than ever since the change below: CTL no longer has its own custom advanced template
(as of 2026-08-05). CTL pages are now required to use one of dotCMS's built-in templates
instead (`DEPRECATED/ctl-site_page template.html` is the old custom template, kept only as
a historical reference — do not edit it expecting it to affect live pages). Practical
consequence: there's no longer a single shared `<head>` to hook sitewide assets into (e.g.
a shared stylesheet `<link>`) — see the shared-styles widget in "Current State" below for
the workaround.

Content types (10 total, prefixed Ctl): CtlActivity (unified events/opportunities),
CtlInstructionalResource, CtlTechSupport (planned, NOT yet built — do not build against
CtlTechResource, that name was deprecated in favor of CtlTechSupport to match an existing
live relationship field), CtlAttachment, and others.

A separate local Variable Map document (`dotCMS Variable Map.md` — this is the editable
source of truth going forward; a `.docx` copy also exists but is no longer actively
maintained) is the definitive reference for all types/fields/variable names/tabs/widgets.
It is NOT included here in full — ask before assuming field names on a given content type
if unsure, rather than relying on memory or older docs.

## Site Information Architecture (revised 2026-08-04 — two rounds, same day)

The site's page structure and titles were reorganized twice this session: an initial rename
pass, then a same-day follow-up that restructured "Opportunities" into a two-level "Funding"
branch and added an "Impact & Reports" subpage. Old names still appear throughout this doc's
dated history sections (field maps, bug writeups, widget headers) — those are left as-is
since they're historical narrative describing what was true when written, not stale errors.
This is the current source of truth for page structure:

1. **Welcome to the CTL** (Level 1 – Landing Page) — `/centers/ctl/`
2. **Request Support** (Level 1 – Landing Page) — `/centers/ctl/request-support`
3. **Events** (Level 1 – Landing Page) — `/centers/ctl/events`
   * 3.1 **Activity Detail Page** (Level 3 / Dynamic) — individual page for a specific
     event/workshop/session — `/centers/ctl/activities/{urlMapPattern}` (shared widget, see below)
4. **Funding** (Level 1 – Landing Page: overview of financial support, grants, and awards)
   — `/centers/ctl/funding` (landing content not yet built — see Next Up)
   * 4.1 **Current Opportunities** (Level 2: active calls, application guidelines, open
     windows) — `/centers/ctl/funding/current-opportunities`
     * 4.1.1 **Activity Detail Page** (Level 3 / Dynamic) — individual page for a specific
       grant/fellowship/award call — same shared Activity Detail widget as 3.1
   * 4.2 **Past Recipients** (Level 2: historical archive of past grant/fellowship/award
     winners) — `/centers/ctl/funding/past-recipients`
5. **Resources** (Level 1 – Landing Page: overview of pedagogical guides and teaching
   support) — `/centers/ctl/resources`
   * 5.1 **For Course Design** (Level 2: curriculum planning/design frameworks — the course
     design database) — `/centers/ctl/resources/course-design`
     * 5.1.1 **Instructional Resource Detail Page** (Level 3 / Dynamic) —
       `/centers/ctl/resources/course-design/{urlTitle}`
   * 5.2 **For Technologies** (Level 2: instructional tech tools/software support, not yet
     built) — `/centers/ctl/resources/technologies`
6. **About the CTL** (Level 1 – Landing Page: mission, vision, and center overview — this
   page now carries the intro/welcome content that a separate "Our Mission & Outcomes"
   subpage would have held; that subpage was dropped in the second revision round in favor
   of putting mission/vision copy directly on this L1 page) — `/centers/ctl/about`
   * 6.1 **Meet the CTL Staff** (Level 2: staff directory/biographies; renamed from the flat
     "CTL Staff" in the second revision round) — `/centers/ctl/about/staff`
   * 6.2 **Faculty Development Committee** (Level 2: committee members/governance) —
     `/centers/ctl/about/faculty-development-committee`
   * 6.3 **Impact & Reports** (Level 2: annual reports, track record, accomplishments,
     institutional data — NEW, not yet built) — `/centers/ctl/about/impact-reports`

Live URL paths above are the intended targets throughout this repo's code/docs — actual
dotCMS page creation/renaming/URL-Map-Pattern updates to match are still pending in every
case (see Next Up). Events and Funding > Current Opportunities / Past Recipients all link to
one shared Activity Detail widget (`ctl-site_activity-detail.html`, unified CtlActivity
content type) via vanity URL forwarding, distinguishing which listing a request came from —
see that file and the vanity-URL gotcha below.

Decisions made across both rounds (all user-confirmed 2026-08-04, not assumptions):

* **"Other Affiliates" is dropped from the IA entirely.** The old combined FDC/Other
  Affiliates grid widget (formerly `ctl-site_about-us-affiliate-personnel.html`, at the
  time called "Affiliate Personnel Grid") is now FDC-only, renamed to
  `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` — the "Other"
  affiliation branch was removed from the Velocity code, not just hidden, since the widget
  file itself was renamed to reflect its narrowed scope.
* **Request Support keeps its own duplicate "Make an Appointment" staff-cards widget
  instance**, even though About the CTL > Meet the CTL Staff is now its own dedicated
  subpage — rather than linking Request Support out to that subpage, it still embeds a
  second content instance of `widgets/ctl-widget_ctl-staff-profiles.html` (same pattern as
  before the rename, see that file's header note).
* **"Our Mission & Outcomes" as a standalone subpage was dropped in the second revision
  round.** Its content (the old About Us intro/welcome paragraph) is now intended for the
  About the CTL L1 landing page itself instead of a dedicated subpage — no separate widget
  needed; the "Next Up" item to build a standalone widget for it is superseded.
* **"Funded Opportunities" split into "Funding" (L1 overview) > "Current Opportunities" (L2)
  and "Past Recipients" (L2).** Current Opportunities holds exactly the former Active
  Opportunities widget, unchanged. Past Recipients is new: it combines the former Missed/
  Expired Opportunities widget (relocated, unchanged logic) with a second, not-yet-built
  widget listing actual past award recipients/winners — a genuinely different concept
  (expired calls vs. who won them), not just a rename. See Next Up.
* **"CTL Staff" renamed "Meet the CTL Staff"**, and a new "Impact & Reports" subpage
  (annual reports/accomplishments/institutional data) was added under About the CTL — both
  not yet built beyond the rename itself; see Next Up.

See dev-checklist.md's widget table for the full current widget/instance/page mapping,
including the pending "Funding" landing, "Past Recipients" award-winners widget, and
"Impact & Reports" widget (none built yet).

## Current State (as of this session)

### CtlInstructionalResource — content type + listing widget (v8), actively in progress

Widget has: radio filters (format/modality), collapsible multi-select topic pill cloud
(6 parent category headings), dynamic cross-section dimming for zero-match options,
partial-match fallback ordering, featured item pinning (maroon border, red tint, ★ badge,
capped at 3, JS-sorted by modDate), QC gate hiding items with empty reviewedBy from listing
(still URL-accessible), edit-mode banner showing QC-gated count.

**Bug just root-caused:** the widget's per-field access for format/category was returning
NULL because it referenced variable names (`format`, `category`) that don't exist on the
live content type schema. Confirmed correct current field names via REST API pull:

* Modality/format filter → `modality` (Checkbox field; values: inperson, async, sync)
* Category/type filter → `formatType` (Category field; tree CTL_Inst. Resource_Types)
* Topic pill cloud → `topics` (Category field; tree CTL_Inst. Resource_Topics)
* Frameworks (display-only badges, NOT filterable) → `frameworks` (Category field)

IMPORTANT: `formatType`, `topics`, and `frameworks` are Category fields, not
CheckboxMap/RadioMap. Category field access via Velocity/$dotcontent.pull() may behave
differently than direct attribute access — verify in isolation before assuming a rename
alone fixes the NULL bug (a Category field returning empty via `$!` looks identical to a
missing/mistyped variable name).

Full current field map for CtlInstructionalResource (confirmed via REST API, replaces any
older/stale references to resTitle, resAuthor, resContent, etc. — those field names no
longer exist):

| Variable | Field Type | Req? |
|----|----|----|
| resourceTitle | Text | Yes |
| urlTitle | Text | Yes |
| shortDescription | Textarea | Yes |
| thumbnail | Image | No |
| body | WYSIWYG | Yes |
| videoFile | File | No |
| embedUrl | Text (URL) | No |
| citations | WYSIWYG | No |
| modality | Checkbox | Yes |
| formatType | Category | Yes |
| topics | Category | Yes |
| frameworks | Category | No |
| author | Text | Yes |
| reviewedBy | Text | No |
| lastUpdated | Date | Yes |
| featured | Radio | No |
| relatedInstructionalResources | Relationship → CtlInstructionalResource | No |
| relatedEvents | Relationship → CtlActivity | No |
| relatedTechSupport | Relationship → CtlTechSupport | No |

Note: `videoFile` is a File field, which dotCMS represents as a **FileAssetMap** — the same
object type as Image fields (e.g. `thumbnail`), NOT a BinaryMap. Confirmed via live debug dump
2026-07-23: `.rawUri`/`.thumbnailUri`/`.resizeUri` do not exist on it and silently resolve empty
(the same "empty vs. wrong accessor" trap as the earlier format/category bug). Correct access is
`.path` + `.fileName` concatenation, identical to the Image-field pattern — e.g.
`$item.videoFile.path$item.videoFile.fileName`. This was root-caused by adding a temporary
EDIT_MODE debug panel that printed the raw Java object's toString(), which showed
`FileAssetMap@...[...fileName=..., path=...]` instead of the expected BinaryMap shape.

### CtlInstructionalResource — detail widget built and WORKING (resolved 2026-07-22)

`ctl-site_instRes-detail` now contains a real single-item detail page (previously this file
held a mis-named duplicate of the listing widget — that's fixed). Matches the URL slug
(last path segment) against `urlTitle` via pull-all + Velocity-side filter, same pattern as
CTL_widget_activity-detail. Host page: "Instructional Resource Detail" at
`/centers/ctl/instructional-resources/instructional-resource-detail`.

**Root cause of the 404 (finally resolved):** the URL Map Pattern
(`/centers/ctl/instructional-resources/{urlTitle}`) and Detail Page were both configured
correctly on the content type, but URLs still 404'd. The actual fix: the `urlTitle` field
itself needed **"System Indexed" and "Unique" checked** in its field settings — without
those, dotCMS's URL Map resolution can't bind to the field even though the pattern config
looks correct. See the new gotcha below — this will bite any future content type that gets
its own URL-mapped detail page (e.g. CtlTechSupport). This URL Map Pattern still needs
updating to `/centers/ctl/resources/course-design/{urlTitle}` to match the "For Course
Design" subpage rename — see Next Up.

### CtlActivity (unified events + opportunities) — live and working; full schema confirmed via REST pull this session

**Bug found in `ctl-site_opportunity-parent.html`, fixed 2026-08-04 during the
active/missed split below:** read `$opp.oppSubmissionDeadline`/`$opp.oppApplicationLink`,
neither of which exists on CtlActivity — same stale-field-name failure mode as the
format/category bug above. Corrected to the real fields, `signupDeadline`/
`registerApplyLink` (already used correctly in activity-detail.html). `oppAmountPaid` IS a
real field (Text, numbers only, `$` added by the template) — that one was never wrong.

**Second instance of the same bug, found 2026-08-04 in `ctl-site_event-parent.html`, fixed
the same day during the upcoming/past split below:** its "Register" button read
`$item.eventRegistrationLink`, which isn't a documented or REST-confirmed field on
CtlActivity and isn't used anywhere else in the project — the same stale-name failure as
the opportunity-parent bug, just a different fake name. Corrected to the real field,
`registerApplyLink`, matching `ctl-site_activity-detail.html`.

### Opportunities listing — split into two standalone widgets (2026-08-04), then relocated under Funding (same day)

Formerly one widget (`ctl-site_opportunity-parent.html`, now a deprecation stub) with two
sections (active, expired/"Missed Connections") sharing one single-column layout. Per
dev-checklist.md's reuse item, and following the same precedent as the Event Calendar split
just above, they became two independent widgets in `widgets/`, each re-pulling CtlActivity
and filtering to `isOpportunity` + its own active/expired date logic. When the flat "Funded
Opportunities" page was restructured into "Funding" > "Current Opportunities" / "Past
Recipients" later the same day, both widgets moved with their sections rather than staying
together on one page — see the Site Information Architecture section above:

1. **`widgets/ctl-widget_active-opportunities_current-opportunities.html`** — unchanged
   single-column layout (thumbnail, categories, deadline, amount, description, Apply/Details
   button). Also fixes the `oppSubmissionDeadline`/`oppApplicationLink` bug above. Now lives
   on the "Current Opportunities" subpage under "Funding" (renamed from the flat "Funded
   Opportunities" page).
2. **`widgets/ctl-widget_missed-opportunities_past-recipients.html`** — redesigned per
   explicit request: 2-column grid (`.ctl-missed-opps-grid`, collapses to 1 column under
   768px, same breakpoint as `.ctl-past-events-grid`) for space conservation, `oppAmountPaid`
   no longer shown on expired cards, `shortDescription` clamped to 2 lines via CSS
   `line-clamp` (same technique as Request Support's service description) with no expand
   toggle — each card's "View Details" button already reaches the full record. The section's
   collapsible show/hide toggle is preserved but rebuilt as an `addEventListener` binding
   (ids prefixed `ctlMissedOpps...`) instead of the original inline `onclick=`. Now lives on
   the new "Past Recipients" subpage under "Funding", alongside a second, not-yet-built
   widget listing actual past award recipients/winners (a distinct concept from this widget's
   expired-calls content) — see Next Up.

### Event Calendar listing — split into two standalone widgets (2026-08-04)

Formerly one widget (`ctl-site_event-parent.html`, now a deprecation stub) with two
sections (Upcoming Events, Past Events) sharing one layout. Per dev-checklist.md ("past
events should be less cluttered" / "multiple columns for past events"), the two sections
needed different layouts, so — same self-containment convention as the home page and About
Us splits — they're now two independent widgets in `widgets/`, each re-pulling CtlActivity
and doing its own upcoming/past date-split rather than sharing state:

1. **`widgets/ctl-widget_upcoming-events_events.html`** — unchanged single-column layout
   (thumbnail, date, time, location, description, Register/Details button). Also fixes the
   `eventRegistrationLink` → `registerApplyLink` bug above.
2. **`widgets/ctl-widget_past-events_events.html`** — new 2-column grid (`.ctl-past-events-grid`,
   collapses to 1 column under 768px) and pared-down tiles: name, short description, date,
   and a Details button only — no thumbnail, time, or location, so past tiles read as
   lighter/archival next to full upcoming tiles.

Live migration (create the 2 widgets in dotCMS, swap them in on the Events listing
page in place of the old single widget, delete the old widget) is still pending; see Next Up.

### CtlPersonnel — affiliation-gated About the CTL / Request Support sections (added 2026-07-29)

Three fields exist on CtlPersonnel beyond the original bio fields, confirmed live in both
`ctl_site_about-us.html` and `ctl-site_get-help.html`:

* `affiliations` — Checkbox (CheckboxMap), access via `.selectedValues.contains(...)`.
  **Option values are capitalized — `"CTL"`, `"FDC"`, `"Other"` — not lowercase.** A
  lowercase-value assumption silently matches nothing (`.contains()` is case-sensitive) —
  same "quiet empty result" failure signature as the format/category bug, confirmed via a
  live REST pull 2026-07-29 after the lowercase assumption first failed silently.
* `websitePromo` — Text (URL). Was About Us-only with Get Help's staff cards intentionally
  omitting it (pre-2026-08-04); as of the Get Help split below (page since renamed Request
  Support), it now renders on both pages — see the widget-reuse tradeoff note there.
* `buttonLabel` — Text, label for the `websitePromo` button, falls back to "Visit My
  Website" when empty. Distinct from CtlService's `serviceButtonLabel` — don't conflate.

About Us (now About the CTL) originally rendered three affiliation-gated sections — CTL
Staff, FDC Members, Other Affiliates — each independently sorted by `rank` (see the rank
root-cause writeup in Gotchas below); Other Affiliates was later dropped from the IA
entirely (see the Site Information Architecture section above). Request Support's "Make an
Appointment" section only shows personnel with `CTL` checked in `affiliations`.

**About Us split into two standalone widgets (2026-08-04)**, per dev-checklist.md ("split
up sections into multiple widgets for potential reuse") — formerly one combined widget
(`ctl_site_about-us.html`, now a deprecation stub), now:

* **`widgets/ctl-widget_ctl-staff-profiles.html`** — CTL-affiliated personnel only, full
  single-column bio card (photo, expertise/help bullets, absolutely-positioned actions),
  matching CTL_widget_get-help's Staff section card treatment exactly. Heading is NOT
  hardcoded — reads a per-instance `sectionHeading` widget field, defaulting to "Meet the
  CTL Staff" (its subpage's current name, renamed from "CTL Staff" in the second IA revision
  round) — because this same widget is now also reused on Request Support (see below).
* **`widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html`** — FDC Members + Other Affiliates
  together (both already share the same compact multi-column grid card layout, so they
  stayed in one widget rather than three).

Same self-containment convention as the home page carousel split: each widget re-pulls
CtlPersonnel and duplicates its own `<style>` block and rank-sort/pronouns-lookup logic
rather than sharing via an include (this dotCMS instance has no general-purpose
cross-widget include mechanism). Splitting also let the old `$isListLayout` branch be
deleted outright in both new widgets, since each widget now renders only one card layout
instead of switching between two.

**Get Help split into two widgets (2026-08-04)**, same dev-checklist.md item — formerly one
combined widget (`ctl-site_get-help.html`, now a deprecation stub), now (page renamed
"Request Support" the same day):

* **`widgets/ctl-widget_our-services.html`** — "Our Services" (CtlService): 2-line-clamped
  expandable description, then a "Next Steps" section (see the reworked-card note below,
  2026-08-06) in place of the original availability badge + top-right access button.
* **"Make an Appointment" is NOT a new widget file.** Per user decision 2026-08-04, Request
  Support places a SECOND content instance of `widgets/ctl-widget_ctl-staff-profiles.html`
  (same dotCMS Widget content type/Code, different content record) with its `sectionHeading`
  field set to "Make an Appointment" instead of "Meet the CTL Staff". Accepted tradeoff: the
  "Visit My Website" button now shows on Request Support's staff cards too (previously
  intentionally omitted there) — reusing one widget won out over maintaining two
  near-duplicate ones. Requires adding the `sectionHeading` field to that widget's content
  type in dotCMS if not already present (see that file's header note) — still pending,
  along with creating both new widget records/instances and swapping them onto the Request
  Support page; see Next Up.

`pronouns` is a Radio field (RadioMap), not Text as first assumed — access via
`.selectValue`, then map to its display label via the parallel `.values`/`.options` arrays
(same pattern as the `modality` Checkbox). `"dont show pronouns"` is a real selectable
option, not blank — treat it as equivalent to blank in display logic (hide the
parenthetical). This is a second instance of the general pattern: a field can have a real
option whose meaning is "show nothing," which looks identical to blank/unset at the
template layer unless you check for it explicitly.

### CTL homepage — split into three standalone widgets (2026-08-04)

Must live in a dotCMS Widget, not a generic "Web Page Content" block — pasting the same
code into a Web Page Content block rendered nothing at all (not even the plain-HTML tile
grid), while a Widget rendered correctly. Matches how every other piece of Velocity in this
project already lives in a Widget (CtlWidgetActivityDetail, CtlWidgetInstructionalParent,
CtlWidgetInstDetail, EventCalendarWidget, CtlWidgetOpportunitiesParent) — Web Page Content
blocks apparently don't evaluate Velocity in this instance (or run it through a sanitizer
that strips it).

Formerly one monolithic widget (`ctl-site_home.html`); per dev-checklist.md ("split up
sections into multiple widgets for potential reuse"), now three independent widgets in
`widgets/`, each self-contained (own CSS, own JS) so any one can be dropped on another page
without the others:

1. **`widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html`** — welcome text + tile grid
   linking to the other 5 top-level site sections. Originally 6 tiles; updated in the second
   IA revision round (same day) to 5, dropping the standalone "Instructional Resources" and
   "Technology Support" tiles (now nested under the "Resources" folder tile) and relabeling
   "Opportunities" → "Funding", "About Us" → "About the CTL", "Event Calendar" → "Events",
   and "Get Help"/"Make an Appointment" → "Request Support".
2. **`widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html`** — pulls CtlActivity, filters to
   `isEvent`/`isOpportunity` via pull-all + `.selectedValues.contains("true")`, computes a
   per-record relevant date (`eventStartDate`/`eventEndDate` for events, `signupDeadline`/
   `submissionDeadline` for opportunities — `activityDate` never existed as a field),
   filters to future-dated items, bubble-sorts ascending, renders each as a card with a
   badge that handles the both-event-and-opportunity case, linking to
   `/centers/ctl/activities/{urlMapPattern}`.
3. **`widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html`** — pulls CtlInstructionalResource,
   filters to `featured.selectValue == "yes"` AND non-empty `reviewedBy` (same QC gate as
   the listing widget), renders using the exact same card markup/classes (`ctl-res-card`,
   format badges, topic chips minus the 6 parent-category names) as the Instructional
   Resources listing widget, so featured cards look identical there and on the homepage.

**Display pattern for both carousels:** NOT a Bootstrap `.carousel` (that component only
supports one slide at a time via JS transitions, with no way to peek at a partial next card
or free-scroll). Instead both use a `.ctl-hscroll` / `.ctl-hscroll-item` pair — a flex row
with `overflow-x: auto` + `scroll-snap-type: x mandatory`, no auto-play, user scrolls
manually (trackpad/touch/scrollbar). Responsive via media queries: ~1.1 cards visible on
mobile, scaling up to ~2.3 cards at standard/max desktop width (2 full + a peek of the 3rd,
by design — signals scrollability without needing arrows) and staying there at all wider
widths too, since the container itself caps out. This CSS (and the button-scroll JS) is now
**duplicated in both carousel widgets on purpose**, not factored into a shared include —
this dotCMS instance has no established mechanism for sharing Velocity/CSS/JS across widget
content assets (`#parseContainer` is container-specific, not a general-purpose include), and
self-containment is the point of the split. Each widget's button-binding script guards
against double-binding buttons via a `data-ctl-hscroll-bound` flag, since both carousels
render on the home page together and would otherwise double-query `.ctl-hscroll-btn` and
attach duplicate click handlers.

Fixed along the way (still true post-split): `$featuredResources`/`$featResources` typo
(Featured Resources could never show real data before), stale `resourceUrl`/
`resourceThumbnail` fields (real ones are built from `urlTitle`/`thumbnail`), Radio-field
`featured` query switched from Lucene to the same pull-all + `.selectValue` pattern the
listing widget uses. Raw always-visible debug `<p>` tags replaced with `$EDIT_MODE`-gated
debug lines at each pull/filter/sort checkpoint (editor-only, invisible to real visitors).

`ctl-site_home.html` itself is now a deprecation stub pointing at the three new files —
live migration (create the 3 widgets in dotCMS, swap them in on the Home page, delete the
old widget) is still pending; see Next Up.

### Shared stylesheet — consolidated 2026-08-05, live and working

Every widget's embedded `<style>` block (per-widget, copy-pasted, prone to drift — see the
several mismatches found below) is consolidated into one file, `styles/ctl-widgets.css`.
All 11 widgets that had a `<style>` block (`ctl-site_activity-detail.html`,
`ctl-site_instRes-detail`, `ctl-site_instRes-parent.html`,
`ctl-widget_ctl-staff-profiles.html`, `ctl-widget_fdc-member-grid.html`,
`ctl-widget_featured-instructional-resources.html`, `ctl-widget_home-nav-tiles.html`,
`ctl-widget_missed-opportunities.html`, `ctl-widget_our-services.html`,
`ctl-widget_past-events_events.html`, `ctl-widget_upcoming-activities-carousel.html`) have
had that block removed in favor of the shared file.

Because CTL no longer has its own custom page template (see Overview), there's no single
shared `<head>` to put one sitewide `<link>` in. Solution: CTL already has a sitewide
content block (contact info/hours, rendered in the nav area) that's a single shared content
record referenced by every CTL page's container — a `<link rel="stylesheet" href="...">`
tag was added directly into that existing block rather than building a dedicated loader
widget, so one edit to one content record covers every page. (An earlier version of this
plan used a dedicated `ctl-widget_shared-styles.html` loader widget added per-page — that
approach is superseded now that the existing shared block covers the same need with zero
per-page work, and the file has been deleted.) Confirmed live and rendering correctly
2026-08-06.

Several drifted/mismatched styles were found and documented (with reasoning) directly in
`styles/ctl-widgets.css`'s comments while consolidating — not silently unified, since
picking a winner is a design call:
* The 4 activity/event listing widgets (`active-opportunities`, `missed-opportunities`,
  `upcoming-events`, `past-events_events`) use Bootstrap's default `btn btn-default btn-sm`
  for their Apply/Register/Details buttons, while every other widget uses the maroon
  outline `.ctl-btn` — buttons look different on those 4 pages vs. everywhere else.
* `ctl-site_instRes-detail`'s topic chips (`.ctl-ir-topic-chip`, grey tag, square corners)
  and format badges (`.ctl-ir-tag`) render differently than the same data does on the For
  Course Design listing / homepage carousel (`.ctl-res-topic-chip`, white pill, rounded —
  `.ctl-res-fmt-badge`) — same resource, different chip shape/size depending which page
  you're on.
* `ctl-widget_missed-opportunities.html`'s "Expired" badge and category tags are inline
  `style="..."`, not `.ctl-badge-closed`/`.ctl-tag` — values have drifted slightly (smaller,
  bolder, different border) from those shared classes.
* `ctl-site_instRes-detail`'s "Related Resources" row uses the same hscroll/scroll-snap
  technique as the homepage carousels but has no prev/next scroll buttons.

Also worth knowing: most of each widget's actual *layout* (card borders/shadows/padding,
spacing) was never a class in the first place — it's raw inline `style="..."` on the HTML.
The shared stylesheet can't touch that (and inline styles win specificity over it anyway).
A repeated single-column "listing card" box (identical inline values across 4 widgets) was
pulled into a new `.ctl-listing-card` class in the stylesheet as a proposed target for a
future pass, but no widget references it yet — linking the stylesheet alone changes
nothing there until that swap happens.

Still open: decide whether/how to resolve the flagged mismatches above (see
dev-checklist.md).

### Our Services — reworked to a "Next Steps" section, code done, field pending (2026-08-06)

User feedback: cards all looked like clickable tiles even though only some services
actually had a link (via the old top-right "Access this Service" button); the "Available
Now" badge was redundant since it showed on effectively every card and conveyed nothing.
Reworked `widgets/ctl-widget_our-services.html` per user-confirmed decisions:

* **Badge removed entirely** (no more "Available Now"/"Currently Unavailable" markup or
  `.ctl-gh-badge` CSS — deleted from `styles/ctl-widgets.css` as unused, confirmed via
  grep it wasn't referenced anywhere else).
* **New in-card "Next Steps" section** (`.ctl-gh-svc-next-steps`, styled in the shared
  stylesheet) replaces the old absolutely-positioned top-right button. It holds whichever
  of two things a service has: the existing access button (`serviceAccessLink` +
  `serviceButtonLabel`, unchanged fields/logic) and/or a new freeform note — for services
  that are just text (drop-in hours) or a jump-link phrase pointing elsewhere on the page
  (e.g. One-on-One Consultations' "booking links below" → the Make an Appointment
  section). Renders both together if both are filled in; the whole section is omitted if
  neither applies.
* **`isCurrentlyAvailable` kept**, but its scope widened: it now gates the entire Next
  Steps section (button AND note), not just the button as before — user-confirmed
  2026-08-06, since an unavailable service showing "drop in Tuesdays 1-3pm" would be
  misleading. An unavailable service still shows its name/description, just no Next Steps
  content beneath it.
* **New field needed:** a freeform Next Steps note field on CtlService — **added in live
  dotCMS 2026-08-12 as `nextSteps` (Textarea)**, not `nextStepsNote`/WYSIWYG as originally
  planned here; see the 2026-08-12 entry below for the corrected field name and the
  `longDescription` field that came with it.

**Converted single-column list to a tile grid (2026-08-07):** per user feedback, cards moved
from `.ctl-gh-svc-list` (flex column, one per row, thumbnail beside content) to the same
`row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4` Bootstrap grid pattern the For Course Design
resource cards use — full-width thumbnail on top (`.ctl-gh-svc-thumb`, now 100%×160px like
`.ctl-res-thumb`, was 100×80px side-by-side), `.ctl-gh-svc-next-steps` pinned to the bottom of
each tile via `mt-auto` on a flex-column card-body so tiles with different description
lengths still line up evenly. Deliberately did NOT add `.ctl-hover-lift`/`.stretched-link`
(the whole-tile-clickable treatment from the same day, see the section above this one) —
unlike resource/event/opportunity tiles, a service tile has no single consistent link target
(the Next Steps button is optional, external, and not every service has one), so a
whole-card link would be misleading. `#ctlGhSvcList`'s id moved from the old flex wrapper to
the new row div.

**Read-more toggle removed, same day, follow-up:** the 2-line description clamp (all tiles
were truncating) is gone — `serviceShortDescription` now always renders in full. Deleted the
"Read more" button, its click-to-expand JS block, and the now-dead
`.ctl-gh-svc-desc`/`.is-expanded`/`.ctl-gh-svc-toggle` CSS. `.ctl-missed-opp-desc` (Past
Recipients' expired-opportunity clamp) is unaffected — it was sharing the same clamp rule as
`.ctl-gh-svc-desc` before this, now split into its own rule.

**Per-tile expand/collapse added, field names corrected (2026-08-12):** the user added the
CtlService fields to live dotCMS and confirmed the real schema via a REST pull of
`/api/v1/contenttype/id/CtlService`. The freeform Next Steps note field was actually built
as **`nextSteps`** (Textarea), not `nextStepsNote` as the 2026-08-06 rework above had
planned/coded against — the widget's `$!s.nextStepsNote` references have been corrected to
`$!s.nextSteps` (the "New field needed" line above is now stale; the field exists under a
different name than first written). A second, new field also came out of this pull:
**`longDescription`** (WYSIWYG) — not something earlier notes anticipated. Per user request,
each tile with a non-empty `longDescription` now gets a "Learn More" toggle; clicking it
expands that tile to span the full grid width and reveals the long description, collapsing
back to the shared tile size on toggle again. This required switching
`#ctlGhSvcList` from Bootstrap's `row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4` to a plain
CSS Grid class, **`.ctl-gh-svc-grid`** (3/2/1 columns at the same breakpoints, defined in the
shared stylesheet next to `.ctl-past-events-grid`/`.ctl-missed-opps-grid`) — flexbox row-cols
has no clean way to let one item span the full row while its siblings keep their normal
width, so this is another instance of the project's standing exception (custom CSS over
fighting the Bootstrap grid). The old per-tile wrapper `.col` is now `.ctl-gh-svc-tile`;
expansion is `.ctl-gh-svc-tile.is-expanded { grid-column: 1 / -1; }`. Toggle JS follows the
same `dataset.ctlXBound` double-bind guard as the homepage carousels' hscroll buttons.

### New `content blocks/` folder — static intro/overview copy split out of `widgets/` (2026-08-07)

Per user decision 2026-08-07: static intro/overview/blurb copy (welcome text, L1
landing-page overview text + down-link tiles, stat tiles) is conceptually "content," not a
"widget," in dotCMS terms — these are meant to be authored as Web Page Content blocks, not
Widget content type/Code assets. A new top-level folder, `content blocks/` (sibling to
`widgets/`), now holds these files, renamed from `ctl-widget_...` to `ctl-content_...` in
the same move:

* `content blocks/ctl-content_events-overview_events.html` (was `ctl-widget_events-overview_events.html`)
* `content blocks/ctl-content_funding-overview_funding.html` (was `ctl-widget_funding-overview_funding.html`)
* `content blocks/ctl-content_impact-reports-intro.html` (was `ctl-widget_impact-reports-intro.html`)
* `content blocks/ctl-content_outcomes-overview.html` (was `ctl-widget_outcomes-overview.html`)
* `content blocks/ctl-content_resources-overview.html` (was `ctl-widget_resources-overview.html`)
* `content blocks/ctl-content_fdc-description.html` (was `ctl-widget_fdc-description.html`) — still an empty stub, not yet built
* `content blocks/ctl-content_services-overview.html` (was `ctl-widget_services-overview.html`) — still an empty stub, not yet built

`widgets/ctl-widget_reports-grid.html` (the third piece of the Impact & Reports subpage,
alongside two of the files above) stays in `widgets/` — it pulls live CtlReport data via
`$dotcontent.pull()`, so it's a real widget, not static copy. This is the dividing line
between the two folders going forward: no `$dotcontent.pull()`/listing/detail logic → `content
blocks/`; anything that pulls or filters content → `widgets/`.

`widgets/ctl-widget_home-nav-tiles.html` (homepage welcome text + nav tile grid) was
deliberately NOT moved despite being similarly static — it predates this split (part of the
original 2026-08-04 homepage 3-way split) and is already deeply woven into the homepage
migration plan below under its `widgets/` path; revisit if the content/widget line should be
drawn more strictly later.

See dev-checklist.md's widget table (rows 14a/14b/15/16/17 updated, rows 18/19 added) and
the Variable Map's new "Content Blocks Index" section for the full current mapping.

### Whole-tile-clickable pass on `.ctl-hover-lift` tiles (2026-08-07)

User feedback: any tile that already carries the lift-on-hover animation (`.ctl-hover-lift`
in the shared stylesheet, or `.ctl-nav-tile`'s equivalent inline rule) should be clickable
across its entire surface, not just its title text — the hover animation implies the whole
card is a link. Audited every `.ctl-hover-lift`/`.ctl-nav-tile` usage in the codebase against
this rule:

* **Already fully clickable, no change needed:** home nav tiles
  (`ctl-widget_home-nav-tiles.html`) and the Funding/Resources landing-page down-link tiles
  (`content blocks/ctl-content_funding-overview_funding.html`,
  `ctl-content_resources-overview.html`) — all three wrap the entire card in a single `<a>`.
  The homepage carousel (`ctl-widget_upcoming-activities-carousel.html`), the For Course
  Design resource grid (`ctl-site_instRes-parent.html`), and the resource detail page's
  Related Resources row (`ctl-site_instRes-detail`) already used Bootstrap's
  `.stretched-link` on the title anchor, which stretches that link to the card's full
  bounds — also no change needed.
* **Fixed:** the 4 event/opportunity listing widgets — `ctl-widget_active-opportunities.html`,
  `ctl-widget_missed-opportunities.html`, `ctl-widget_upcoming-events.html`,
  `ctl-widget_past-events_events.html` — had a `.ctl-listing-title-link` on the title only,
  with a separate Apply/Register/Details `.btn` doing the rest of the card's clicking. Added
  `.stretched-link` to each title anchor, plus `position: relative` on `.ctl-listing-card`/
  `.ctl-listing-card-compact` (required for `.stretched-link`'s overlay to anchor to the card
  instead of some further-out ancestor) and a `position: relative; z-index: 2;` rule on `.btn`
  inside those two card classes (all in `styles/ctl-widgets.css`) so the button stays
  independently clickable on top of the stretched overlay instead of being visually present
  but dead. For Missed Connections/Past Events, the button already pointed at the same URL as
  the title, so this only widens the click target; for Current Opportunities/Events Upcoming,
  the button can point to an external `registerApplyLink` instead — clicking anywhere else on
  those cards now goes to the detail page while the button still applies/registers externally.

## Next Up / On the Horizon

* **Rename/restructure pages and folders in live dotCMS to match the new IA** (see "Site
  Information Architecture" above) — this is a prerequisite for the migration items below,
  which now target the new page names/hierarchy: Home → Welcome to the CTL, Get Help →
  Request Support, Event Calendar → Events, Opportunities → Funding (with new "Current
  Opportunities" and "Past Recipients" subpages, replacing the flat "Funded Opportunities"
  page from the first rename round), Instructional Resources moves under a new Resources
  folder as "For Course Design" (with "For Technologies" reserved alongside it for
  CtlTechSupport once built), About Us becomes an About the CTL landing page (now carrying
  the mission/vision copy directly, no separate "Our Mission & Outcomes" subpage) containing
  Meet the CTL Staff / Faculty Development Committee / Impact & Reports subpages
* **Build the "Funding" landing page content** (new, not yet built) — L1 overview of
  financial support, grants, and awards; links down to Current Opportunities and Past
  Recipients
* **Build the "Past Recipients" award-winners widget** (new, not yet built) — a genuinely
  new concept (who won past grants/fellowships/awards), distinct from the relocated
  Missed/Expired Opportunities widget it sits alongside on the same subpage; needs its own
  data modeling (likely a new content type or new CtlActivity fields) — not yet scoped
* **Build the "Impact & Reports" subpage/widget** (new, not yet built) — annual reports,
  track record, accomplishments, institutional data, under About the CTL
* **Write mission/vision overview copy for the About the CTL landing page itself** — this
  replaces the old plan to build a separate "Our Mission & Outcomes" subpage/widget (that
  subpage was dropped in the second IA revision round); reuses the old About Us intro/
  welcome paragraph as a starting point
* **Migrate the Welcome to the CTL page in live dotCMS to the 3 new split widgets** — create
  Widget content assets from `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html`
  (now 5 tiles, not 6 — see that file's header), `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html`, and
  `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html`, place all three on the page in
  that order in place of the old single widget, confirm live, then delete the old widget and
  remove the now-superseded `ctl-site_home.html` stub from the repo
* **Migrate About the CTL's Meet the CTL Staff and Faculty Development Committee subpages in live
  dotCMS** — create Widget content assets from `widgets/ctl-widget_ctl-staff-profiles.html`
  (placed on the Meet the CTL Staff subpage) and
  `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` (placed on the
  Faculty Development Committee subpage — FDC-only now, Other Affiliates was dropped from
  the IA), confirm live, then delete the old combined About Us widget and remove the
  now-superseded `ctl_site_about-us.html` stub from the repo
* **Migrate the Events listing page in live dotCMS to the 2 new split widgets** —
  create Widget content assets from `widgets/ctl-widget_upcoming-events_events.html` and
  `widgets/ctl-widget_past-events_events.html`, place both on the listing page in that order in
  place of the old single widget, confirm live (upcoming/past split correct, past grid is
  2-column, past tiles show no time/location, detail links point to `/centers/ctl/events/`),
  then delete the old widget and remove the now-superseded `ctl-site_event-parent.html` stub
  from the repo
* **Migrate the Request Support page in live dotCMS to the 2 new split widgets** — add a
  `sectionHeading` Text field to the CTL Staff profiles widget's content type (if not already
  present), create a Widget content asset from
  `widgets/ctl-widget_our-services_request-support.html`, then create a SECOND content record of the
  CTL Staff profiles widget (same Code as the Meet the CTL Staff subpage instance) with `sectionHeading` = "Make an
  Appointment". Place both — our-services, then the staff-profiles/Make an Appointment instance
  — on the Request Support page in place of the old single widget, confirm live (including the new
  "Visit My Website" button now appearing on Request Support's staff cards, an accepted tradeoff of
  reuse — see the CtlPersonnel section above), then delete the old widget and remove the
  now-superseded `ctl-site_get-help.html` stub from the repo
* **Migrate the Funding page in live dotCMS to its own overview page plus the 2 relocated
  listing widgets** — create the new "Funding" (L1) and "Current Opportunities" / "Past
  Recipients" (L2) pages; create Widget content assets from
  `widgets/ctl-widget_active-opportunities_current-opportunities.html` (Current Opportunities
  subpage) and `widgets/ctl-widget_missed-opportunities_past-recipients.html` (Past
  Recipients subpage, alongside the not-yet-built award-winners widget above), confirm live
  (deadline/Apply link now populated correctly, Missed Connections toggle still works,
  2-column grid, amount hidden, description clamped to 2 lines, detail links point to
  `/centers/ctl/funding/current-opportunities/` and `/centers/ctl/funding/past-recipients/`
  respectively), then delete the old flat "Funded Opportunities" page/widget and remove the
  now-superseded `ctl-site_opportunity-parent.html` stub from the repo
* **Update the CtlInstructionalResource content type's URL Map Pattern** in live dotCMS to
  `/centers/ctl/resources/course-design/{urlTitle}` (formerly
  `/centers/ctl/instructional-resources/{urlTitle}`) to match the "For Course Design"
  subpage rename — remember the field also needs "System Indexed" and "Unique" checked (see
  the Gotchas entry below) if that ever needs re-confirming after the path change
* **Verify `ctlAttachmentfile` downloads actually work** — `ctl-site_activity-detail.html`
  and `ctl-site_instRes-detail` both still access it via `.rawUri`/`.thumbnailUri`
  (BinaryMap accessors) on what is a **File**-type field. `videoFile` looked identical on
  paper and turned out to need `.path`/`.fileName` (FileAssetMap) instead — `ctlAttachmentfile`
  has never been confirmed working with a live debug dump the way `videoFile` was. If
  Downloads links/thumbnails are ever silently blank, check this first.
* CtlTechSupport content type (not yet built — see naming note above)
* Delete orphaned widgets: ctl_widget_detail-router, CTL_widget_event-calendar_detail,
  CTL_widget_opportunities_detail (reference deleted CtlEventItem/CtlOpportunity)
* Format/category fix in CtlInstructionalResource listing widget (v8) is confirmed applied
  in code (verified 2026-08-04) — still pending a live-URL check
* Fix CtlAttachment thumbnail letterboxing — need a crop-based URL pattern instead of thumbnailUri
* Clean up leftover `$EDIT_MODE` debug scaffolding once the rank-sort fix has been confirmed
  stable live — heaviest in `ctl-site_get-help.html` (visible debug panels with per-record
  `.class.name` reflection output), lighter in `ctl-site_home.html` and `ctl_site_about-us.html`.
  Harmless to leave (invisible to real visitors) but worth trimming.
* `relatedTechSupport` (CtlInstructionalResource field) exists on the schema but isn't
  rendered anywhere yet — not a bug, just unbuilt (CtlTechSupport itself doesn't exist yet
  either, so there's nothing to relate to)

## Critical Instance-Specific Gotchas (this dotCMS instance specifically)

* `$dotcontent.pull()` sort-by-field returns empty results — always sort by "score", do real sorting in JS
* `$navTool` unavailable
* Page editor preview uses a different internal API path than the live URL — always test via
  live URL + view-source, not the editor preview
* Don't use /dA/, /dotAsset/, /convert/image?id=, /contentAsset/raw-data/ for images — none work here
* Image field → FileAssetMap: `src="$item.field.path$item.field.fileName"`, check existence
  with `#if("$!item.field.fileName" != "")`
* **File**-type field (dotCMS field type "File", e.g. `videoFile`) → also a **FileAssetMap**,
  same as Image fields — use `.path`/`.fileName` (NOT `.rawUri`/`.thumbnailUri`/`.resizeUri`,
  which don't exist on it and silently resolve empty). Confirmed 2026-07-23 via a live
  EDIT_MODE debug panel printing the raw object's toString() — it showed
  `FileAssetMap@...[map={fileName=..., path=...}]`, not a BinaryMap. An earlier version of this
  doc wrongly generalized "Binary/File field → BinaryMap, use `.rawUri`" — that only holds for
  actual **Binary**-type fields (dotCMS field type "Binary", embedded/private to the content
  record), which really is a different field type from "File" despite the similar name and
  despite both storing an uploaded file. Don't assume the two are interchangeable — verify
  which one a field actually is before choosing an accessor.
* Binary-type field (dotCMS field type "Binary") → BinaryMap: use `.rawUri`, `.name`,
  `.thumbnailUri`, `.resizeUri` (NOT `.path`/`.fileName`) — confirm the field is actually typed
  Binary (not File) before relying on this pattern; see the File-type note above.
* CheckboxMap → `.selectedValues` (parallel options/values arrays for labels)
* RadioMap → `.selectValue`
* Category fields → different access pattern than Checkbox/Radio, verify in isolation
* Date fields are java.sql.Timestamp — use `.getTime()` for epoch comparisons
* Velocity's `>`/`<` comparison operators only reliably work Number-vs-Number in this
  engine — comparing two Strings silently never evaluates true, so a bubble sort built on
  String keys just never swaps and quietly keeps pull order. Symptom looks like "sorting
  did nothing" (e.g. a higher-rank item still appearing first), not an error.
  Root-caused 2026-07-28 on the `rank` sort in both `ctl-site_get-help.html` and
  `ctl_site_about-us.html` — **root cause was self-inflicted, not an engine limitation.**
  `$item.rank` (CtlPersonnel's sort-order field) is labeled "Text ... whole number" in the
  dotCMS editor, but at the Velocity layer it's already a real `java.lang.Long` — confirmed
  via `.class.name` in a live `EDIT_MODE` debug dump. Two dead-end fix attempts happened
  before this was known: `#set($n = $rawStringValue + 0)` and then `* 1`, both trying to
  coerce a *String* back into a Number — but the String was never necessary; it was created
  by our own code doing `#set($rawRank = "$!p.rank")` first (quoted-string interpolation
  always stringifies, regardless of underlying type). That reverse String→Number conversion
  silently failed both times (String arithmetic throws in this engine's `MathUtils`, which
  makes the enclosing `#set` a no-op — the variable is left completely UNSET, not null/zero).
  Proof it was silently failing: downstream references printed as literal unresolved tokens
  like `$debugKey` in the rendered page — that only happens when a variable was never
  successfully `#set`, and is a reliable tell for "this #set threw and got swallowed."
  **Fix:** don't assume a field's runtime type from its dotCMS editor label — check
  `$item.field.class.name` via a live debug dump FIRST. If it's already a Number, use it
  directly as the sort key; skip any String round-trip entirely (only the blank-check needs
  the stringified `"$!item.field"` quiet-reference form). Treat blank as a large sentinel
  Number (e.g. `999999`), not a padded string, so it still sorts last correctly.
* `$!` quiet reference hides empty fields AND wrong variable names identically — a typo'd
  variable name is silently indistinguishable from an empty field (this caused the
  format/category bug above — always verify field names against a fresh REST API pull,
  not memory or older docs)
* Checkbox/Category option VALUES can be case-sensitive against `.contains()` checks —
  e.g. CtlPersonnel's `affiliations` field stores `"CTL"`/`"FDC"`/`"Other"` (capitalized),
  not lowercase. A lowercase-value assumption silently matches nothing — same "quiet empty
  result" failure signature as a wrong field name, just one level deeper (right field,
  wrong value casing). Confirm actual option values via REST pull, don't guess casing.
* A Radio/Checkbox field can have a real selectable option whose meaning is "show
  nothing"/"hide this" (e.g. CtlPersonnel's `pronouns` field has a `"dont show pronouns"`
  option) — this looks identical to blank/unset at the template layer unless checked for
  explicitly. Treat it as equivalent to blank in display logic; don't assume blank is the
  only "don't render this" state.
* The old custom page template's secondary nav (`DEPRECATED/ctl-site_page template.html`)
  was data-driven, not hard-coded — it pulled `+contentType:htmlpageasset
  +path:/centers/ctl/*` and included a page only if its own `showOnMenu` Checkbox field (a
  page-level field, not a Ctl content type field) contained `"true"`. That template is no
  longer in use (see the Overview section — CTL pages now use a built-in dotCMS template
  instead), so this logic no longer runs; kept here as historical context in case the
  built-in template's own secondary nav needs the same `showOnMenu`-gating behavior
  reproduced.
* `urlMapPattern` not indexed for Lucene — pull-all + Velocity-side filter instead
* URL Map Pattern fields (the field referenced in a content type's `{fieldVar}` URL Map
  Pattern, e.g. `urlTitle`) must have **"System Indexed" AND "Unique" checked** in the
  field's own settings, or dotCMS's URL Map resolution silently fails to bind — matching
  URLs 404 at the routing layer (before any Velocity even runs) even though the URL Map
  Pattern + Detail Page are both configured correctly on the content type. This cost a long
  debugging session on CtlInstructionalResource's `urlTitle` field — check this FIRST for
  any future URL-mapped content type (e.g. CtlTechSupport)
* Hyphenated slugs can fail field-scoped Lucene queries — Velocity-side filter workaround
* Use `+path:` not `+url:` in content queries
* `$uri.substring($uri.lastIndexOf('/') + 1)` silently kills template render (integer boxing bug) — fix:

  ```
  #set($idx = $uri.lastIndexOf('/'))
  #set($idxPlus = $idx.intValue() + 1)
  $uri.substring($idxPlus)
  ```
* Relationship fields are pre-hydrated — no separate pull needed
* Velocity array assignment: `#set($list[$i] = $value)`
* This dotCMS's Velocity engine does NOT support map literals (`#set($x = {"k": v})`) —
  confirmed via live bisection debugging on the homepage carousel: using one blanked the
  entire page render with no error message, even in sections before the map literal in the
  file. Use parallel arrays instead (one array per "field" you'd have put in the map)
* `$foreach.count` is NOT supported either (a Velocity 2.x feature) — use a manually
  incremented counter (`#set($i = $i + 1)`) instead, consistent with this project's existing
  preference for hand-rolled loop state over trusting engine implicit behavior
* Inline arithmetic directly inside a `.get(...)` call or `[...]` index — e.g.
  `$list.get($i - 1)` or `$list[$i - 1]` — silently fails to render past that point. `#set`
  the computed index to its own variable FIRST (`#set($idx = $i - 1)`), then use that plain
  variable in the `.get()`/`[]` call. Arithmetic directly in a `#set` right-hand side or an
  `#if` condition is fine — it's specifically inline arithmetic as a method/index argument
  that breaks
* Velocity's `[m..n]` range operator produces a DESCENDING list when `m > n` (e.g.
  `[1..0]` evaluates to `[1, 0]`, two elements — NOT an empty range like most languages'
  range semantics). A hand-rolled bubble sort using `[1..$n]` / `[1..($n - $i)]` hits this
  on its last outer pass for ANY `$n` (not just `$n == 0`), producing an out-of-bounds
  `.get(-1)` call that crashes the whole render. Fix: use fixed ranges (e.g. `[0..99]`)
  with explicit `#if` bounds guards instead of deriving range bounds from a value that can
  reach zero or go negative — this is the pattern `ctl-site_opportunity-parent.html`'s
  bubble sort already used safely; `ctl-site_home.html`'s carousel sort did not, and it
  cost a long blank-page debugging session before being fixed to match
* CtlEventItem/CtlOpportunity are deleted, replaced by unified CtlActivity
  (isEvent/isOpportunity checkboxes). CTL_widget_activity-detail is the live detail widget
* Combine listing+detail Velocity logic into one widget (slug switches view) — Constant
  Field widgets don't evaluate Velocity on URL-mapped detail pages
* Vanity URL routing across two prefixes → one detail page: use
  `$request.getAttribute("javax.servlet.forward.request_uri")`
* Vanity URL regex: `(.+)` not `(.*)`, plus negative lookahead `(?!index\.dot$)` to avoid
  catching bare listing URLs
* Unicode bold/circled numbers/emoji/Braille Pattern Blank (U+2800 as invisible spacer for
  required-but-empty Line Divider fields) all work in field labels/hints
* Use Category fields (not Tag fields) for vocabularies — Tag fields have limits
* Category import CSV needs name, key, variable, sort columns
* dotCMS REST API: base `https://suny-auth.dotcms.cloud/api/v1/contenttype?per_page=500`;
  single type `/api/v1/contenttype/id/{variableName}`; being logged into admin in-browser
  is sufficient auth; fields nested in layout → columns → fields, check `fieldType == "Tab"`
  for tab boundaries; large JSON responses work well as RTF attachments

## Working Approach (carry these over)

* Isolate before building: test with `$list.size()` debug pull first, add markup only after
  data confirmed
* Debug sequentially/via bisection — narrow down before guessing fixes
* Test via live URL + view-source, never editor preview
* Prefer complete file outputs after major changes, not partial snippets
* Prefer plain text output for copy-paste into existing docs (not generated files)
* Prefer existing Bootstrap classes over custom CSS; avoid !important — build standalone
  classes (like .ctl-btn) when theme conflicts arise
* Prefer Velocity-side filtering over Lucene queries for unreliable-in-index fields
* Use `$!` quiet reference syntax throughout, but verify field/variable names against a
  fresh REST API pull when something returns unexpectedly empty — don't assume it's just
  empty data


