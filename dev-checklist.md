- [ ] swap degree for 
- [ ] \
- [x] Side scroll cards: (checked 2026-08-07 — both bullets addressed)
  * Event/Event & Opportunity label colors need to be consistent with Brand Identity — FIXED
    2026-08-07: the carousel's type badges (`ctl-widget_upcoming-activities-carousel.html`)
    used generic Bootstrap `text-bg-success`/`text-bg-dark`/`text-bg-primary`, not CTL
    maroon. Now use `.ctl-badge` with per-state inline colors, same pattern as Activity
    Detail's Registration/Application badges: Event = `--ctl-maroon-tint`/`--ctl-maroon`,
    Opportunity = new `--ctl-navy-tint`/`--ctl-navy` (Secondary "Deep Navy" `#04202E`, added
    since 2 independent type flags need 3 distinguishable states), Event & Opportunity
    (combined) = solid `--ctl-maroon`/`#fff`. See `styles/ctl-widgets.css`'s design-tokens
    comment and the brand style guide's "Notes for future CTL site application" section.
  * Cards are cut off on the right. Is there a different way we can visually indicate that
    there is a side scroll? — addressed: both homepage carousels intentionally show a
    partial peek of the next card (\~2.3 cards visible) plus prev/next scroll buttons
    (`.ctl-hscroll-btn`), per CLAUDE.md's carousel display-pattern note
- [x] Headings are clickable/linked for each event card. These should visually show that they
  are clickable. (checked 2026-08-07 — the event/opportunity listing card headings, e.g.
  `ctl-widget_upcoming-events.html`/`ctl-widget_active-opportunities.html`, ARE wrapped in a
  link, but styled `text-decoration: none; color: inherit;` with no hover rule — they still
  don't visually read as clickable. The homepage carousel's cards (`.ctl-activity-card`) DO
  get a **maroon**+underline hover treatment, so this is only unresolved on the listing pages, not
  the carousel. FIXED 2026-08-07: added `.ctl-listing-title-link` to `styles/ctl-widgets.css`
  — same plain-until-hovered, then maroon+underline treatment as `.ctl-activity-card` — and
  applied it to the title link in all 4 single/2-column listing widgets: `ctl-widget_upcoming-events.html`,
  `ctl-widget_past-events_events.html`, `ctl-widget_active-opportunities.html`,
  `ctl-widget_missed-opportunities.html`, replacing the old inline
  `text-decoration: none; color: inherit;`.)
  * Recommend hiding past events or the scroll will get extremely long over time (and slow page load)

    \

  \
- [x] H1 should not appear twice on the page
- [x] Recommend putting all topics/keywords in one group below article (checked 2026-08-07:
  `ctl-site_instRes-detail` groups byline (author/updated), format/modality tags, AND topic
  chips into a single block after the body/downloads/video, not scattered — see lines \~198-227)
- [x] Recommend changing the topics/keywords styling so they don't look clickable like the
  filters (checked 2026-08-07: detail-page topic chips use `.ctl-ir-topic-chip` — square
  4px-radius grey tag, no `cursor: pointer`, no hover state — deliberately distinct from the
  listing page's clickable `.ctl-res-pill` filter — rounded 20px pill with pointer cursor and
  hover/active states. See `styles/ctl-widgets.css` comment above `.ctl-ir-tag`.)
- [x] Related Resources - Can the whole cards be clickable/have a hover? (checked 2026-08-07 —
  previously only the title text was a link, with no lift/highlight on hover. FIXED
  2026-08-07 in `ctl-site_instRes-detail`: title link now carries Bootstrap's
  `stretched-link` class, and the card gets `.ctl-hover-lift` alongside its existing
  `.ctl-ir-related-card` class; added `position: relative` to `.ctl-ir-related-card` in
  `styles/ctl-widgets.css` so the stretched-link anchors to the card itself, same pattern
  as `.ctl-res-card`.)
- [x] Headings are clickable/linked for each event card. These should visually show that they
  are clickable. (checked 2026-08-07 — duplicate of the item above; same finding: linked but
  not visually indicated as clickable on the listing widgets. FIXED 2026-08-07 — see the
  item above for details, same fix covers both.)
- [ ] Recommend clarifing language next to "Amount" -- maybe "Amount earned" (checked 2026-08-07
  — still just labeled "Amount:" in `ctl-widget_active-opportunities.html`)
- [x] Note: When the test content was present, the bottom 4 opportunities had passed but were
  still showing above the Past Opportunities (checked 2026-08-07: Active/Missed Opportunities
  are now two separately-filtered widgets, each comparing real epoch time via
  `$opp.signupDeadline.getTime()` vs. `$nowMs` — `>= $nowMs` for active,
  `< $nowMs` for missed — not a shared sort, so this can't recur)
- [x] Card headings should be H2 (checked 2026-08-07 — every listing widget's card title is
  actually H3 nested under an H2 section heading (e.g. "Upcoming Events"), which is the
  correct heading hierarchy — none use a literal H2 card title. Flagging in case "H2" was
  meant literally rather than "make it a real heading, not a styled div." — resolved
  2026-08-07: confirmed the intent was H3, not literal H2. Re-verified card headings on the
  instructional resource listing (`ctl-site_instRes-parent.html:141`) and the Resources
  overview tiles (`content blocks/ctl-content_resources-overview.html:33,45`, incl. "For
  Technologies") are already real `<h3>` elements, so no code change was needed.)
- [x] May want to clarify language for "Modality" (ie. the resource is about teaching in that
  format, NOT meaning that the resource is delivered in that format) (checked 2026-08-07 — the
  filter label was renamed "Course Modality" (see the separate checked item below), but no
  further explanatory text/tooltip was added beyond that label change)
- [x] H1 should not appear twice on the page  Ex: <https://suny-auth-1.dotcms.cloud/centers/ctl/event-calendar/professional-development-day-2026>
  (checked 2026-08-07 — same fix as the H1 item above: `ctl-site_activity-detail.html` has no
  `<h1>` of its own, and `ctl-site_instRes-detail` actively downgrades any `<h1>` found inside
  WYSIWYG body/citations content to `<h3>`)
- [x] Add mailto: links for emails (checked 2026-08-07 — the only two places a personnel
  email displays, `ctl-widget_ctl-staff-profiles.html` and `ctl-widget_fdc-member-grid.html`,
  both wrap `$!p.emailCortlandEdu` in `<a href="mailto:...">`; no other bare email
  addresses found in any widget)

* \

- [ ] Recommend clarifying language near dollar to indicate whether "paid" means that attendees
  are paid to attend or if there is a cost to attend (checked 2026-08-07 — still just shows the
  raw `$` amount with no clarifying label, in both `ctl-widget_active-opportunities.html` and
  `ctl-site_activity-detail.html`)

* \

- [x] Colors used for events should be within our brand identity (checked 2026-08-07 — the
  registration/application status badges on `ctl-site_activity-detail.html` used generic
  Bootstrap-alert colors (`#cfe2ff`/`#084298` blue, `#fff3cd`/`#856404` yellow), not the CTL
  maroon palette — same underlying issue as the "Side scroll cards" brand-color bullet above.
  FIXED 2026-08-07: `ctl-site_activity-detail.html`'s `$badgeBg`/`$badgeColor` now set
  `var(--ctl-maroon-tint)`/`var(--ctl-maroon)` for "Registration Required" and solid
  `var(--ctl-maroon)`/`#fff` for "Application Required — Selective" (filled solid to read as
  the more selective of the two, same outline-vs-filled logic as `.ctl-btn`/`.ctl-btn:hover`).
  Removed the now-unused `--ctl-warn-*` tokens from `styles/ctl-widgets.css` and updated its
  design-tokens comment + the brand style guide's "Notes for future CTL site application"
  section to match. The "Side scroll cards" carousel type-badge colors (`text-bg-success`/
  `text-bg-dark`/`text-bg-primary`) were a separate instance of the same issue, not touched
  by this fix at the time — since resolved the same day, see the "Side scroll cards" item
  above.)


- [x] no local stored video, yuja preferred, youtube okay; add quality control in form for video
- [x] attachment quality control cehck for accessibility
- [ ] add host field to content type to protect from other office/dept visibility (checked
  2026-08-07 — confirmed via the CtlActivity field table in the Variable Map: no `host` field
  exists on CtlActivity)
- [x] page tmplate -- change to right side nav, move non-nav items to a webpagecontent element; use RWD 1 main | subnav built-in template
- [x] split up sections into mutliple widgets for potential reuse
- [x] rephrase opportunities to “funded oportunities” (superseded 2026-08-04 same day: further restructured into "Funding" > "Current Opportunities" / "Past Recipients" — see IA section below; live page rename still pending)
- [x] move authors & superfluous tags to bottom of articles
- [x] detail page titles should be heading 2
- [x] add hidden screen reader text for all buttons
- [ ] friendly-names should stand alone -- rename friendly names for each page (checked
  2026-08-07 — no evidence of this in code or docs; still tied to the pending live dotCMS page
  rename/restructure work under "Next Up" in CLAUDE.md)
- [x] modality filter -→ “course modality”
- [x] past events should be less cluttered
- [ ] spell out FDC acronym (checked 2026-08-07 — the widget heading already reads "Faculty
  Development Committee" in full, but the empty-state message right below it
  (`ctl-widget_fdc-member-grid.html`) still says "FDC member bios are coming soon" without
  expanding it there; `content blocks/ctl-content_fdc-description.html`, presumably meant to
  hold fuller framing copy, is currently an empty file — renamed 2026-08-07 from
  `widgets/ctl-widget_fdc-description.html` when static intro/overview copy was split out of
  `widgets/` into its own `content blocks/` folder, see the note below row 17)
- [x] “About the CTL” becomes a folder containing subpages (finalized 2026-08-04, revised
  same day — see IA section below: Meet the CTL Staff, Faculty Development Committee,
  Impact & Reports; "Our Mission & Outcomes" dropped as a separate subpage in favor of
  mission/vision copy on the About the CTL landing page itself; "Other Affiliates" grouping
  dropped entirely; live pages/folder still pending)
  - [ ] FDC (checked 2026-08-07 — widget built in code, `widgets/ctl-widget_fdc-member-grid.html`,
    but not yet live-migrated to the Faculty Development Committee subpage, same "code done,
    live pending" state as the parent item)
  - [ ] Records (checked 2026-08-07 — unclear if this literally means "Records," or was
    superseded by "Impact & Reports" (6.3, new in the same IA revision) — that subpage's three
    pieces (`content blocks/ctl-content_impact-reports-intro.html`,
    `content blocks/ctl-content_outcomes-overview.html`, `widgets/ctl-widget_reports-grid.html`)
    are built in code but not live; leaving unchecked rather than assuming the mapping)
- [x] IR resources -- captions are same format as body content -- smaller? (fixed
  2026-08-06: `ctl-site_instRes-detail`'s body wrapper now carries a `ctl-ir-body` class;
  `styles/ctl-widgets.css` targets `figure figcaption` inside it — smaller, italic, muted
  gray, centered, distinct from body copy. Confirmed via live view-source the same day that
  the WYSIWYG editor's caption feature always emits `<figure class="image"><img>... <figcaption>...</figcaption></figure>`, so no fallback selectors were needed)
- [x] rename how can we help -→ “Request support” (naming finalized 2026-08-04 as "Request Support", replacing Get Help/Make an Appointment/Our Services — see IA table below; live page rename still pending)
- [x] create distinct styles page to override in-line styles (done 2026-08-05, confirmed
  live 2026-08-06: class-based `<style>` blocks consolidated into `styles/ctl-widgets.css`,
  loaded sitewide via a `<link>` tag added directly into the existing shared contact-info
  content block that already renders on every CTL page (no per-page edits needed, since the
  CTL page template is no longer in use — see CLAUDE.md). Still open: several widgets never
  used a shared class for parts of their layout (raw inline `style="..."` instead), so those
  spots aren't touched by this yet — see CLAUDE.md's flagged mismatches (Bootstrap default
  buttons on the 4 listing widgets vs. `.ctl-btn` elsewhere, the instRes-detail page's
  chip/tag styling vs. the listing/homepage version, the "Expired" badge).)

## Site IA rename (2026-08-04, two rounds same day)

Full current hierarchy (see the CLAUDE.md "Site Information Architecture" section for the
complete table with levels and intended URL paths):



1. Welcome to the CTL (was: Home)
2. Request Support (was: Get Help / Our Services + Make an Appointment)
3. Events (was: Event Calendar) → 3.1 Activity Detail Page
4. Funding (was: Opportunities, then flat "Funded Opportunities" — restructured a second
   time same day into the two-level branch below)
   * 4.1 Current Opportunities → 4.1.1 Activity Detail Page
   * 4.2 Past Recipients (new)
5. Resources (folder)
   * 5.1 For Course Design (was: Instructional Resources) → 5.1.1 Instructional Resource Detail Page
   * 5.2 For Technologies (was: Tech Support, not yet built)
6. About the CTL (was: About Us; now carries mission/vision copy directly on the L1 page —
   "Our Mission & Outcomes" as a separate subpage was dropped in the second revision round)
   * 6.1 Meet the CTL Staff (was: CTL Staff)
   * 6.2 Faculty Development Committee
   * 6.3 Impact & Reports (new)

Decisions made 2026-08-04 (all user-confirmed, not guesses):

* **Other Affiliates is dropped entirely** — no longer has a home anywhere in the new IA. The old combined FDC/Other Affiliates grid widget is now FDC-only (see row 6 below); its "Other" affiliation branch was removed from the code, not just hidden.
* **Request Support keeps its own duplicate "Make an Appointment" staff-cards widget instance** (row 5 below), even though About the CTL > Meet the CTL Staff is now a dedicated subpage — this preserves the existing reuse pattern rather than linking out.
* **"Our Mission & Outcomes" was dropped as a standalone subpage** in the second revision round — its old About Us intro/welcome text is now intended for the About the CTL L1 landing page itself instead (no dedicated widget needed; row 12 below is retired).
* **"Funded Opportunities" split into "Funding" (L1) > "Current Opportunities" (L2) + "Past Recipients" (L2)** — Current Opportunities is exactly the former Active Opportunities widget (row 10, relocated). Past Recipients combines the former Missed/Expired Opportunities widget (row 11, relocated) with a new, not-yet-built past-award-recipients widget (row 13) — a genuinely different concept (expired calls vs. actual winners), not just a rename.
* **A new "Impact & Reports" subpage was added under About the CTL** (row 14, not yet built) — annual reports, track record, accomplishments, institutional data.

## New widgets to create in dotCMS (2026-08-04 split, page names updated 2026-08-04 for the IA rename above)

Naming convention:

* Content-type widget name: `CTL widget_{content description}` — describes what's inside the widget, lowercase, no page reference (this is the reusable dotCMS Widget content type/Code asset)
* Instance name: `CTL widget_{content description}_{page}` — same content-type name plus the page the instance is placed on (one content-type can have multiple instances/content records, e.g. the CTL staff profiles widget is reused on both About the CTL > CTL Staff and Request Support)

**Content blocks (added 2026-08-07):** static intro/overview/blurb copy — no `$dotcontent.pull()`,
no listing/detail logic — lives in `content blocks/`, not `widgets/`, since in dotCMS these are
authored as "Web Page Content" blocks rather than Widget content type/Code assets. Same naming
shape as above but with `content` swapped in for `widget`:

* File: `ctl-content_{description}_{page}.html` (page suffix omitted when the file name is
  already unambiguous, e.g. a page-unique piece)
* Content-type name: `CTL content_{content description}`
* Instance name: `CTL content_{content description}_{page}`

Rows 14a, 14b, 15, 16, 17 below moved from `widgets/` to `content blocks/` under this convention
on 2026-08-07 (file paths updated in place rather than duplicating rows) — see CLAUDE.md and
the Variable Map's new Content Blocks Index for the current file locations.

| # | File | Content-type widget name | Instance name | Page | Description |
|----|----|----|----|----|----|
| 1 | `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html` | CTL widget_home nav tiles | CTL widget_home nav tiles_welcome to the ctl | Welcome to the CTL | Welcome text plus a 5-tile navigation grid (was 6 — see file header) linking to the other top-level CTL site sections |
| 2 | `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html` | CTL widget_upcoming activities carousel | CTL widget_upcoming activities carousel_welcome to the ctl | Welcome to the CTL | Horizontal-scroll carousel of upcoming events and opportunities pulled from CTL Activity, sorted by nearest relevant date, linking to each activity's detail page |
| 3 | `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html` | CTL widget_featured instructional resources | CTL widget_featured instructional resources_welcome to the ctl | Welcome to the CTL | Horizontal-scroll carousel of featured, QC-approved items pulled from CTL Instructional Resource |
| 4 | `widgets/ctl-widget_ctl-staff-profiles.html` | CTL widget_ctl staff profiles | CTL widget_ctl staff profiles_meet the ctl staff | About the CTL > Meet the CTL Staff | Full single-column bio cards (photo, expertise/help bullets, actions) for CTL-affiliated staff pulled from CTL Personnel, single-column list, heading "Meet the CTL Staff" |
| 5 | `widgets/ctl-widget_ctl-staff-profiles.html` (2nd instance, same code) | CTL widget_ctl staff profiles | CTL widget_ctl staff profiles_request support | Request Support | Same bio card layout/content as #4, reused under heading "Make an Appointment"; accepted tradeoff is the "Visit My Website" button also shows here. Kept as a duplicate instance rather than linking out to the Meet the CTL Staff subpage — user decision 2026-08-04 |
| 6 | `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` | CTL widget_fdc member grid | CTL widget_fdc member grid_faculty development committee | About the CTL > Faculty Development Committee | Compact multi-column grid cards for FDC Members only, pulled from CTL Personnel, sorted by rank. Renamed and narrowed from the old "affiliate personnel grid" (FDC + Other Affiliates) — Other Affiliates was dropped from the IA entirely, code updated to match |
| 7 | `widgets/ctl-widget_our-services.html` | CTL widget_our services | CTL widget_our services_request support | Request Support | List of services pulled from CTL Service: 2-line-clamped expandable description, then a "Next Steps" section (access button and/or freeform note text, e.g. drop-in hours or a jump-link phrase) gated on availability. Reworked 2026-08-06, replacing the old availability badge + top-right button — see CLAUDE.md |
| 8 | `widgets/ctl-widget_upcoming-events_events.html` | CTL widget_upcoming events | CTL widget_upcoming events_events | Events | Single-column list of upcoming events pulled from CTL Activity (isEvent), with thumbnail, date, time, location, description, and Register/Details button |
| 9 | `widgets/ctl-widget_past-events_events.html` | CTL widget_past events | CTL widget_past events_events | Events | 2-column grid (1-column under 768px) of past events pulled from CTL Activity (isEvent), pared-down tiles: name, short description, date, and Details button only |
| 10 | `widgets/ctl-widget_active-opportunities_current-opportunities.html` | CTL widget_active opportunities | CTL widget_active opportunities_current opportunities | Funding > Current Opportunities | Single-column list of active/open opportunities pulled from CTL Activity (isOpportunity), with thumbnail, categories, deadline, amount, description, and Apply/Details button. Relocated 2026-08-04 from the flat "Funded Opportunities" page, unchanged otherwise |
| 11 | `widgets/ctl-widget_missed-opportunities_past-recipients.html` | CTL widget_missed opportunities | CTL widget_missed opportunities_past recipients | Funding > Past Recipients | Collapsible 2-column grid (1-column under 768px) of expired opportunities ("Missed Connections") pulled from CTL Activity (isOpportunity), amount hidden, description clamped to 2 lines. Relocated 2026-08-04 from the flat "Funded Opportunities" page, unchanged otherwise — now sits alongside row 13 below |
| 12 | not applicable — retired | — | — | — | Formerly "Our Mission & Outcomes" subpage widget; dropped in the second IA revision round (2026-08-04) in favor of mission/vision copy directly on the About the CTL L1 landing page. No widget needed |
| 13 | `widgets/ctl-widget_past-award-recipients_past-recipients.html` | CTL widget_past award recipients | CTL widget_past award recipients_past recipients | Funding > Past Recipients | Built in code 2026-08-06: list of actual past grant/fellowship/award winners — a distinct concept from row 11's expired-call listing. Proposes a new content type, CtlPastRecipient, not yet created in dotCMS — see this file's header for the full field list before creating it live |
| 14a | `content blocks/ctl-content_impact-reports-intro.html` (was `widgets/ctl-widget_impact-reports-intro.html`, renamed 2026-08-07) | CTL content_impact and reports intro | CTL content_impact and reports intro_impact & reports | About the CTL > Impact & Reports | Built in code 2026-08-06: opening/framing blurb, hardcoded static copy (same convention as the homepage welcome text) |
| 14b | `content blocks/ctl-content_outcomes-overview.html` (was `widgets/ctl-widget_outcomes-overview.html`, renamed 2026-08-07) | CTL content_outcomes overview | CTL content_outcomes overview_impact & reports | About the CTL > Impact & Reports | Built in code 2026-08-06: list-of-outcomes stat tiles, hardcoded PLACEHOLDER numbers — real figures still need to be filled in before going live |
| 14c | `widgets/ctl-widget_reports-grid.html` | CTL widget_reports grid | CTL widget_reports grid_impact & reports | About the CTL > Impact & Reports | Built in code 2026-08-06: downloadable annual reports grid. Proposes a new content type, CtlReport, not yet created in dotCMS — see this file's header for the full field list before creating it live. Stays a widget (not a content block) since it pulls live CtlReport data |
| 15 | `content blocks/ctl-content_funding-overview_funding.html` (was `widgets/ctl-widget_funding-overview_funding.html`, renamed 2026-08-07) | CTL content_funding overview | CTL content_funding overview_funding | Funding | Built in code 2026-08-06: L1 landing/overview content for the Funding page (financial support, grants, awards), linking down to Current Opportunities and Past Recipients |
| 16 | `content blocks/ctl-content_resources-overview.html` (was `widgets/ctl-widget_resources-overview.html`, renamed 2026-08-07) | CTL content_resources overview | CTL content_resources overview_resources | Resources | Built in code 2026-08-06: L1 landing/overview content for the Resources page, linking down to For Course Design and For Technologies |
| 17 | `content blocks/ctl-content_events-overview_events.html` (was `widgets/ctl-widget_events-overview_events.html`, renamed 2026-08-07) | CTL content_events overview | CTL content_events overview_events | Events | Built in code 2026-08-06: top-of-page intro blurb, sits above the existing Upcoming/Past Events widgets on the same page |
| 18 | `content blocks/ctl-content_fdc-description.html` (was `widgets/ctl-widget_fdc-description.html`, renamed 2026-08-07) | CTL content_fdc description | CTL content_fdc description_faculty development committee | About the CTL > Faculty Development Committee | Not yet built — currently an empty file. Presumably framing/intro copy for the FDC subpage, per the "spell out FDC acronym" checklist item above |
| 19 | `content blocks/ctl-content_services-overview.html` (was `widgets/ctl-widget_services-overview.html`, renamed 2026-08-07) | CTL content_services overview | CTL content_services overview_request support | Request Support | Not yet built — currently an empty file. Presumably a top-of-page intro blurb for Request Support, same pattern as row 17's Events overview |

Note: rows 4 and 5 share one content-type widget (`CTL widget_ctl staff profiles`) with two separate content instances/records — this requires adding a `sectionHeading` Text field to that widget's content type in dotCMS if not already present (see the CtlPersonnel section in CLAUDE.md).


