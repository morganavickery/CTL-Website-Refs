# CTL Website — dotCMS Variable Map

Compiled from the dotCMS Content Type REST API (`/api/v1/contenttype`). Last compiled: July 2026. Updated July 22, 2026 with the full CtlActivity schema, CtlAttachment schema, and current widget health notes. Updated July 23, 2026: CtlInstructionalResource detail page 404 resolved (System Indexed/Unique gotcha), CTL homepage widget confirmed working end-to-end with a horizontally-scrolling card layout replacing the original grid/carousel design.

## How to Use This Document

Each content type below lists its fields grouped by the tab they appear under in the dotCMS content editor. "Variable" is the Velocity variable name used in templates and widgets (e.g. `$item.eventTitle`). "Req?" indicates whether the field is required at the content-entry level.

Where a field's exact name, type, or tab could not be confirmed from the API response captured so far, it is marked accordingly rather than guessed.

---

## CTL Activity

- **Content Type Variable:** `CtlActivity`
- **Base Type:** Content
- **Description (from dotCMS):** for all time-sensitive ctl events, workshops, conferences, awards, grants, etc.
- **Site:** www2.cortland.edu
- **URL Map Pattern:** `/centers/ctl/activities/{urlMapPattern}`
- **Detail Page ID:** `677c33a575e4ebbf53f573b51c23e87a`

This is the newer, unified content type covering events, opportunities, and general activities in one record, distinguished by the *Is this an event?* and *Is this an opportunity?* checkboxes (a record can be both, or neither).

| Tab / Section | Field Label | Variable | Field Type | Req? | Notes |
|---|---|---|---|---|---|
| 📝 Basic Info | Activity Title | `title` | Text | Yes | Shown everywhere: page title, cards, browser tab. |
| 📝 Basic Info | URL Slug | `urlMapPattern` | Text | Yes | Pattern: type-shortname-year (e.g. workshop-udl-2026). Regex-enforced. |
| 📝 Basic Info | What type of activity is this? | `categories` | Category (multi) | Yes | Category tree: `CTL_Activity_Types`. |
| 📝 Basic Info | Card Preview Text | `shortDescription` | Textarea | No | 1–2 sentences max (~75 words), used for listing-card preview blurb. |
| 📝 Basic Info | Thumbnail Image | `thumbnail` | Image | No | Landscape recommended; avoid repeating thumbnails across activities. |
| 📝 Basic Info | Full Description | `longDescription` | WYSIWYG | Yes | No length max. Use built-in Heading styles (start at H2) for accessibility. |
| 📝 Basic Info | Contact Person | `primaryContact` | Text | No | Format: Name, email — e.g. Jane Doe, jane.doe@cortland.edu. |
| 📝 Basic Info | Files, Multimedia, & Downloads | `attachments` | Relationship → CTL Attachment | No | Parent side of the relationship to CTL Attachment. Download-button style, not embedded. |
| ⚙️ Settings | Is this an event? | `isEvent` | Checkbox | No | Defaults to checked (true). Also fill in Event Details tab if checked. |
| ⚙️ Settings | Is this an opportunity? | `isOpportunity` | Checkbox | No | Defaults unchecked. For awards/stipends/fellowships. Also fill in Opportunity Details tab. |
| ⚙️ Settings | How Do People Sign Up? | `signupType` | Radio | Yes | Values: `open` (no RSVP) / `registration` (headcount) / `application` (selective). |
| ⚙️ Settings | Registration/Application Link | `registerApplyLink` | Text (URL) | No | Only relevant if Signup Type ≠ open. |
| ⚙️ Settings | Registration/Application Deadline | `signupDeadline` | Date | No | Only if Signup Type ≠ open. Blank defaults to "ongoing." |
| ⚙️ Settings | Accepts Submissions (Call for Proposals)? | `acceptsSubmissions` | Checkbox | No | Rare — separate from registering to attend; for conferences w/ a call for submissions. |
| ⚙️ Settings | Manually Close Signup Early? | `signupCapacityReached` | Checkbox | No | Only if full before the deadline. |
| ⚙️ Settings | Submission Type Label | `submissionLabel` | Text | No | e.g. "Submit a Paper." Blank = "Submit to Present." |
| ⚙️ Settings | Submission Link | `submissionLink` | Text (URL) | No | Only if Accepts Submissions is checked. |
| ⚙️ Settings | Submission Deadline | `submissionDeadline` | Date-and-Time | No | Only if Accepts Submissions is checked. |
| 📅 Event Details | Location | `eventLocation` | Text | No | e.g. Moffett 208. Skip this tab unless "Is this an event?" is checked. |
| 📅 Event Details | Event Start Date | `eventStartDate` | Date | No | — |
| 📅 Event Details | Event End Date | `eventEndDate` | Date | No | Blank = single-day event. |
| 📅 Event Details | Daily Start Time | `eventStartTime` | Time | No | e.g. 9:00 AM. |
| 📅 Event Details | Daily End Time | `eventEndTime` | Time | No | e.g. 3:00 PM. |
| 💵 Opportunity Details | Compensation Amount | `oppAmountPaid` | Text | No | Numbers only — "$" is added automatically in the template. Skip tab unless compensation is involved. |
| 💵 Opportunity Details | What's expected of participants? | `oppLaborExpectation` | Textarea | No | Keep under ~10 words — what earns the compensation. |

### CTL_Activity_Types (category tree)

| name | key | variable |
|---|---|---|
| Award | award | award |
| Conference | conference | conference |
| Fellowship | fellowship | fellowship |
| Paid | paid | paid |
| Professional Development | professionalDevelopment | professionalDevelopment |
| Research Grant | researchGrant | researchGrant |
| Service | service | service |
| Teaching Grant | teachingGrant | teachingGrant |
| Working Group | workingGroup | workingGroup |
| Workshop | workshop | workshop |

---

## CTL Attachment

- **Content Type Variable:** `CtlAttachment`
- **Base Type:** Content
- **Description (from dotCMS):** for any / all attachments that will be individually-downloadable from the CTL website
- **Site:** www2.cortland.edu
- **URL Map Pattern:** None — not a URL-mapped type; accessed only via the relationship from CTL Activity.

| Tab / Section | Field Label | Variable | Field Type | Req? | Notes |
|---|---|---|---|---|---|
| (single tab) | Attachment Label | `ctlAttachmentlabel` | Text | No | Display name shown on the download button — e.g. "Slide Deck," "Event Flyer," "Handout." |
| (single tab) | Attachment File | `ctlAttachmentfile` | File | No | The actual file asset. Currently rendered via `$file.ctlAttachmentfile.rawUri` (thumbnail: `.thumbnailUri`) in both CTL_widget_activity-detail and CTL_widget_inst-detail's Downloads sections. **⚠️ Unverified as of 2026-07-23:** `videoFile` (also documented as field type "File") turned out to actually be a FileAssetMap needing `.path`/`.fileName`, not a BinaryMap — `.rawUri` on it silently resolved empty. `ctlAttachmentfile` has NOT been re-checked with a live debug dump since that discovery. If Downloads links ever appear blank/broken, check this field the same way (dump `$!file.ctlAttachmentfile` raw object in an EDIT_MODE debug line) before assuming it's still fine. |
| (single tab) | Sort Order | `ctlAttachmentsortorder` | Text | No | Optional — controls order when an activity has multiple attachments. |
| (single tab) | Attachment Type | `ctlAttachmenttype` | Radio | No | Values: Slide Deck / Agenda / Schedule / Handout / Reading. |

> **NOTE:** No parent-link (Tag) field exists on this content type in the current API pull — association to CTL Activity happens via CTL Activity's own "attachments" Relationship field instead.

---

## CTL Instructional Resource

- **Content Type Variable:** `CtlInstructionalResource`
- **Base Type:** Content
- **Site:** www2.cortland.edu
- **URL Map Pattern:** No `urlMapPattern` field on this content type (unlike CtlActivity). Detail links are built manually from the `urlTitle` Text field, e.g. `/centers/ctl/instructional-resources/{urlTitle}`. The detail widget now exists (see Widgets below) but **404s until the content type's URL Map Pattern is configured in the dotCMS admin** — see [Known Issues](#known-issues--not-yet-built).

| Tab / Section | Field Label | Variable | Field Type | Req? | Notes |
|---|---|---|---|---|---|
| 📌 THE BASICS | Resource Title | `resourceTitle` | Text | Yes | The full display title of this resource. |
| 📌 THE BASICS | URL Title | `urlTitle` | Text | Yes | Lowercase, hyphens only, no spaces. Becomes the page URL — do not change after publishing. **Must have "System Indexed" and "Unique" checked in the field settings, or the URL Map Pattern silently fails to resolve** (confirmed 2026-07-22 — this was the actual root cause of a long detail-page 404 debugging session). No year suffix — do NOT apply the same regexCheck used on CtlActivity's `urlMapPattern`. |
| 📌 THE BASICS | Short Description Blurb | `shortDescription` | Textarea | Yes | 1–2 sentences shown on the listing card. |
| 📌 THE BASICS | Thumbnail | `thumbnail` | Image | No | Optional. Shown on listing card and detail page header. |
| 📄 THE CONTENT | Body | `body` | WYSIWYG | Yes | The resource itself, or context for downloads/videos. |
| 🎬 VIDEO (recordings only) | Video File | `videoFile` | File | No | Local upload, short clips only (~10 min). **Confirmed 2026-07-23 via live debug dump:** this is a FileAssetMap (same object type as Image fields), NOT a BinaryMap — `.rawUri`/`.thumbnailUri`/`.resizeUri` don't exist on it and silently resolve empty. Correct access: `.path` + `.fileName` concatenation, e.g. `$item.videoFile.path$item.videoFile.fileName` (identical to the `thumbnail` Image field pattern). |
| 🎬 VIDEO (recordings only) | Embed URL | `embedUrl` | Text (URL) | No | Plain video URL (e.g. YouTube), not iframe code. For anything longer than ~10 min. |
| 📚 CITATIONS & FURTHER READING | Citations | `citations` | WYSIWYG | No | One reference per line/bullet. Leave empty if none — section won't render. |
| *Unconfirmed* | Downloadable Attachments | `downloadableAttachments` | Relationship → CtlAttachment | No | Renders as a "Downloads" section on the detail page — same button/thumbnail markup as CtlActivity's `attachments` field. Pre-hydrated like all relationship fields; access `.ctlAttachmentfile`/`.ctlAttachmentlabel` directly. Tab placement not yet confirmed. |
| 🏷️ TAGGING & FILTERS | Modality | `modality` | Checkbox | Yes | Course format(s). Values: In-Person\|inperson, Online Async\|async, Online Sync\|sync |
| 🏷️ TAGGING & FILTERS | Format Type | `formatType` | Category | Yes | Tree: `CTL_Inst. Resource_Types` (key: `ctlInstResourceTypes`). What kind of thing this is (video, blog-post, slide-deck, template, guide). |
| 🏷️ TAGGING & FILTERS | Topics | `topics` | Category (multi) | Yes | Tree: `CTL_Inst. Resource_Topics`. Powers the collapsible multi-select topic pill cloud (6 parent category headings) in the listing widget. |
| 🏷️ TAGGING & FILTERS | Frameworks | `frameworks` | Category (multi) | No | Display-only badges on cards/detail — NOT filterable. Category tree not yet confirmed. |
| *Unconfirmed* | Author | `author` | Text | Yes | Byline shown on the resource. Tab placement not yet confirmed — verify via REST pull. |
| *Unconfirmed* | Reviewed By | `reviewedBy` | Text | No | QC gate: empty ⇒ item is hidden from the listing widget (still reachable via direct URL). Edit-mode banner shows how many items this hides. Tab placement not yet confirmed. |
| *Unconfirmed* | Last Updated | `lastUpdated` | Date | Yes | `java.sql.Timestamp` — use `.getTime()` for epoch comparisons, not string comparison. Drives default card sort order (desc). Tab placement not yet confirmed. |
| *Unconfirmed* | Featured | `featured` | Radio | No | Access via `.selectValue` ("yes"/"no"), NOT `.selectedValues` — it's a Radio field, not Checkbox. Featured items get a maroon border + ★ badge, capped at 3, JS-sorted by modDate. Tab placement not yet confirmed. |
| 🔗 Related Content *(assumed)* | Related Instructional Resources | `relatedInstructionalResources` | Relationship → CtlInstructionalResource | No | Self-relationship. Tab name assumed, not yet confirmed via REST pull. |
| 🔗 Related Content *(assumed)* | Related Events | `relatedEvents` | Relationship → CtlActivity | No | Tab name assumed, not yet confirmed via REST pull. |
| 🔗 Related Content *(assumed)* | Related Tech Support | `relatedTechSupport` | Relationship → CtlTechSupport | No | CtlTechSupport content type is planned but **NOT YET BUILT** (see Known Issues) — this relationship field already exists on CtlInstructionalResource per the REST pull. Do NOT build against a type called `CtlTechResource` — that name was deprecated in favor of `CtlTechSupport`. |

---

## Widgets

Widgets don't hold content-editor fields in the same way; this section is a quick-reference index of what each one does and what it queries, so you know where to look when something breaks.

### CTL_widget_activity-detail — ✅ LIVE
- **Variable:** `CtlWidgetActivityDetail`
- **Purpose:** Renders the full detail page for a single CTL Activity record (event, opportunity, or general activity).
- **Queries / Renders:** `$dotcontent.pull("+contentType:CtlActivity ...")` matched by `urlMapPattern` against the URL slug.
- The current, working detail renderer. Handles Add-to-Calendar (.ics) generation, attachment downloads, signup/submission boxes, and breadcrumb + page-title JS override.

### CTL_widget_event-calendar_parent — ✅ LIVE
- **Variable:** `EventCalendarWidget`
- **Purpose:** Renders the Event Calendar listing page — Upcoming Events and Past Events sections.
- **Queries / Renders:** `$dotcontent.pull("+contentType:CtlActivity ...")`, filtered client-side (Velocity loop) to `isEvent = true`, split into upcoming/past based on `eventEndDate`/`eventStartDate` vs. now.
- Correctly migrated to CtlActivity. Links each card to `/centers/ctl/event-calendar/$item.urlMapPattern`.

### CTL_widget_opportunities_parent — ⚠️ LIVE, BUG CONFIRMED
- **Variable:** `CtlWidgetOpportunitiesParent`
- **Purpose:** Renders the Opportunities listing page — active opportunities plus a collapsible "Missed Connections" (expired) section.
- **Queries / Renders:** `$dotcontent.pull("+contentType:CtlActivity ...")`, filtered to `isOpportunity = true`, sorted by deadline.
- **⚠️ BUG (confirmed 2026-07-22 via full CtlActivity REST pull):** this widget reads `$opp.oppSubmissionDeadline` and `$opp.oppApplicationLink` — neither field exists on CtlActivity. The real fields are `signupDeadline` and `registerApplyLink` (the same ones CTL_widget_activity-detail already uses correctly). Until fixed, the deadline display and Apply button on this listing page will silently render blank/fallback for every opportunity.

### CTL_widget_instructional_parent (v8) — 🚧 IN PROGRESS
- **File:** `ctl-site_instRes-parent.html`
- **dotCMS widget variable:** `CtlWidgetInstructionalParent`
- **Host page:** "Instructional Resources" — url `index.dot` in `/centers/ctl/instructional-resources/`
- **Purpose:** Renders the Instructional Resources listing page — radio filters (Format / Modality), a collapsible multi-select Topic pill cloud (6 parent category headings), dynamic cross-section dimming for zero-match options, partial-match fallback ordering, featured-item pinning (maroon border, ★ badge, capped at 3, JS-sorted by modDate), and a QC gate hiding items with an empty `reviewedBy` from the listing (still reachable via direct URL).
- **Queries / Renders:** `$dotcontent.pull("+contentType:CtlInstructionalResource +languageId:1 +live:true", 500, "score")`.
- **Root-caused bug (fixed in this version):** earlier drafts referenced format/category as `$r.format` / `$r.category`, which don't exist on the live schema — the real fields are `formatType` and `topics` (both Category fields, accessed by iterating the field and reading `.categoryName` on each entry, NOT the Checkbox/Radio `.selectedValues` / `.selectValue` pattern). Still pending: final live-URL verification of this fix.
- Note: `ctl-site_instRes-detail` used to contain a mis-named duplicate of this listing widget — it now holds the real detail widget (see below).

### CTL_widget_inst-detail — ✅ LIVE (resolved 2026-07-22, updated 2026-07-23)
- **File:** `ctl-site_instRes-detail`
- **dotCMS widget variable:** `CtlWidgetInstDetail`
- **Purpose:** Renders the full detail page for a single CtlInstructionalResource record — thumbnail, format/modality tags, byline (author + lastUpdated), frameworks badges, inline video, body, downloads, topic chips, related resources/events, citations (last), back link. `shortDescription` is intentionally NOT rendered on this page (card-preview text only).
- **Section order (updated 2026-07-23):** thumbnail → tags → title/byline → frameworks → body → **video** → **Downloads** → Topics → Related Resources (now a carousel, see below) → Related Events → **Citations (moved to last)** → back link. Video was moved to directly under the body text per request (was above it originally).
- **Downloads (added 2026-07-23):** loops `$item.downloadableAttachments` (Relationship → CtlAttachment, pre-hydrated) using the same button/thumbnail markup as CTL_widget_activity-detail's `attachments` block. Previously this field existed on the content type with no rendering path at all. Positioned directly under the video block.
- **Related Resources carousel (added 2026-07-23):** switched from a static Bootstrap `row row-cols-md-2` grid to the same `.ctl-hscroll`/`.ctl-hscroll-item` horizontally-scrolling pattern as the homepage (own copy: `.ctl-ir-hscroll`/`.ctl-ir-hscroll-item`, same breakpoints/flex-basis values). Related Events keeps its plain `<ul>` list — only Related Resources was asked to become a carousel.
- **Modality tags (updated 2026-07-23):** format-type tags (`.ctl-ir-tag`, grey square badge) and modality tags (`.ctl-ir-modality-tag`, solid maroon pill) now use visually distinct styles. If all 3 possible modality values are selected, they collapse to a single "All Modalities" tag instead of listing all 3.
- **H1/H2 downgrade (added 2026-07-23):** `body` and `citations` WYSIWYG output is regex-sanitized (`<h1>`/`<h2>` → `<h3>`, both open and close tags, case-insensitive) before rendering, so editor-typed headings can't collide with the page's own H1/H2 — directly targets the `stress-full-multimedia-kitchen-sink` heading-collision test case. Sanitized copies are held in `$bodyHtml`/`$citationsHtml`, not `$item.body`/`$item.citations` directly.
- **Video embed (changed 2026-07-23):** `embedUrl` no longer renders as a "Watch Video" link (new tab) — it's parsed into a native embeddable `src` (YouTube `watch?v=`/`youtu.be` → `youtube.com/embed/...`, `vimeo.com/...` → `player.vimeo.com/video/...`) and rendered inline in a responsive 16:9 `<iframe>`. Unrecognized hosts fall back to using the raw URL as the iframe `src` (best effort — may not render if the host blocks framing).
- **⚠️ TEMP (2026-07-23):** local `videoFile` and `embedUrl` currently render **both at once** (independent `#if` blocks, not either/or) for side-by-side stress-test comparison — revert to `videoFile`-takes-precedence (`#if`/`#elseif`) once testing is done.
- **`videoFile` playback bug, root-caused 2026-07-23:** the local `<video>` block never rendered because it used `.rawUri`, which doesn't exist on a File-type field (see the FileAssetMap/BinaryMap gotcha in CLAUDE.md and above). Fixed to `$item.videoFile.path$item.videoFile.fileName`. Root-caused via a temporary EDIT_MODE debug panel dumping the raw field object — now trimmed to a single permanent debug line above the video block.
- **Queries / Renders:** same pull-all + Velocity-side slug match pattern as CTL_widget_activity-detail, matching the URL slug against `urlTitle` (CtlInstructionalResource has no `urlMapPattern` field).
- **Host page:** "Instructional Resource Detail" — url `instructional-resource-detail` in `/centers/ctl/instructional-resources/`.
- **Content type config:** URL Map Pattern = `/centers/ctl/instructional-resources/{urlTitle}`; Detail Page = `//www2.cortland.edu/centers/ctl/instructional-resources/instructional-resource-detail`.
- **Root cause of the 404 (resolved):** the `urlTitle` field needed **"System Indexed" AND "Unique" checked** in its own field settings — without those, dotCMS's URL Map resolution silently fails to bind even though the URL Map Pattern + Detail Page are configured correctly on the content type. See the gotcha in CLAUDE.md — this will affect any future URL-mapped content type (e.g. CtlTechSupport).

### CTL Homepage Widget — ✅ LIVE, CONFIRMED WORKING (2026-07-23)
- **File:** `ctl-site_home.html`
- **Must live in a dotCMS Widget, not a "Web Page Content" block** — the latter rendered nothing at all in this instance (not even plain HTML), matching every other Velocity file in this project already living in a Widget.
- **Purpose:** Tile grid linking to all six site sections, an "Upcoming Events & Opportunities" row, and a "Featured Resources" row.
- **Display pattern (both rows):** a horizontally-scrolling flex row (`.ctl-hscroll` container / `.ctl-hscroll-item` per card — CSS defined once at the top of the Events section, shared by both), NOT a Bootstrap `.carousel`. No auto-play; the user scrolls manually. Responsive: ~1.1 cards visible on mobile, scaling up to ~2.3 cards at standard/max desktop width (2 full + a peek of the 3rd — deliberate, signals scrollability) and staying there at wider widths too, via media queries on `.ctl-hscroll-item`'s `flex-basis`.
- **Upcoming Events & Opportunities:** pulls CtlActivity (pull-all + Velocity-side `isEvent`/`isOpportunity` filter via `.selectedValues.contains("true")`, not Lucene), computes a per-record relevant date (`eventStartDate`/`eventEndDate` for events, `signupDeadline`/`submissionDeadline` for opportunities — `activityDate` never existed as a field), filters to future-dated items, bubble-sorts ascending, renders each with a badge that handles the both-event-and-opportunity case, linking to `/centers/ctl/activities/{urlMapPattern}`.
- **Featured Resources:** filters to `featured.selectValue == "yes"` AND non-empty `reviewedBy` (same QC gate as the Instructional Resources listing widget), renders using the *exact same* card markup/classes (`ctl-res-card`, `ctl-res-fmt-badge`, `ctl-res-topic-chip`, stretched-link title) as that listing widget, so featured cards look identical there and on the homepage. Holds ALL featured + reviewed resources, no cap. Fixed along the way: `$featuredResources`/`$featResources` typo (block could never show real data before), stale `resourceUrl`/`resourceThumbnail` fields (real ones are `urlTitle`/`thumbnail`), Radio-field `featured` query switched from Lucene to pull-all + `.selectValue`.
- **Debugging:** raw always-visible debug `<p>` tags removed; replaced with `$EDIT_MODE`-gated debug lines at each pull/filter/sort checkpoint in both sections (editor-only, invisible to real visitors).

---

## Known Issues / Not Yet Built

- **CtlTechSupport** content type: planned, not yet built. Do not build against `CtlTechResource` — that name was deprecated in favor of `CtlTechSupport` to match an existing live relationship field (see `relatedTechSupport` above).
- **Orphaned widgets to delete:** `ctl_widget_detail-router`, `CTL_widget_event-calendar_detail`, `CTL_widget_opportunities_detail` — all reference the deleted `CtlEventItem`/`CtlOpportunity` content types (replaced by unified CtlActivity).
- **CtlAttachment thumbnail letterboxing:** current rendering uses `.thumbnailUri`, which letterboxes non-matching aspect ratios. Needs a crop-based URL pattern instead.
