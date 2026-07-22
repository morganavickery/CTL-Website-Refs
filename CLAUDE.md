# CTL Website Rebuild — Project Context

## Overview

Building the SUNY Cortland CTL (Center for Teaching and Learning) website rebuild in dotCMS,
at /centers/ctl/. Built from Figma mockups using Cortland's Bootstrap 5.3 theme. I'm the
primary builder/content editor; CTL colleagues (Jennifer Hall, Kristina Maricle, Lisa Sieg,
Heather Hammond) will author content later. IT/web team handles elevated permissions.

Site sections: Event Calendar, Opportunities, Instructional Resources, Tech Support,
Make an Appointment, About Us. Event Calendar and Opportunities are parent listing pages
with URL-mapped detail pages. Single shared advanced template across all CTL pages —
detail page logic lives in listing widgets, not the template.

Content types (10 total, prefixed Ctl): CtlActivity (unified events/opportunities),
CtlInstructionalResource, CtlTechSupport (planned, NOT yet built — do not build against
CtlTechResource, that name was deprecated in favor of CtlTechSupport to match an existing
live relationship field), CtlAttachment, and others.

A separate local Variable Map document (`dotCMS Variable Map.md` — this is the editable
source of truth going forward; a `.docx` copy also exists but is no longer actively
maintained) is the definitive reference for all types/fields/variable names/tabs/widgets.
It is NOT included here in full — ask before assuming field names on a given content type
if unsure, rather than relying on memory or older docs.

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

Note: `videoFile` is a File field (not Binary) — access via `.rawUri`/`.name`/`.thumbnailUri`,
NOT the Binary-field pattern used elsewhere.

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
its own URL-mapped detail page (e.g. CtlTechSupport).

### CtlActivity (unified events + opportunities) — live and working; full schema confirmed via REST pull this session

**Bug found in `ctl-site_opportunity-parent.html`** (not yet fixed): reads
`$opp.oppSubmissionDeadline`/`$opp.oppApplicationLink`, neither of which exists on
CtlActivity — same stale-field-name failure mode as the format/category bug above. Real
fields are `signupDeadline`/`registerApplyLink` (already used correctly in
activity-detail.html).

### CTL homepage — currently BROKEN (not "done" as previously noted)

`ctl-site_home.html` has: a variable-name typo (`$featuredResources` vs `$featResources`),
stale field names (`resourceUrl`/`resourceThumbnail` don't exist on CtlInstructionalResource),
carousel card markup entirely HTML-commented-out (renders an empty shell), sorts by a
nonexistent `activityDate` field, and leftover debug output. Not yet fixed — full detail in
the Variable Map doc's Known Issues section.

## Next Up / On the Horizon

* Remove (or fix) the regexCheck accidentally added to `urlTitle` during 404 debugging —
  it requires a trailing `-YYYY` year suffix (copied from CtlActivity's urlMapPattern
  field), which no CtlInstructionalResource slug actually has, so it'll block future
  edits/saves on every resource until removed
* Fix `ctl-site_home.html` (typo, stale fields, dead carousel, nonexistent activityDate field)
* Fix `ctl-site_opportunity-parent.html` (oppSubmissionDeadline/oppApplicationLink → the real
  fields, signupDeadline/registerApplyLink)
* CtlTechSupport content type (not yet built — see naming note above)
* Delete orphaned widgets: ctl_widget_detail-router, CTL_widget_event-calendar_detail,
  CTL_widget_opportunities_detail (reference deleted CtlEventItem/CtlOpportunity)
* Finish verifying the format/category fix in CtlInstructionalResource listing widget (v8)
* Fix CtlAttachment thumbnail letterboxing — need a crop-based URL pattern instead of thumbnailUri

## Critical Instance-Specific Gotchas (this dotCMS instance specifically)

* `$dotcontent.pull()` sort-by-field returns empty results — always sort by "score", do real sorting in JS
* `$navTool` unavailable
* Page editor preview uses a different internal API path than the live URL — always test via
  live URL + view-source, not the editor preview
* Don't use /dA/, /dotAsset/, /convert/image?id=, /contentAsset/raw-data/ for images — none work here
* Image field → FileAssetMap: `src="$item.field.path$item.field.fileName"`, check existence
  with `#if("$!item.field.fileName" != "")`
* Binary/File field (e.g. CtlAttachment, videoFile) → BinaryMap: use `.rawUri`, `.name`,
  `.thumbnailUri`, `.resizeUri` (NOT `.path`/`.fileName`)
* CheckboxMap → `.selectedValues` (parallel options/values arrays for labels)
* RadioMap → `.selectValue`
* Category fields → different access pattern than Checkbox/Radio, verify in isolation
* Date fields are java.sql.Timestamp — use `.getTime()` for epoch comparisons
* `$!` quiet reference hides empty fields AND wrong variable names identically — a typo'd
  variable name is silently indistinguishable from an empty field (this caused the
  format/category bug above — always verify field names against a fresh REST API pull,
  not memory or older docs)
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


