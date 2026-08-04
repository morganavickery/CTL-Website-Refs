# CTL Website — dotCMS Variable Map

A crash-course index of the CTL site's backend: what content types exist, what fields they
have, and which widget renders what. Written for someone unfamiliar with this dotCMS
instance to get oriented quickly — not a debugging log.



Field names ("Variable") are the Velocity variable used in templates/widgets, e.g.
`$item.eventTitle`. Compiled from the dotCMS Content Type REST API plus a line-by-line
audit of every widget file against that API data. Last full verification: 2026-08-04.


## Site Map

Current IA (revised 2026-08-04, two rounds same day — see CLAUDE.md's "Site Information
Architecture" section for the full hierarchy with levels and intended URL paths), all under
`/centers/ctl/`, sharing one advanced template (`ctl-site_page template.html`):

| Section | Listing Widget(s) | Detail Widget |
|----|----|----|
| Events | `widgets/ctl-widget_upcoming-events_events.html` + `widgets/ctl-widget_past-events_events.html` | `ctl-site_activity-detail.html` (shared) |
| Funding > Current Opportunities | `widgets/ctl-widget_active-opportunities_current-opportunities.html` | `ctl-site_activity-detail.html` (shared) |
| Funding > Past Recipients | `widgets/ctl-widget_missed-opportunities_past-recipients.html` + a not-yet-built past-award-recipients widget | `ctl-site_activity-detail.html` (shared, for the expired-opportunities half only) |
| Resources > For Course Design | `CtlWidgetInstructionalParent` (`ctl-site_instRes-parent.html`) | `CtlWidgetInstDetail` (`ctl-site_instRes-detail`) |
| Resources > For Technologies | not yet built |    |
| Request Support | `widgets/ctl-widget_our-services_request-support.html` + a 2nd instance of `widgets/ctl-widget_ctl-staff-profiles.html` | — (no detail pages; cards only) |
| About the CTL > Meet the CTL Staff | `widgets/ctl-widget_ctl-staff-profiles.html` | — |
| About the CTL > Faculty Development Committee | `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` | — |
| About the CTL > Impact & Reports | not yet built | — |
| Welcome to the CTL (homepage) | `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html` + `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html` + `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html` | — |

All of the above were split from single combined widgets into the standalone files listed
2026-08-04, for reuse — see the Widgets Index below and CLAUDE.md's Next Up for live
dotCMS migration status (code is done; several pages still need their old single widget
swapped for the new ones in the live CMS, and the Funding/About the CTL restructure and
Impact & Reports/past-award-recipients/Funding-overview widgets still need building).

Events and Funding (both Current Opportunities and the expired half of Past Recipients)
list `CtlActivity` records (distinguished by `isEvent`/`isOpportunity`) and share one
detail widget/page, routed via vanity URL prefix — see CLAUDE.md.

**Shared template** (`ctl-site_page template.html`): every CTL page uses this one advanced
template — detail-page logic lives in the listing widgets, not the template. The sidebar
nav is built dynamically each render from published pages under `/centers/ctl/*`, gated by
each page's own `showOnMenu` checkbox field (a page-level field, not a content-type field)
— to add/remove a page from the nav, toggle that checkbox on the page itself.

Widgets must live in a dotCMS **Widget**, not a "Web Page Content" block — the latter
doesn't evaluate Velocity in this instance.


## Content Types

### CtlActivity — unified events + opportunities

* **Base Type:** Content · **Site:** www2.cortland.edu
* **URL Map Pattern:** `/centers/ctl/activities/{urlMapPattern}` · **Detail Page ID:** `677c33a575e4ebbf53f573b51c23e87a`
* One record can be an event, an opportunity, both, or neither — distinguished by `isEvent`/`isOpportunity`.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `title` | Text | Yes | Page title, cards, browser tab. |
| `urlMapPattern` | Text | Yes | Slug, e.g. `workshop-udl-2026`. Not Lucene-indexed — filter in Velocity. |
| `categories` | Category (multi) | Yes | Tree: `CTL_Activity_Types` (see below). |
| `shortDescription` | Textarea | No | Card preview blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `longDescription` | WYSIWYG | Yes | Full body. |
| `primaryContact` | Text | No | "Name, email" format. |
| `attachments` | Relationship → CtlAttachment | No | Parent side; download buttons. |
| `isEvent` | Checkbox | No | Default checked. |
| `isOpportunity` | Checkbox | No | Default unchecked. |
| `signupType` | Radio | Yes | `open` / `registration` / `application`. |
| `registerApplyLink` | Text (URL) | No | Real field for register/apply — see Known Issues for two widgets that don't use it. |
| `signupDeadline` | Date | No | Blank = "ongoing." |
| `acceptsSubmissions` | Checkbox | No | Separate from registering to attend (calls for proposals). |
| `signupCapacityReached` | Checkbox | No | Manually close signup early. |
| `submissionLabel` | Text | No | e.g. "Submit a Paper." Blank → "Submit to Present." |
| `submissionLink` | Text (URL) | No | Only if `acceptsSubmissions`. |
| `submissionDeadline` | Date-and-Time | No | Only if `acceptsSubmissions`. |
| `eventLocation` | Text | No | e.g. "Moffett 208." |
| `eventStartDate` / `eventEndDate` | Date | No | Blank end date = single-day event. |
| `eventStartTime` / `eventEndTime` | Time | No | — |
| `oppAmountPaid` | Text | No | Numbers only; `$` added by the template. |
| `oppLaborExpectation` | Textarea | No | \~10 words, what earns the compensation. |
| `sessionTimeBlockDefinitions` | Key-Value | No | Multi-track conferences only. Key = block number, Value = display label (e.g. "Concurrent Sessions 1 — 9-9:50 a.m."). |
| `activitySessions` | Relationship → CtlActivitySession | No | Multi-track conferences only. |

#### CTL_Activity_Types (category tree)

Award · Conference · Fellowship · Paid · Professional Development · Research Grant ·
Service · Teaching Grant · Working Group · Workshop
(name/key/variable are identical lowerCamelCase-or-plain strings for each, e.g. `workshop`)


### CtlActivitySession — conference session sub-records

* **Base Type:** Content · **Site:** www2.cortland.edu · No URL Map — reached only via `CtlActivity.activitySessions`.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `sessionTimeBlock` | Select (Dropdown) | Yes | Values 1–6; must match a key in the parent's `sessionTimeBlockDefinitions`. |
| `sessionRowSortOrder` | Text (number) | Yes | Order within a time block. |
| `sessionRoom` | Text | Yes | e.g. "204-205." |
| `sessionTitle` | Text | Yes | Shown on the collapsed bar. |
| `sessionSpeakers` | Text | Yes | Comma-separated names. |
| `sessionTags` | Text | No | Comma-separated free-form keywords; split/trimmed for badges. |
| `sessionAbstract` | WYSIWYG | Yes | Shown when expanded. |
| `sessionAttachments` | Relationship → CtlAttachment | No | Own relationship, separate from the parent activity's. |


### CtlAttachment — downloadable files

* **Base Type:** Content · **Site:** www2.cortland.edu · No URL Map — reached only via relationship from CtlActivity/CtlActivitySession/CtlInstructionalResource.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `ctlAttachmentlabel` | Text | No | Download-button display text. |
| `ctlAttachmentfile` | File | No | ⚠️ Rendered via `.rawUri`/`.thumbnailUri` — **unverified, likely broken**, see Known Issues. |
| `ctlAttachmentsortorder` | Text | No | Optional manual ordering. |
| `ctlAttachmenttype` | Radio | No | Slide Deck / Agenda / Schedule / Handout / Reading. |

No parent-link field on this type — association happens from the *other* side (the
relationship field on CtlActivity/CtlInstructionalResource/CtlActivitySession).


### CtlInstructionalResource

* **Base Type:** Content · **Site:** www2.cortland.edu
* No native URL Map field. Detail links are built manually: `/centers/ctl/resources/course-design/{urlTitle}`
  (updated 2026-08-04 for the Resources IA revision — formerly `/centers/ctl/instructional-resources/{urlTitle}`;
  live URL Map Pattern update on the content type itself still pending, see CLAUDE.md's Next Up).

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `resourceTitle` | Text | Yes | Full display title. |
| `urlTitle` | Text | Yes | Must have **System Indexed + Unique** checked or the URL Map silently fails to bind (see CLAUDE.md). Lowercase, hyphens only. |
| `shortDescription` | Textarea | Yes | Listing-card blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `body` | WYSIWYG | Yes | Main content. |
| `videoFile` | File | No | FileAssetMap (not BinaryMap, despite field type "File") — `.path` + `.fileName`. |
| `embedUrl` | Text (URL) | No | YouTube/Vimeo URL, parsed into an embeddable iframe src. |
| `citations` | WYSIWYG | No | Section hidden when empty. |
| `downloadableAttachments` | Relationship → CtlAttachment | No | Renders a Downloads section. |
| `modality` | Checkbox | Yes | Values: `inperson`, `async`, `sync`. |
| `formatType` | Category | Yes | Tree: `CTL_Inst. Resource_Types`. |
| `topics` | Category (multi) | Yes | Tree: `CTL_Inst. Resource_Topics`; powers the topic pill cloud. |
| `frameworks` | Category (multi) | No | Display-only badges, not filterable. |
| `author` | Text | Yes | Byline. |
| `reviewedBy` | Text | No | QC gate — empty hides the item from listings (still URL-reachable). |
| `lastUpdated` | Date | Yes | `java.sql.Timestamp` — use `.getTime()`. |
| `featured` | Radio | No | `.selectValue` ("yes"/"no"). Featured + reviewed items get a maroon border/★, capped at 3 on the listing page. |
| `relatedInstructionalResources` | Relationship → CtlInstructionalResource | No | Self-relationship. |
| `relatedEvents` | Relationship → CtlActivity | No | — |
| `relatedTechSupport` | Relationship → CtlTechSupport | No | Field exists on the schema but nothing renders it yet (CtlTechSupport itself isn't built). |


### CtlPersonnel — CTL/FDC/Other staff and affiliates

* **Base Type:** Content · No URL Map — all personnel render on About the CTL (Meet the CTL Staff) / Request Support.

| Variable | Field Type | Note |
|----|----|----|
| `name` | Text | Full display name. |
| `pronouns` | Radio | `.selectValue` + parallel `.values`/`.options` lookup for the display label (same pattern as `modality`). `"dont show pronouns"` is a real option, treated as blank. |
| `headshotPhoto` | Image | FileAssetMap — `.path` + `.fileName`. |
| `jobTitle` | Text | Combined with `degrees` on one line: "Job Title \| Deg1, Deg2." |
| `degrees` | Text | e.g. "PhD," "MSEd." See `jobTitle`. |
| `rank` | Text (whole number) | Sort key, ascending, blank sorts last. **Runtime type is already** `java.lang.Long` despite the "Text" editor label — use `$item.rank` directly, don't round-trip through a String (see CLAUDE.md). |
| `affiliations` | Checkbox | Values are capitalized — `"CTL"`, `"FDC"`, `"Other"` — `.contains()` is case-sensitive. Historically drove three sections (CTL Staff / FDC Members / Other Affiliates) on the old combined About Us page; Other Affiliates was dropped from the IA entirely 2026-08-04, so only `CTL` (Meet the CTL Staff / Request Support) and `FDC` (Faculty Development Committee) are still used. |
| `emailCortlandEdu` | Text | Rendered as `mailto:`; link hidden if empty. |
| `bookWithMeFullLink` | Text | External booking URL; "Meet with Me" button hidden if empty. |
| `websitePromo` | Text (URL) | Shown on both instances of the staff-profiles widget (Meet the CTL Staff and Request Support) — not gated by page. |
| `buttonLabel` | Text | Label for the `websitePromo` button, falls back to "Visit My Website." Distinct from CtlService's `serviceButtonLabel`. |
| `areasOfExpertise` | Textarea | One bullet per line (splits on `\n`). |
| `howICanHelp` | Textarea | Same one-bullet-per-line convention. |


### CtlService — Request Support page service cards

* **Base Type:** Content · No URL Map — all services render as cards on Request Support.

| Variable | Field Type | Note |
|----|----|----|
| `serviceName` | Text | Card title. |
| `serviceShortDescription` | Textarea | Card body; clamped to 2 lines with a "Read more" toggle. |
| `serviceAccessLink` | Text | External URL; button hidden unless non-empty AND `isCurrentlyAvailable`. |
| `serviceButtonLabel` | Text | Falls back to "Access this Service." |
| `thumbnailImage` | Image | FileAssetMap — `.path` + `.fileName`. |
| `isCurrentlyAvailable` | Checkbox | Values `yes`/`no`. Drives the availability badge; unavailable services still show, just without the access button. |

No sort-order field — display order is whatever the pull returns.


## Widgets Index

| Widget (dotCMS variable) | File | Content Type(s) | Status |
|----|----|----|----|
| `CtlWidgetActivityDetail` | `widgets/ctl-site_activity-detail.html` | CtlActivity | ✅ Live; vanity-URL routing updated 2026-08-04 for the Events/Funding IA revision — see CLAUDE.md |
| *(Events, split 2026-08-04)* | `widgets/ctl-widget_upcoming-events_events.html` + `widgets/ctl-widget_past-events_events.html` | CtlActivity | ✅ Fixed in code; live migration pending (old `ctl-site_event-parent.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Funding > Current Opportunities, split 2026-08-04, relocated same day)* | `widgets/ctl-widget_active-opportunities_current-opportunities.html` | CtlActivity | ✅ Fixed in code; live migration pending (old `ctl-site_opportunity-parent.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Funding > Past Recipients, split 2026-08-04, relocated same day)* | `widgets/ctl-widget_missed-opportunities_past-recipients.html` + a not-yet-built past-award-recipients widget | CtlActivity | ✅ First widget fixed in code; second not yet built. Live migration pending — see CLAUDE.md Next Up |
| `CtlWidgetInstructionalParent` (v8) | `widgets/ctl-site_instRes-parent.html` | CtlInstructionalResource | ✅ Live on "For Course Design" (formerly "Instructional Resources") — URL Map Pattern update still pending |
| `CtlWidgetInstDetail` | `widgets/ctl-site_instRes-detail` | CtlInstructionalResource | ✅ Live — URL Map Pattern update still pending |
| *(About the CTL, split 2026-08-04)* | `widgets/ctl-widget_ctl-staff-profiles.html` (Meet the CTL Staff) + `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` (Faculty Development Committee) | CtlPersonnel | ✅ Code ready; live migration pending (old `ctl_site_about-us.html` is now a deprecation stub) — see CLAUDE.md Next Up. "Impact & Reports" subpage/widget not yet built |
| *(Request Support, split 2026-08-04)* | `widgets/ctl-widget_our-services_request-support.html` + a second instance of `widgets/ctl-widget_ctl-staff-profiles.html` | CtlService, CtlPersonnel | ✅ Code ready; needs a `sectionHeading` field added to the CTL Staff profiles widget's content type, then live migration (old `ctl-site_get-help.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Welcome to the CTL homepage, split 2026-08-04)* | `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html` + `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html` + `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html` | CtlActivity, CtlInstructionalResource | ✅ Code ready (nav tiles updated to 5 tiles for the Funding/Resources restructure); live migration pending (old `ctl-site_home.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(shared template)* | `DEPRECATED/ctl-site_page template.html` | — (page-level `showOnMenu` field) | ✅ Live |
| *(Funding overview — NEW, not yet built)* | not yet built | — | ⬜ L1 landing/overview content for Funding; see CLAUDE.md Next Up |
| *(Impact & Reports — NEW, not yet built)* | not yet built | — | ⬜ L2 subpage under About the CTL; see CLAUDE.md Next Up |

All widgets above were originally one file per page; each has been split into multiple
standalone widgets in `widgets/` for reuse (dev-checklist.md: "split up sections into
multiple widgets for potential reuse"). The old combined files are deprecation stubs
pointing at their replacements, kept in the repo only until each page's live dotCMS
migration is confirmed — see CLAUDE.md's Next Up section for per-page migration steps.

For what each widget actually does section-by-section, read the widget file itself — the
header comment block in each one is kept current as a purpose summary.


## Known Issues / Not Yet Built

* `ctlAttachmentfile` (CtlAttachment) is accessed via `.rawUri`/`.thumbnailUri` in both
  `ctl-site_activity-detail.html` and `ctl-site_instRes-detail`. `videoFile` looked
  identical on paper and turned out to need `.path`/`.fileName` instead — `ctlAttachmentfile`
  has never been confirmed working with a live debug dump. Check this first if
  Downloads links/thumbnails ever appear blank.
* **CtlTechSupport** content type: planned, not yet built. Don't build against
  `CtlTechResource` — deprecated in favor of `CtlTechSupport`.
* **CtlAttachment thumbnail letterboxing**: `.thumbnailUri` letterboxes non-matching aspect
  ratios; needs a crop-based URL pattern instead.
* **Orphaned widgets to delete:** `ctl_widget_detail-router`, `CTL_widget_event-calendar_detail`,
  `CTL_widget_opportunities_detail` — reference the deleted `CtlEventItem`/`CtlOpportunity`
  content types.
* **Leftover** `$EDIT_MODE` debug output (invisible to real visitors, but not yet trimmed):
  heaviest in the deprecated `DEPRECATED/ctl-site_get-help.html` stub (visible debug panels
  with per-record Java-type reflection output from the `rank`-sort investigation), lighter
  in `DEPRECATED/ctl-site_home.html` and `DEPRECATED/ctl_site_about-us.html`.


