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
| `oppAmountPaid` | Text | No | Numeric values (e.g. `500`, `1,500.00`) get `$` prepended by the template; non-numeric values (e.g. `Free Book Copy`) render as entered, no `$`. Already-prefixed values (`$500`) are left as-is either way. |
| `oppLaborExpectation` | Textarea | No | \~10 words, what earns the compensation. |
| `sessiontimeblockdefinitions` | Key-Value | No | Live schema variable is all-lowercase (not camelCase `sessionTimeBlockDefinitions`). Key = block number, Value = display label. Multi-track conferences only. |
| `activitySessions` | Relationship → CtlActivitySession | No | Multi-track conferences only. |

### CTL_Activity_Types (category tree)

Top-level category — Name: `CTL_Activity_Types` · Key/Variable: `ctlactivitytypes` · 19 child categories.

| Name | Key | Variable | Sort |
|----|----|----|----|
| Award | `award` | `award` | 1 |
| Book Club | `bookClub` | `bookClub` | 2 |
| Community of Practice | `communityOfPractice` | `communityOfPractice` | 3 |
| Conference | `conference` | `conference` | 4 |
| Fellowship | `fellowship` | `fellowship` | 5 |
| Grant | `grant` | `grant` | 6 |
| Information Session | `informationSession` | `informationSession` | 7 |
| Paid | `paid` | `paid` | 8 |
| Presentation | `presentation` | `presentation` | 9 |
| Professional Development | `professionalDevelopment` | `professionalDevelopment` | 10 |
| Research Grant | `researchGrant` | `researchGrant` | 11 |
| Service | `service` | `service` | 12 |
| Teaching Grant | `teachingGrant` | `teachingGrant` | 13 |
| Working Group | `workingGroup` | `workingGroup` | 14 |
| Working Session | `workingSession` | `workingSession` | 15 |
| Workshop | `workshop` | `workshop` | 16 |
| New Faculty | `newFaculty` | `newFaculty` | 17 |
| Staff | `staff` | `staff` | 18 |
| FDC | `fdc` | `fdc` | 19 |


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
| `sessionAbstract` | WYSIWYG | Yes | camelCase (unlike `sessionrowsortorder`). Shown when expanded. |
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
| `relatedTechSupport` | Relationship → CtlTechSupport | No | |

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


## CtlPersonnel — CTL/FDC staff, affiliates, and past award/grant recipients

* **Base Type:** Content · No URL Map — all personnel render on About the CTL (Meet the CTL Staff) / Request Support / Opportunities > Past Recipients.
* Form is organized into a default section plus three tabs — **CTL Staff**, **FDC**, **Associate** — and a **Recipients** tab holding `honorRecieved`. The tab grouping is editor-UI organization only; it has no effect on Velocity access (every field is still `$item.fieldVar` regardless of which tab it lives on).

| Variable | Field Type | Note |
|----|----|----|
| `name` | Text | Full display name. |
| `pronouns` | Radio | `.selectValue` + parallel `.values`/`.options` lookup for the display label. `"dont show pronouns"` is a real option, treated as blank. |
| `headshotPhoto` | Image | FileAssetMap — `.path` + `.fileName`. |
| `jobTitle` | Text | Shown on its own line on the FDC grid card. |
| `department` | Text | Academic/administrative department. Distinct from `degrees` — don't conflate the two. |
| `degrees` | Text | e.g. "PhD," "MSEd." On the FDC grid card, a case-insensitive "phd" substring appends ", PhD" to the displayed name; other degree values aren't otherwise rendered. |
| `affiliations` | Checkbox (multi) | Values are capitalized — `"CTL"`, `"FDC"`, `"Affiliate"`, `"Recipient"`, `"Other"` — `.contains()` is case-sensitive. A record can hold more than one (e.g. an actual FDC committee member who also won an award is `"FDC,Recipient"`). `CTL` gates Meet the CTL Staff/Request Support; `FDC` gates the FDC grid; `Affiliate` is for Faculty Associates (Associate tab); `Recipient` marks a past-honor-only record with no other staff role. |
| `emailCortlandEdu` | Text | Rendered as `mailto:`; link hidden if empty. |
| `websitePromo` | Text (URL) | Shown on both instances of the staff-profiles widget. Only populate with an actual URL — a page title/label with no link should be left blank. |
| `buttonLabel` | Text | Label for the `websitePromo` button, falls back to "Visit My Website." Distinct from CtlService's `serviceButtonLabel`. |
| `rank` | Text (whole number) | **CTL Staff tab.** Sort key, ascending, blank sorts last. Runtime type is already `java.lang.Long` despite the "Text" editor label — use `$item.rank` directly, no String round-trip. |
| `bookWithMeFullLink` | Text | **CTL Staff tab.** External booking URL; "Meet with Me" button hidden if empty. |
| `areasOfExpertise` | Textarea | **CTL Staff tab.** One bullet per line (splits on `\n`). |
| `howICanHelp` | Textarea | **CTL Staff tab.** Same one-bullet-per-line convention. |
| `serviceYearStart` / `serviceYearEnd` | Text (whole number) | **FDC tab.** FDC term-of-service years, shown on the FDC grid card as "2022-2024" (falls back to just the start year if only one is set). |
| `associateTitle` | Text | **Associate tab.** Role label, e.g. "SoTL Faculty Associate" (hint: `"_____ Associate"`). For Faculty Associates — not yet rendered by any content block. |
| `roleDescription` | Textarea | **Associate tab.** What the associate role covers. |
| `termYearStart` / `termYearEnd` | Text | **Associate tab.** Associate's term years — separate field pair from the FDC tab's `serviceYearStart`/`serviceYearEnd`, don't conflate. |
| `honorRecieved` | Relationship → CtlActivity (multi) | **Recipients tab.** One entry per honor won. A person shows up as a Past Recipient once per honor whose linked CtlActivity carries an Award/Grant/Fellowship/Teaching Grant/Research Grant category — see `ctl-content_past-award-recipients_opportunities.html`. No per-honor date lives on CtlPersonnel: that content block parses the year from a required `(YYYY)` suffix on each honor's own CtlActivity `title` instead. |


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

For `content blocks/ctl-content_reports-grid.html` (About the CTL > Impact & Reports).

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `reportTitle` | Text | Yes | e.g. "2024-2025 Annual Report." |
| `reportYearLabel` | Text | Yes | Display label, e.g. "2024-2025." |
| `reportYearStart` | Text (whole number) | Yes | Sort key, e.g. `2024`. Same runtime-Long pattern as CtlPersonnel's `rank`. |
| `reportSummary` | Textarea | No | 1–2 sentence blurb. |
| `reportThumbnail` | Image | No | FileAssetMap — `.path` + `.fileName`. |
| `reportFile` | File | Yes | FileAssetMap — `.path` + `.fileName`. |


## CtlTechSupport — KB article index

dotCMS content type name "CTL Tech Resource", variable `CtlTechSupport`. For
`content blocks/ctl-content_tech-support-listing_technologies.html` (Resources > For
Technologies). Each card links straight out to its TDX/university knowledge base article in
a new tab.

The content type has a `urlMapPattern` (`/centers/ctl/resources/technology-resources/{urlTitle}`)
and a detail page configured, but there is no `urlTitle` field on the type — this routing is
unused/dead (the content block links out directly, per above) and will not resolve if hit. Leave
it alone unless asked to build an on-site detail page for these records.

| Variable | Field Type | Req? | Note |
|----|----|----|----|
| `articleTitle` | Text | No | Listing-card title, matches the KB article's own title. |
| `shortDescription` | Textarea | No | Listing-card blurb. |
| `knowledgeBaseUrl` | Text | No | External TDX/KB article link; card opens it in a new tab (`target="_blank"`). |
| `toolsCovered` | Multi-Select | No | What platform the article covers. Options below. |
| `tasksFunctions` | Multi-Select | No | What task/function the article addresses. Options below. |
| `host1` | Host-Folder | Yes | Site/folder identifier, same pattern as other CSV imports. |

`toolsCovered`/`tasksFunctions` are Multi-Select fields (not Category), so editors can
add/reorder options directly on the field in Content Types — no separate Categories admin
step. Access in Velocity via CheckboxMap-style accessor: `$r.toolsCovered.selectedValues` /
`$r.tasksFunctions.selectedValues` (see CLAUDE.md's CheckboxMap gotcha), not `.categoryName`
iteration.

Both filter facets are multiselect: selecting more than one value within a facet is OR'd
(Tool = Brightspace OR YuJa), while the two facets are AND'd against each other (Tool =
Brightspace AND Task = Grading) — this differs from the Instructional Design listing's
topic cloud, which ANDs every selection regardless of dimension.

`CtlInstructionalResource.relatedTechSupport` targets this content type (see that type's
field table above) — confirm the relationship binds correctly now that CtlTechSupport
exists.

### `toolsCovered` field options

```
brightspace | Brightspace
yuja | YuJa
turnitin | Turnitin
perusall | Perusall
starfish | Starfish
selectsurvey | Select Survey
m365 | Microsoft 365
acrobat | Adobe Acrobat
indesign | InDesign
classroomdisplays | Classroom Displays
webex | Webex
waymaker | Waymaker
banner | Banner
anthology | Anthology
google | Google Workspace
```

### `tasksFunctions` field options

```
accessibility | Accessibility & Remediation
grading | Grading & Assessment
coursecontent | Course Content Creation
communication | Communication & Notifications
courseadmin | Course Administration
video | Video
training | Training & Professional Development
reporting | Reporting & Analytics
troubleshooting | Troubleshooting & Known Issues
classroomtech | Classroom Technology & AV Support
requesthelp | Request Help / Appointment
```


