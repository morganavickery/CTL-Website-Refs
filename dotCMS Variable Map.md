# CTL Website — dotCMS Variable Map

Index of every CTL content type, its fields (Velocity variable, field type, required,
access notes), and its category trees. This is a reference, not a debugging log — for
architecture/infra context see CLAUDE.md; for outstanding work see dev-checklist.md.

Field names ("Variable") are the Velocity variable used in templates/widgets, e.g.
`$item.eventTitle`. Compiled from the dotCMS Content Type REST API.


## CtlActivity — unified events + opportunities

* **Base Type:** Content · **Site:** www2.cortland.edu
* **URL Map Pattern:** `/centers/ctl/activities/{urlMapPattern}` · **Detail Page ID:** `677c33a575e4ebbf53f573b51c23e87a`
* One record can be an event, an opportunity, both, or neither — distinguished by `isEvent`/`isOpportunity`.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `title` | Text | Yes | Page title, cards, browser tab. |
| `urlMapPattern` | Text | Yes | Slug, e.g. `workshop-udl-2026`. Not Lucene-indexed — filter in Velocity. |
| `categories` | Category (multi) | Yes | Tree: `CTL_Activity_Types`. |
| `shortDescription` | Textarea | No | Card preview blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `longDescription` | WYSIWYG | Yes | Full body. |
| `primaryContact` | Text | No | "Name, email" format. |
| `attachments` | Relationship → CtlAttachment | No | Parent side; download buttons. |
| `isEvent` | Checkbox | No | Values `Yes`/`true`. |
| `isConference` | Checkbox | No | Values `yes`/`True` — note the capitalized `True`, unlike `isEvent`. Multi-session events; if checked, `isEvent` should also be checked. |
| `isOpportunity` | Checkbox | No | — |
| `signupType` | Radio | Yes | `open` / `registration` / `application`. |
| `registerApplyLink` | Text (URL) | No | — |
| `signupDeadline` | Date | No | Blank = "ongoing." |
| `acceptsSubmissions` | Checkbox | No | Separate from registering to attend (calls for proposals). |
| `signupCapacityReached` | Checkbox | No | Manually close signup early. |
| `submissionLabel` | Text | No | e.g. "Submit a Paper." Blank → "Submit to Present." |
| `submissionLink` | Text (URL) | No | Only if `acceptsSubmissions`. |
| `submissionDeadline` | Date-and-Time | No | Only if `acceptsSubmissions`. |
| `eventLocation` | Text | No | e.g. "Moffett 208." |
| `eventStartDate` | Date | No | Blank end date on `eventEndDate` = single-day event. |
| `eventEndDate` | Date | No | Blank = single-day event. |
| `eventStartTime` | Time | No | — |
| `eventEndTime` | Time | No | — |
| `oppAmountPaid` | Text | No | Numbers only; `$` added by the template. |
| `oppLaborExpectation` | Textarea | No | \~10 words, what earns the compensation. |
| `sessiontimeblockdefinitions` | Key-Value | No | Live schema variable is all-lowercase (not camelCase `sessionTimeBlockDefinitions`). Key = block number, Value = display label. Multi-track conferences only. |
| `activitySessions` | Relationship → CtlActivitySession | No | Multi-track conferences only. |

### CTL_Activity_Types (category tree)

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


## CtlActivitySession — conference session sub-records

* **Base Type:** Content · **Site:** www2.cortland.edu · No URL Map — reached only via `CtlActivity.activitySessions`.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `sessionTimeBlock` | Text | Yes | Values 1–6 documented, but real data has used 7+ — don't assume a hard cap. Must match a key in the parent's `sessiontimeblockdefinitions`. |
| `sessionrowsortorder` | Text (number) | Yes | All-lowercase schema variable (not camelCase). Order within a time block. |
| `sessionRoom` | Text | Yes | e.g. "204-205." |
| `sessionTitle` | Text | Yes | Shown on the collapsed bar. |
| `sessionSpeakers` | Text | Yes | Comma-separated names. |
| `sessionTags` | Text | No | Comma-separated free-form keywords; split/trimmed for badges. |
| `sessionabstract` | WYSIWYG | Yes | All-lowercase schema variable (not camelCase). Shown when expanded. |
| `sessionAttachments` | Relationship → CtlAttachment | No | Own relationship, separate from the parent activity's. |


## CtlAttachment — downloadable files

* **Base Type:** Content · **Site:** www2.cortland.edu · No URL Map — reached only via relationship from CtlActivity/CtlActivitySession/CtlInstructionalResource.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `ctlAttachmentlabel` | Text | No | Download-button display text. |
| `ctlAttachmentfile` | File | No | FileAssetMap — `.path` + `.fileName` (not `.rawUri`/`.thumbnailUri`, which are BinaryMap accessors). |
| `ctlAttachmentsortorder` | Text | No | Optional manual ordering. |
| `ctlAttachmenttype` | Radio | No | Slide Deck / Agenda / Schedule / Handout / Reading. |

No parent-link field on this type — association happens from the *other* side (the
relationship field on CtlActivity/CtlInstructionalResource/CtlActivitySession).


## CtlInstructionalResource

* **Base Type:** Content · **Site:** www2.cortland.edu
* No native URL Map field. Detail links built manually: `/centers/ctl/resources/instructional-design-resources/{urlTitle}`

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `resourceTitle` | Text | Yes | Full display title. |
| `urlTitle` | Text | Yes | Requires **System Indexed + Unique** checked or the URL Map silently fails to bind. Lowercase, hyphens only. |
| `shortDescription` | Textarea | Yes | Listing-card blurb. |
| `thumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `body` | WYSIWYG | Yes | Main content. |
| `highlights` | Textarea | No | Up to 4 short "need to know" bullets, one per line. |
| `videoFile` | File | No | FileAssetMap — `.path` + `.fileName` (not BinaryMap). |
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
| `featured` | Radio | No | `.selectValue` ("yes"/"no"). |
| `relatedInstructionalResources` | Relationship → CtlInstructionalResource | No | Self-relationship. |
| `relatedEvents` | Relationship → CtlActivity | No | — |
| `relatedTechSupport` | Relationship → CtlTechSupport | No | Schema exists; CtlTechSupport itself isn't built yet. |

### CTL_Inst. Resource_Types (category tree)

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

Note: this tree's "Presentation" key (`presentation-1`) is distinct from
`CTL_Activity_Types`' unrelated "Presentation" category (key `presentation`) — separate
trees on separate content types that happen to share a display name.

### CTL_Inst. Resource_Topics (category tree)

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

### CTL_Inst. Resource_Frameworks (category tree)

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

Note: sort jumps from 22 to 24 (no 23) in the live data — a gap in the source export, not a doc error.


## CtlPersonnel — CTL/FDC staff and affiliates

* **Base Type:** Content · No URL Map — all personnel render on About the CTL (Meet the CTL Staff) / Request Support.

| Variable | Field Type | Note |
|----|----|----|
| `name` | Text | Full display name. |
| `pronouns` | Radio | `.selectValue` + parallel `.values`/`.options` lookup for the display label. `"dont show pronouns"` is a real option, treated as blank. |
| `headshotPhoto` | Image | FileAssetMap — `.path` + `.fileName`. |
| `jobTitle` | Text | Shown on its own line on the FDC grid card. |
| `degrees` | Text | e.g. "PhD," "MSEd." On the FDC grid card, a case-insensitive "phd" substring appends ", PhD" to the displayed name; other degree values aren't otherwise rendered. |
| `serviceYearStart` / `serviceYearEnd` | Text (whole number) | FDC term-of-service years, shown on the FDC grid card as "2022-2024" (falls back to just the start year if only one is set). |
| `rank` | Text (whole number) | Sort key, ascending, blank sorts last. Runtime type is already `java.lang.Long` despite the "Text" editor label — use `$item.rank` directly, no String round-trip. |
| `affiliations` | Checkbox | Values are capitalized — `"CTL"`, `"FDC"`, `"Other"` — `.contains()` is case-sensitive. Only `CTL` and `FDC` are currently used in the IA. |
| `emailCortlandEdu` | Text | Rendered as `mailto:`; link hidden if empty. |
| `bookWithMeFullLink` | Text | External booking URL; "Meet with Me" button hidden if empty. |
| `websitePromo` | Text (URL) | Shown on both instances of the staff-profiles widget. |
| `buttonLabel` | Text | Label for the `websitePromo` button, falls back to "Visit My Website." Distinct from CtlService's `serviceButtonLabel`. |
| `areasOfExpertise` | Textarea | One bullet per line (splits on `\n`). |
| `howICanHelp` | Textarea | Same one-bullet-per-line convention. |


## CtlService — Request Support page service cards

* **Base Type:** Content · No URL Map — all services render as cards on Request Support.

| Variable | Field Type | Note |
|----|----|----|
| `serviceName` | Text | Card title. |
| `serviceShortDescription` | Textarea | Card lead text, renders in full. |
| `longDescription` | WYSIWYG | Optional. Revealed via a "Learn More" toggle that expands the tile to full grid width. |
| `serviceAccessLink` | Text | External URL; button hidden unless non-empty AND `isCurrentlyAvailable`. |
| `serviceButtonLabel` | Text | Falls back to "Access this Service." |
| `nextSteps` | Textarea | Freeform "Next Steps" text (drop-in hours, a jump-link phrase, etc.). Hidden unless non-empty AND `isCurrentlyAvailable`. |
| `thumbnailImage` | Image | FileAssetMap — `.path` + `.fileName`. |
| `isCurrentlyAvailable` | Checkbox | Values `yes`/`no`. Gates the whole "Next Steps" section (button + note). |

No sort-order field — display order is whatever the pull returns.


## CtlReport — annual reports (PROPOSED, not yet created in dotCMS)

For `widgets/ctl-widget_reports-grid.html` (About the CTL > Impact & Reports).

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `reportTitle` | Text | Yes | e.g. "2024-2025 Annual Report." |
| `reportYearLabel` | Text | Yes | Display label, e.g. "2024-2025." |
| `reportYearStart` | Text (whole number) | Yes | Sort key, e.g. `2024`. Same runtime-Long pattern as CtlPersonnel's `rank`. |
| `reportSummary` | Textarea | No | 1–2 sentence blurb. |
| `reportThumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `reportFile` | File | Yes | FileAssetMap — `.path` + `.fileName`. |


## CtlPastRecipient — past award/grant/fellowship winners (PROPOSED, not yet created in dotCMS)

For `widgets/ctl-widget_past-award-recipients_past-recipients.html` (Funding > Past Recipients).
Distinct from CtlActivity, which models the call/opportunity itself — this models the person
who won it, optionally linking back via `relatedActivity`.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `recipientName` | Text | Yes | — |
| `awardYearLabel` | Text | Yes | Display label, e.g. "2024-2025." |
| `awardYearStart` | Text (whole number) | Yes | Sort key, e.g. `2024`. Same runtime-Long pattern as `rank`/`reportYearStart`. |
| `awardCategories` | Category (multi) | No | Reuses the `CTL_Activity_Types` tree. |
| `department` | Text | No | — |
| `summary` | Textarea | No | What they did / won it for. |
| `photo` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `relatedActivity` | Relationship → CtlActivity | No | Links back to the original opportunity call, if still live. |


