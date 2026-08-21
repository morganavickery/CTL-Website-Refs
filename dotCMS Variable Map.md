# CTL Website — dotCMS Variable Map

A crash-course index of the CTL site's backend: what content types exist, what fields they
have, and which widget renders what. Written for someone unfamiliar with this dotCMS
instance to get oriented quickly — not a debugging log.



Field names ("Variable") are the Velocity variable used in templates/widgets, e.g.
`$item.eventTitle`. Compiled from the dotCMS Content Type REST API plus a line-by-line
audit of every widget file against that API data.


## Site Map

Current IA (see CLAUDE.md's "Site Information Architecture" section for the full hierarchy
with levels and intended URL paths), all under `/centers/ctl/`, sharing one advanced
template (`ctl-site_page template.html`):

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

All of the above were split from single combined widgets into the standalone files listed,
for reuse — see the Widgets Index below and CLAUDE.md's Next Up for live
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

| Variable | Field Type | Req? | Note | Hint (editor-facing text) |
|----|----|----|----|----|
| `title` | Text | Yes | Page title, cards, browser tab. | Shown everywhere: page title, cards, browser tab |
| `urlMapPattern` | Text | Yes | Slug, e.g. `workshop-udl-2026`. Not Lucene-indexed — filter in Velocity. | type-shortname-year (e.g. workshop-udl-2026) |
| `categories` | Category (multi) | Yes | Tree: `CTL_Activity_Types` (see below). | Select all that apply |
| `shortDescription` | Textarea | No | Card preview blurb. | 1–2 sentences MAX. For preview blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. | Landscape works best, avoid repeating thumbnails across activities |
| `longDescription` | WYSIWYG | Yes | Full body. | No length max. Images/embeds welcome. For accessibility, use the built-in Heading styles (start at H2 — the page title is already H1) instead of just making text bold/large. |
| `primaryContact` | Text | No | "Name, email" format. | Name + email, e.g. Jane Doe, jane.doe@cortland.edu |
| `attachments` | Relationship → CtlAttachment | No | Parent side; download buttons. | Download button only, not embedded. PDFs preferred. For in-page images, use Full Description. |
| `isEvent` | Checkbox | No | Default checked. | Is this something people attend? Should it appear on the calendar? |
| `isConference` | Checkbox | No | Multi-session events (e.g. a conference); if checked, `isEvent` should also be checked. | Is this an event with multiple sessions? (i.e. a conference); if yes, ensure "Event" is also checked |
| `isOpportunity` | Checkbox | No | Default unchecked. | For awards, stipends, fellowships, etc. Also fill in Opportunity Details tab. |
| `signupType` | Radio | Yes | `open` / `registration` / `application`. | Open = none. Registration = headcount. Application = selective |
| `registerApplyLink` | Text (URL) | No | Real field for register/apply — see Known Issues for two widgets that don't use it. | Only if Signup Type ≠ Open |
| `signupDeadline` | Date | No | Blank = "ongoing." | Only if Signup Type ≠ Open. Blank defaults to "ongoing" |
| `acceptsSubmissions` | Checkbox | No | Separate from registering to attend (calls for proposals). | Rare — separate from registering to attend; reserved for conferences where there's a call for submissions |
| `signupCapacityReached` | Checkbox | No | Manually close signup early. | Only if full before the deadline |
| `submissionLabel` | Text | No | e.g. "Submit a Paper." Blank → "Submit to Present." | e.g. "Submit a Paper." Blank = "Submit to Present" |
| `submissionLink` | Text (URL) | No | Only if `acceptsSubmissions`. | Only if Accepts Submissions is checked |
| `submissionDeadline` | Date-and-Time | No | Only if `acceptsSubmissions`. | Only if Accepts Submissions is checked. |
| `eventLocation` | Text | No | e.g. "Moffett 208." | e.g. Moffett 208 |
| `eventStartDate` | Date | No | Blank end date on `eventEndDate` = single-day event. | Only needed if "Show on Event Calendar?" is checked. |
| `eventEndDate` | Date | No | Blank = single-day event. | Blank = single-day event |
| `eventStartTime` | Time | No | — | e.g. 9:00 AM |
| `eventEndTime` | Time | No | — | e.g. 3:00 PM |
| `oppAmountPaid` | Text | No | Numbers only; `$` added by the template. | Numbers only — "$" added automatically |
| `oppLaborExpectation` | Textarea | No | \~10 words, what earns the compensation. | <10 words — what earns the compensation? |
| `sessiontimeblockdefinitions` | Key-Value | No | Live schema variable is all-lowercase `sessiontimeblockdefinitions`, not camelCase `sessionTimeBlockDefinitions`. No widget references this field yet (multi-track session display isn't built), so this hasn't caused a live bug, but any future Velocity code must use the lowercase form or it'll silently return empty (same failure mode as the format/category bug in CLAUDE.md). Multi-track conferences only; fill in FIRST. Key = block number, Value = display label. | Fill this in FIRST if this activity has multiple concurrent sessions. One row per time block: Key = block number (1, 2, 3…), Value = the label shown on the page, e.g. "Concurrent Sessions 1 — 9-9:50 a.m." |
| `activitySessions` | Relationship → CtlActivitySession | No | Multi-track conferences only. | Only for multi-track conferences. Add one entry per individual talk/session. Leave empty for regular single-session activities. |

#### CTL_Activity_Types (category tree)

Top-level category — Name: `CTL_Activity_Types` · Key/Variable: `ctlactivitytypes` · 16 child categories.

| Name | Key | Variable | Sort |
|----|----|----|----|
| Award | `award` | `award` | 0 |
| Book Club | `bookClub` | `bookClub` | 0 |
| Community of Practice | `communityOfPractice` | `communityOfPractice` | 0 |
| Conference | `conference` | `conference` | 0 |
| Fellowship | `fellowship` | `fellowship` | 0 |
| Grant | `grant` | `grant` | 0 |
| Information Session | `informationSession` | `informationSession` | 0 |
| Paid | `paid` | `paid` | 0 |
| Presentation | `presentation` | `presentation` | 0 |
| Professional Development | `professionalDevelopment` | `professionalDevelopment` | 0 |
| Research Grant | `researchGrant` | `researchGrant` | 0 |
| Service | `service` | `service` | 0 |
| Teaching Grant | `teachingGrant` | `teachingGrant` | 0 |
| Working Group | `workingGroup` | `workingGroup` | 0 |
| Working Session | `workingSession` | `workingSession` | 0 |
| Workshop | `workshop` | `workshop` | 0 |


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
  (formerly `/centers/ctl/instructional-resources/{urlTitle}`; live URL Map Pattern update
  on the content type itself still pending, see CLAUDE.md's Next Up).

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `resourceTitle` | Text | Yes | Full display title. |
| `urlTitle` | Text | Yes | Must have **System Indexed + Unique** checked or the URL Map silently fails to bind (see CLAUDE.md). Lowercase, hyphens only. |
| `shortDescription` | Textarea | Yes | Listing-card blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `body` | WYSIWYG | Yes | Main content. |
| `highlights` | Textarea | No | Up to 4 short "need to know" takeaway bullets, one per line. Rendered in a colored callout box at the top of the detail page, before `body`. |
|    |    |    |    |
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

#### CTL_Inst. Resource_Types (category tree)

Top-level category — Name: `CTL_Inst. Resource_Types` · Key/Variable: `ctlInstResourceTypes` ·
9 child categories. Drives `formatType`.

| Name | Key | Variable | Sort |
|----|----|----|----|
| Course Template | `courseTemplate` | `courseTemplate` | 0 |
| Deep Dive | `deepDive` | `deepDive` | 0 |
| Listicles | `listicles` | `listicles` | 0 |
| Material Template | `materialTemplate` | `materialTemplate` | 0 |
| Presentation | `presentation-1` | `presentation` | 0 |
| Recording | `recording` | `recording` | 0 |
| Teaching Tip | `teachingTip` | `teachingTip` | 0 |
| Slide Deck | `slide-deck` | `slideDeck` | 3 |
| Guide | `guide` | `guide` | 5 |

Note: this tree's "Presentation" key (`presentation-1`) is distinct from — and not to be
confused with — `CTL_Activity_Types`' unrelated "Presentation" category (key `presentation`,
no suffix); they're separate trees on separate content types that happen to share a display
name.

#### CTL_Inst. Resource_Topics (category tree)

Top-level category — Name: `CTL_Inst. Resource_Topics` · Key/Variable: `ctlInstResourceTopics` ·
6 child categories. Drives `topics` (the topic pill cloud's 6 parent-category headings).

| Name | Key | Variable | Sort |
|----|----|----|----|
| Course Policies, Norms, & Expectations | `course-policies-norms-expectations` | `coursePoliciesNormsExpectations` | 1 |
| Curriculum Design | `curriculum-design` | `curriculumDesign` | 2 |
| Student Engagement | `student-engagement` | `studentEngagement` | 3 |
| Assessment & Grading | `assessment-grading` | `assessmentGrading` | 4 |
| Course Materials & Tooling | `course-materials-tooling` | `courseMaterialsTooling` | 5 |
| Classroom Climate & Student Voice | `classroom-climate-student-voice` | `classroomClimateStudentVoice` | 6 |

#### CTL_Inst. Resource_Frameworks (category tree)

Top-level category — Name: `CTL_Inst. Resource_Frameworks` · Key/Variable:
`ctlinstResourceframeworks` · 28 child categories. Drives `frameworks` (display-only badges).

| Name | Key | Variable | Sort |
|----|----|----|----|
| Bloom's Taxonomy | `bloomsTaxonomy` | `bloomsTaxonomy` | 0 |
| Motivation | `motivation` | `motivation` | 1 |
| Intellectual Respect | `intellectual-respect-framework` | `intellectualRespectFramework` | 2 |
| Cognitive Load | `cognitive-load` | `cognitiveLoad` | 3 |
| Metacognition | `metacognition` | `metacognition` | 4 |
| Cognitive Expertise | `cognitive-expertise` | `cognitiveExpertise` | 5 |
| Transfer | `transfer` | `transfer` | 6 |
| Asset-Based Approaches | `asset-based-approaches` | `assetBasedApproaches` | 7 |
| Forms of Capital | `forms-of-capital` | `formsOfCapital` | 8 |
| Self-Efficacy & Determination | `self-efficacy-determination` | `selfEfficacyDetermination` | 9 |
| FoK / FoI | `fok-foi` | `fokFoi` | 10 |
| Culturally Sustaining Pedagogy | `culturally-sustaining-pedagogy` | `culturallySustainingPedagogy` | 11 |
| ZPD + Scaffolding | `zpd-scaffolding` | `zpdScaffolding` | 12 |
| Cognitive Apprenticeship | `cognitive-apprenticeship` | `cognitiveApprenticeship` | 13 |
| Problem-Based Learning | `problem-based-learning` | `problemBasedLearning` | 14 |
| Inquiry-Based Learning | `inquiry-based-learning` | `inquiryBasedLearning` | 15 |
| Maslow's Hierarchy | `maslows-hierarchy` | `maslowsHierarchy` | 16 |
| Situated Cognition | `situated-cognition` | `situatedCognition` | 17 |
| Social Model | `social-model` | `socialModel` | 18 |
| Feminist + Queer Theories | `feminist-queer-theories` | `feministQueerTheories` | 19 |
| Project-Based Learning | `project-based-learning` | `projectBasedLearning` | 20 |
| Engineering Design Process | `engineering-design-process` | `engineeringDesignProcess` | 21 |
| Achievement-Goal Theory | `achievement-goal-theory` | `achievementGoalTheory` | 22 |
| UDL | `udl` | `udl` | 24 |
| Communities of Practice | `communities-of-practice` | `communitiesOfPractice` | 25 |
| Decolonize the Curriculum | `decolonize-the-curriculum` | `decolonizeTheCurriculum` | 26 |
| Learning by Design | `learning-by-design` | `learningByDesign` | 27 |
| Expectancy-Value Theory | `expectancy-value-theory` | `expectancyValueTheory` | 28 |

Note: sort jumps from 22 to 24 (no 23) in the live data — not a doc error, just a gap in the
source export.


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
| `affiliations` | Checkbox | Values are capitalized — `"CTL"`, `"FDC"`, `"Other"` — `.contains()` is case-sensitive. Historically drove three sections (CTL Staff / FDC Members / Other Affiliates) on the old combined About Us page; Other Affiliates was dropped from the IA entirely, so only `CTL` (Meet the CTL Staff / Request Support) and `FDC` (Faculty Development Committee) are still used. |
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
| `nextStepsNote` | WYSIWYG | **NEW, not yet added in dotCMS** (2026-08-06) — freeform "Next Steps" text: drop-in hours, a jump-link phrase pointing elsewhere on the page (e.g. "see Make an Appointment below"), etc. Renders alongside the access button (both if both are filled in); hidden unless non-empty AND `isCurrentlyAvailable`. |
| `thumbnailImage` | Image | FileAssetMap — `.path` + `.fileName`. |
| `isCurrentlyAvailable` | Checkbox | Values `yes`/`no`. No longer shows a badge (dropped as redundant 2026-08-06) — instead gates the whole "Next Steps" section (button + note); unavailable services still show their description, just with no Next Steps content beneath it. |

No sort-order field — display order is whatever the pull returns.


### CtlReport — annual reports (PROPOSED, not yet created in dotCMS)

* Proposed 2026-08-06 for `widgets/ctl-widget_reports-grid.html` (About the CTL > Impact &
  Reports). Not built against CtlAttachment because CtlAttachment has no parent-link field —
  it's only reachable via a relationship from CtlActivity/CtlActivitySession/
  CtlInstructionalResource, and a report isn't naturally a child of any of those. See that
  widget file's header for the full reasoning.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `reportTitle` | Text | Yes | e.g. "2024-2025 Annual Report." |
| `reportYearLabel` | Text | Yes | Display label, e.g. "2024-2025." |
| `reportYearStart` | Text (whole number) | Yes | Sort key, e.g. `2024`. Same pattern as CtlPersonnel's `rank` — will already be a `java.lang.Long` at the Velocity layer despite the "Text" editor label; use directly, no String round-trip. |
| `reportSummary` | Textarea | No | 1–2 sentence blurb. |
| `reportThumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `reportFile` | File | Yes | FileAssetMap (not BinaryMap, despite field type "File") — `.path` + `.fileName`, same pattern as `videoFile`. |


### CtlPastRecipient — past award/grant/fellowship winners (PROPOSED, not yet created in dotCMS)

* Proposed 2026-08-06 for `widgets/ctl-widget_past-award-recipients_past-recipients.html`
  (Funding > Past Recipients). Distinct from CtlActivity, which models the call/opportunity
  itself — this models the person who won it, optionally linking back to the CtlActivity
  record via `relatedActivity`. See that widget file's header for the full reasoning.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `recipientName` | Text | Yes | — |
| `awardYearLabel` | Text | Yes | Display label, e.g. "2024-2025." |
| `awardYearStart` | Text (whole number) | Yes | Sort key, e.g. `2024`. Same `rank`-style runtime-Long pattern as CtlReport's `reportYearStart` above. |
| `awardCategories` | Category (multi) | No | Reuses the existing `CTL_Activity_Types` tree (Award/Grant/Fellowship/etc.) rather than a new tree, so badges match Current Opportunities. |
| `department` | Text | No | — |
| `summary` | Textarea | No | What they did / won it for. |
| `photo` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `relatedActivity` | Relationship → CtlActivity | No | Links back to the original opportunity call, if it still exists as a live CtlActivity record. |


## Widgets Index

| Widget (dotCMS variable) | File | Content Type(s) | Status |
|----|----|----|----|
| `CtlWidgetActivityDetail` | `widgets/ctl-site_activity-detail.html` | CtlActivity | ✅ Live; vanity-URL routing matches the Events/Funding IA — see CLAUDE.md |
| *(Events, split for reuse)* | `widgets/ctl-widget_upcoming-events_events.html` + `widgets/ctl-widget_past-events_events.html` | CtlActivity | ✅ Fixed in code; live migration pending (old `ctl-site_event-parent.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Funding > Current Opportunities, split for reuse, relocated under Funding)* | `widgets/ctl-widget_active-opportunities_current-opportunities.html` | CtlActivity | ✅ Fixed in code; live migration pending (old `ctl-site_opportunity-parent.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Funding > Past Recipients, split for reuse, relocated under Funding)* | `widgets/ctl-widget_missed-opportunities_past-recipients.html` + `widgets/ctl-widget_past-award-recipients_past-recipients.html` | CtlActivity + CtlPastRecipient | ✅ Both in code; the second needs the CtlPastRecipient content type created in dotCMS first (see that section above) before either the type or the live migration can happen |
| `CtlWidgetInstructionalParent` (v8) | `widgets/ctl-site_instRes-parent.html` | CtlInstructionalResource | ✅ Live on "For Course Design" (formerly "Instructional Resources") — URL Map Pattern update still pending |
| `CtlWidgetInstDetail` | `widgets/ctl-site_instRes-detail` | CtlInstructionalResource | ✅ Live — URL Map Pattern update still pending |
| *(About the CTL, split for reuse)* | `widgets/ctl-widget_ctl-staff-profiles.html` (Meet the CTL Staff) + `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` (Faculty Development Committee) | CtlPersonnel | ✅ Code ready; live migration pending (old `ctl_site_about-us.html` is now a deprecation stub) — see CLAUDE.md Next Up. "Impact & Reports" subpage/widget not yet built |
| *(Request Support, split for reuse)* | `widgets/ctl-widget_our-services_request-support.html` + a second instance of `widgets/ctl-widget_ctl-staff-profiles.html` | CtlService, CtlPersonnel | ✅ Code ready; needs a `sectionHeading` field added to the CTL Staff profiles widget's content type, then live migration (old `ctl-site_get-help.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(Welcome to the CTL homepage, split for reuse)* | `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html` + `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html` + `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html` | CtlActivity, CtlInstructionalResource | ✅ Code ready (nav tiles updated to 5 tiles for the Funding/Resources restructure); live migration pending (old `ctl-site_home.html` is now a deprecation stub) — see CLAUDE.md Next Up |
| *(shared template)* | `DEPRECATED/ctl-site_page template.html` | — (page-level `showOnMenu` field) | ✅ Live |
| *(Impact & Reports > reports grid only — see Content Blocks Index below for its two sibling pieces)* | `widgets/ctl-widget_reports-grid.html` | CtlReport | ✅ In code (2026-08-06); needs the CtlReport content type created in dotCMS first (see that section above) before either the type or the live migration can happen |

All widgets above were originally one file per page; each has been split into multiple
standalone widgets in `widgets/` for reuse (dev-checklist.md: "split up sections into
multiple widgets for potential reuse"). The old combined files are deprecation stubs
pointing at their replacements, kept in the repo only until each page's live dotCMS
migration is confirmed — see CLAUDE.md's Next Up section for per-page migration steps.

For what each widget actually does section-by-section, read the widget file itself — the
header comment block in each one is kept current as a purpose summary.


## Content Blocks Index

Static intro/overview/blurb copy — no `$dotcontent.pull()`, no listing/detail logic — split
out of `widgets/` into its own `content blocks/` folder 2026-08-07, since in dotCMS these are
authored as "Web Page Content" blocks rather than Widget content type/Code assets (see
dev-checklist.md's naming-convention note for the parallel `CTL content_...` naming). Files
below were renamed from `ctl-widget_...` to `ctl-content_...` in the same move.

| Content block (dotCMS variable) | File | Page | Status |
|----|----|----|----|
| *(Funding overview)* | `content blocks/ctl-content_funding-overview_funding.html` | Funding | ✅ In code (2026-08-06); live migration pending |
| *(Resources overview)* | `content blocks/ctl-content_resources-overview.html` | Resources | ✅ In code (2026-08-06); live migration pending |
| *(Events overview)* | `content blocks/ctl-content_events-overview_events.html` | Events | ✅ In code (2026-08-06); live migration pending |
| *(Impact & Reports intro + outcomes, split for reuse)* | `content blocks/ctl-content_impact-reports-intro.html` (static blurb) + `content blocks/ctl-content_outcomes-overview.html` (static stat tiles, placeholder numbers) | About the CTL > Impact & Reports | ✅ In code (2026-08-06); live migration pending. Third sibling piece, the reports grid itself, pulls live CtlReport data and stays a widget — see the Widgets Index above |
| *(FDC description)* | `content blocks/ctl-content_fdc-description.html` | About the CTL > Faculty Development Committee | ⬜ Not yet built — empty file |
| *(Services overview)* | `content blocks/ctl-content_services-overview.html` | Request Support | ⬜ Not yet built — empty file |


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
* **CtlReport** and **CtlPastRecipient**: proposed content types (2026-08-06), not yet
  created in dotCMS — see their sections above for full field lists. `ctl-widget_reports-grid.html`
  and `ctl-widget_past-award-recipients_past-recipients.html` are written against these
  schemas and will pull 0 records (rendering their "coming soon" / "none on record" fallback)
  until the types + fields are created.
* **Outcomes stat tiles** (`content blocks/ctl-content_outcomes-overview.html`, About the
  CTL > Impact & Reports): all numbers are hardcoded PLACEHOLDERS — replace with real
  figures before this goes live.


