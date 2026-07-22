# CTL Website — dotCMS Variable Map

Compiled from the dotCMS Content Type REST API (`/api/v1/contenttype`). Last compiled: July 2026. Updated July 22, 2026 with the full CtlActivity schema, CtlAttachment schema, and current widget health notes.

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
| (single tab) | Attachment File | `ctlAttachmentfile` | File | No | The actual file asset. Render via `$file.ctlAttachmentfile.rawUri` (thumbnail: `.thumbnailUri`). |
| (single tab) | Sort Order | `ctlAttachmentsortorder` | Text | No | Optional — controls order when an activity has multiple attachments. |
| (single tab) | Attachment Type | `ctlAttachmenttype` | Radio | No | Values: Slide Deck / Agenda / Schedule / Handout / Reading. |

> **NOTE:** No parent-link (Tag) field exists on this content type in the current API pull — association to CTL Activity happens via CTL Activity's own "attachments" Relationship field instead.

---

## CTL Instructional Resource

- **Content Type Variable:** `CtlInstructionalResource`
- **Base Type:** Content
- **Site:** www2.cortland.edu
- **URL Map Pattern:** No `urlMapPattern` field on this content type (unlike CtlActivity). Detail links are built manually from the `urlTitle` Text field, e.g. `/centers/ctl/instructional-resources/{urlTitle}`. No confirmed single-item detail widget exists yet for this content type — see [Known Issues](#known-issues--not-yet-built).

| Tab / Section | Field Label | Variable | Field Type | Req? | Notes |
|---|---|---|---|---|---|
| 📌 THE BASICS | Resource Title | `resourceTitle` | Text | Yes | The full display title of this resource. |
| 📌 THE BASICS | URL Title | `urlTitle` | Text | Yes | Lowercase, hyphens only, no spaces. Becomes the page URL — do not change after publishing. |
| 📌 THE BASICS | Short Description Blurb | `shortDescription` | Textarea | Yes | 1–2 sentences shown on the listing card. |
| 📌 THE BASICS | Thumbnail | `thumbnail` | Image | No | Optional. Shown on listing card and detail page header. |
| 📄 THE CONTENT | Body | `body` | WYSIWYG | Yes | The resource itself, or context for downloads/videos. |
| 🎬 VIDEO (recordings only) | Video File | `videoFile` | File | No | Local upload, short clips only (~10 min). File field, not Binary — use `.rawUri`/`.name`/`.thumbnailUri`. |
| 🎬 VIDEO (recordings only) | Embed URL | `embedUrl` | Text (URL) | No | Plain video URL (e.g. YouTube), not iframe code. For anything longer than ~10 min. |
| 📚 CITATIONS & FURTHER READING | Citations | `citations` | WYSIWYG | No | One reference per line/bullet. Leave empty if none — section won't render. |
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

### CTL_widget_instructional-resources_listing (v8) — 🚧 IN PROGRESS
- **File:** `ctl-site_instRes-parent.html`
- **Purpose:** Renders the Instructional Resources listing page — radio filters (Format / Modality), a collapsible multi-select Topic pill cloud (6 parent category headings), dynamic cross-section dimming for zero-match options, partial-match fallback ordering, featured-item pinning (maroon border, ★ badge, capped at 3, JS-sorted by modDate), and a QC gate hiding items with an empty `reviewedBy` from the listing (still reachable via direct URL).
- **Queries / Renders:** `$dotcontent.pull("+contentType:CtlInstructionalResource +languageId:1 +live:true", 500, "score")`.
- **Root-caused bug (fixed in this version):** earlier drafts referenced format/category as `$r.format` / `$r.category`, which don't exist on the live schema — the real fields are `formatType` and `topics` (both Category fields, accessed by iterating the field and reading `.categoryName` on each entry, NOT the Checkbox/Radio `.selectedValues` / `.selectValue` pattern). Still pending: final live-URL verification of this fix.
- Note: the file `ctl-site_instRes-detail` (despite its name) currently contains an earlier draft of this same LISTING widget, not a single-item detail page — see Known Issues below.

### CTL Homepage Widget — 🔴 BROKEN
- **File:** `ctl-site_home.html`
- **Purpose:** Tile grid linking to all six site sections, an "Upcoming Events & Opportunities" carousel, and a "Featured Resources" block.
- **Known bugs (confirmed 2026-07-22):**
  - Featured Resources block checks `$featuredResources.size()`, but the pull two lines earlier stores its result in `$featResources` — a variable-name typo. This section never shows real data as written.
  - That same block reads `$item.resourceUrl` and `$item.resourceThumbnail`, neither of which exists on CtlInstructionalResource — the real fields are `urlTitle` (build the link from it, as the listing widget does) and `thumbnail`.
  - The entire carousel's card markup is wrapped in HTML comments, so it currently renders an empty carousel shell with no visible cards.
  - Sorts "upcoming" by `$item.activityDate`, which does not exist anywhere on CtlActivity (confirmed via full schema pull). Needs to branch per record: `eventStartDate` for events, `signupDeadline` (falling back to `submissionDeadline`) for opportunities.
  - Leftover debug output still present in the markup ("Count of allResources...", "Count of featResources...").

---

## Known Issues / Not Yet Built

- **CtlTechSupport** content type: planned, not yet built. Do not build against `CtlTechResource` — that name was deprecated in favor of `CtlTechSupport` to match an existing live relationship field (see `relatedTechSupport` above).
- **Orphaned widgets to delete:** `ctl_widget_detail-router`, `CTL_widget_event-calendar_detail`, `CTL_widget_opportunities_detail` — all reference the deleted `CtlEventItem`/`CtlOpportunity` content types (replaced by unified CtlActivity).
- **`ctl-site_instRes-detail`** is a misnamed / duplicate earlier draft of the Instructional Resources LISTING widget, not an actual single-item detail page. CtlInstructionalResource has no confirmed detail widget yet — pending a decision on whether to build one from scratch (parallel to CTL_widget_activity-detail).
- **CtlAttachment thumbnail letterboxing:** current rendering uses `.thumbnailUri`, which letterboxes non-matching aspect ratios. Needs a crop-based URL pattern instead.
