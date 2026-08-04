- [ ] no local stored video, yuja preferred, youtube okay; add quality control in form for video
- [ ] attachment quality control cehck for accessibility
- [ ] add host field to content type to protect from other office/dept visibility
- [ ] page tmplate -- change to right side nav, move non-nav items to a webpagecontent element; use RWD 1 main | subnav built-in template
- [x] split up sections into mutliple widgets for potential reuse
- [x] rephrase opportunities to “funded oportunities” (superseded 2026-08-04 same day: further restructured into "Funding" > "Current Opportunities" / "Past Recipients" — see IA section below; live page rename still pending)
- [ ] move authors & superfluous tags to bottom of articles
- [ ] detail page titles should be heading 2
- [ ] add hidden screen reader text for all buttons
- [ ] friendly-names should stand alone -- rename friendly names for each page
- [ ] modality filter -→ “course modality”
- [x] past events should be less cluttered
- [ ] spell out FDC acronym
- [x] “About the CTL” becomes a folder containing subpages (finalized 2026-08-04, revised
  same day — see IA section below: Meet the CTL Staff, Faculty Development Committee,
  Impact & Reports; "Our Mission & Outcomes" dropped as a separate subpage in favor of
  mission/vision copy on the About the CTL landing page itself; "Other Affiliates" grouping
  dropped entirely; live pages/folder still pending)
  - [ ] FDC
  - [ ] Records
- [ ] IR resources -- captions are same format as body content -- smaller?
- [x] rename how can we help -→ “Request support” (naming finalized 2026-08-04 as "Request Support", replacing Get Help/Make an Appointment/Our Services — see IA table below; live page rename still pending)
- [ ] create distinct styles page to override in-line styles
- [ ] multiple columns for past events / opportunities (events done 2026-08-04, see widgets/ctl-widget_past-events_events.html; opportunities' 2-column grid is done in widgets/ctl-widget_missed-opportunities_past-recipients.html but the page it's meant to relocate to, "Past Recipients", isn't live yet)
- [ ] \

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

| # | File | Content-type widget name | Instance name | Page | Description |
|----|----|----|----|----|----|
| 1 | `widgets/ctl-widget_home-nav-tiles_welcome-to-the-ctl.html` | CTL widget_home nav tiles | CTL widget_home nav tiles_welcome to the ctl | Welcome to the CTL | Welcome text plus a 5-tile navigation grid (was 6 — see file header) linking to the other top-level CTL site sections |
| 2 | `widgets/ctl-widget_upcoming-activities-carousel_welcome-to-the-ctl.html` | CTL widget_upcoming activities carousel | CTL widget_upcoming activities carousel_welcome to the ctl | Welcome to the CTL | Horizontal-scroll carousel of upcoming events and opportunities pulled from CTL Activity, sorted by nearest relevant date, linking to each activity's detail page |
| 3 | `widgets/ctl-widget_featured-instructional-resources_welcome-to-the-ctl.html` | CTL widget_featured instructional resources | CTL widget_featured instructional resources_welcome to the ctl | Welcome to the CTL | Horizontal-scroll carousel of featured, QC-approved items pulled from CTL Instructional Resource |
| 4 | `widgets/ctl-widget_ctl-staff-profiles.html` | CTL widget_ctl staff profiles | CTL widget_ctl staff profiles_meet the ctl staff | About the CTL > Meet the CTL Staff | Full single-column bio cards (photo, expertise/help bullets, actions) for CTL-affiliated staff pulled from CTL Personnel, single-column list, heading "Meet the CTL Staff" |
| 5 | `widgets/ctl-widget_ctl-staff-profiles.html` (2nd instance, same code) | CTL widget_ctl staff profiles | CTL widget_ctl staff profiles_request support | Request Support | Same bio card layout/content as #4, reused under heading "Make an Appointment"; accepted tradeoff is the "Visit My Website" button also shows here. Kept as a duplicate instance rather than linking out to the Meet the CTL Staff subpage — user decision 2026-08-04 |
| 6 | `widgets/ctl-widget_fdc-member-grid_faculty-development-committee.html` | CTL widget_fdc member grid | CTL widget_fdc member grid_faculty development committee | About the CTL > Faculty Development Committee | Compact multi-column grid cards for FDC Members only, pulled from CTL Personnel, sorted by rank. Renamed and narrowed from the old "affiliate personnel grid" (FDC + Other Affiliates) — Other Affiliates was dropped from the IA entirely, code updated to match |
| 7 | `widgets/ctl-widget_our-services_request-support.html` | CTL widget_our services | CTL widget_our services_request support | Request Support | Grid of services pulled from CTL Service, with availability badge, 2-line-clamped expandable description, and "Access this Service" button |
| 8 | `widgets/ctl-widget_upcoming-events_events.html` | CTL widget_upcoming events | CTL widget_upcoming events_events | Events | Single-column list of upcoming events pulled from CTL Activity (isEvent), with thumbnail, date, time, location, description, and Register/Details button |
| 9 | `widgets/ctl-widget_past-events_events.html` | CTL widget_past events | CTL widget_past events_events | Events | 2-column grid (1-column under 768px) of past events pulled from CTL Activity (isEvent), pared-down tiles: name, short description, date, and Details button only |
| 10 | `widgets/ctl-widget_active-opportunities_current-opportunities.html` | CTL widget_active opportunities | CTL widget_active opportunities_current opportunities | Funding > Current Opportunities | Single-column list of active/open opportunities pulled from CTL Activity (isOpportunity), with thumbnail, categories, deadline, amount, description, and Apply/Details button. Relocated 2026-08-04 from the flat "Funded Opportunities" page, unchanged otherwise |
| 11 | `widgets/ctl-widget_missed-opportunities_past-recipients.html` | CTL widget_missed opportunities | CTL widget_missed opportunities_past recipients | Funding > Past Recipients | Collapsible 2-column grid (1-column under 768px) of expired opportunities ("Missed Connections") pulled from CTL Activity (isOpportunity), amount hidden, description clamped to 2 lines. Relocated 2026-08-04 from the flat "Funded Opportunities" page, unchanged otherwise — now sits alongside row 13 below |
| 12 | not applicable — retired | — | — | — | Formerly "Our Mission & Outcomes" subpage widget; dropped in the second IA revision round (2026-08-04) in favor of mission/vision copy directly on the About the CTL L1 landing page. No widget needed |
| 13 | not yet built | CTL widget_past award recipients | CTL widget_past award recipients_past recipients | Funding > Past Recipients | New (2026-08-04): list of actual past grant/fellowship/award winners — a distinct concept from row 11's expired-call listing. Needs data modeling (new content type or new CtlActivity fields) not yet scoped |
| 14 | not yet built | CTL widget_impact and reports | CTL widget_impact and reports_impact & reports | About the CTL > Impact & Reports | New (2026-08-04): annual reports, track record, accomplishments, institutional data. Not yet scoped |
| 15 | not yet built | CTL widget_funding overview | CTL widget_funding overview_funding | Funding | New (2026-08-04): L1 landing/overview content for the Funding page (financial support, grants, awards), linking down to Current Opportunities and Past Recipients. Not yet scoped |

Note: rows 4 and 5 share one content-type widget (`CTL widget_ctl staff profiles`) with two separate content instances/records — this requires adding a `sectionHeading` Text field to that widget's content type in dotCMS if not already present (see the CtlPersonnel section in CLAUDE.md).


