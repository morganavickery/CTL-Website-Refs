

# CTL Website — Dev Checklist

## Open

- [ ] breadcrumb errors -- home links to cortland home, not ctl home
- [ ] move activity tags to bottom of page
- [ ] Swap degree for
- [ ] Recommend clarifying "Amount" label — e.g. "Amount earned"
- [ ] Recommend clarifying language near "$" — distinguish paid-to-attend vs. cost-to-attend
- [ ] Add a `host` field to CtlActivity to restrict visibility to other offices/departments
- [ ] Standardize/finalize friendly page names across the site
- [ ] Spell out "FDC" acronym in FDC section copy/empty-state text; build `ctl-content_fdc-description.html`
- [ ] Recommend hiding past events eventually (list will grow unbounded over time, slows page load)
- [ ] Rename/restructure pages and folders in live dotCMS to match the current IA (see CLAUDE.md)
- [ ] Rename the Funding folder to Opportunities in live dotCMS; create Awards, Grants & Funding, and Programs & Communities as new subpages (Past Recipients and the Activity Detail URL Map already exist)
- [ ] Import `imports (populated)/CTL_Activity_import_teaching-innovation-past-years.csv` and `CTL_Personnel_import_8_14_2026.csv` (in that order — copy the new Activities' Identifiers into the Personnel CSV's `PENDING-TIG-*` cells first)
- [ ] Build the Impact & Reports subpage content — needs the CtlReport content type created in dotCMS first
- [ ] Write mission/vision overview copy for the About the CTL landing page
- [ ] Migrate Welcome to the CTL (homepage) to its 3 split content blocks in live dotCMS
- [ ] Migrate About the CTL > Meet the CTL Staff / Faculty Development Committee to their split content blocks in live dotCMS
- [ ] Migrate Events listing to its 3 split content blocks in live dotCMS (add `ctl-content_happening-now-events.html` above Upcoming Events)
- [ ] Migrate Request Support to its split content blocks in live dotCMS — needs a `sectionHeading` Text field added to the staff-profiles content block's content type first
- [ ] Migrate Opportunities (+ Awards / Grants & Funding / Programs & Communities / Past Recipients) to its split content blocks in live dotCMS
- [ ] Configure vanity URL forwarding for the four Opportunities listing prefixes into the shared Activity Detail page (dormant fallback logic in `ctl-content_activity-detail.html` takes over automatically once this is set up — see that file's header)
- [ ] Import `imports (populated)/CTL_Activity_import_past-award-recipients.csv` first, then copy the new Activities' Identifiers into `CTL_Personnel_import_8_14_2026.csv`'s `PENDING-AWD-*` cells before re-importing that file (already updated to the new CtlPersonnel schema/template — Gillian Mertens and Nancy J. Kane's existing rows already carry their new honor alongside their old one; confirm the import matches/updates by email rather than creating duplicates)
- [ ] Build a content block for the Associate tab (Faculty Associates) — `associateTitle`/`roleDescription`/`termYearStart`/`termYearEnd` fields exist on CtlPersonnel (`affiliations` contains `"Affiliate"`) but nothing renders them yet; Katie Laux and Carrie Rood are already imported and waiting
- [ ] Update CtlInstructionalResource's URL Map Pattern to `/centers/ctl/resources/instructional-design-resources/{urlTitle}`
- [ ] Verify `ctlAttachmentfile` downloads/thumbnails actually work live
- [ ] Finalize the full Tool and Task/Function category lists (see `dotCMS Variable Map.md`), then create the CtlTechSupport content type + its two category trees in dotCMS
- [ ] Create the For Technologies page in dotCMS at `/centers/ctl/resources/technologies` and add `ctl-content_tech-support-listing_technologies.html` as its content block
- [ ] Delete orphaned widgets: `ctl_widget_detail-router`, `CTL_widget_event-calendar_detail`, `CTL_widget_opportunities_detail`
- [ ] Fix CtlAttachment thumbnail letterboxing — need a crop-based URL pattern instead of `.thumbnailUri`
- [ ] Clean up leftover `$EDIT_MODE` debug scaffolding in the `DEPRECATED/` stub files
- [ ] Fill in real numbers for the Outcomes stat tiles (currently placeholders)
- [ ] Resolve style mismatches in `styles/ctl-widgets.css`: Bootstrap default buttons on the listing content blocks vs. `.ctl-btn` elsewhere; `ctl-content_instRes-detail`'s chip/tag styling vs. the listing/homepage version; the Opportunities tag pages' closed-items inline "Expired" badge/tags vs. the shared `.ctl-badge-closed`/`.ctl-tag` classes
- [ ] Swap the listing content blocks' inline "listing card" styles over to the shared `.ctl-listing-card` class
- [ ] Create the Request a GIFT page in dotCMS at `/centers/ctl/pages/request-a-gift`, add `ctl-content_request-a-gift.html` as its content, then on the Our Services "Request a GIFT" CtlService record: set `serviceAccessLink` to that URL and clear `longDescription` (so the tile links out via the Next Steps button instead of expanding inline)

## Completed

- [x] Side scroll cards: brand-consistent label colors; partial-peek + scroll buttons indicate scrollability
- [x] Listing card headings are visibly clickable (hover treatment + stretched-link)
- [x] H1 does not appear twice on any page
- [x] Topics/keywords grouped in one place below the article body
- [x] Topic/keyword chip styling no longer looks like clickable filters
- [x] Related Resources cards are fully clickable with hover
- [x] Expired opportunities no longer show above active ones (separately filtered widgets)
- [x] Card headings use correct heading hierarchy (H3 under H2 section heading)
- [x] "Modality" filter relabeled "Course Modality"
- [x] mailto: links added for all displayed personnel emails
- [x] Event/opportunity/registration badge colors match brand identity
- [x] Local video hosting disallowed — YuJa preferred, YouTube okay; added video QC field
- [x] Attachment QC check for accessibility
- [x] Page template changed to a right-side-nav layout; non-nav items moved to Web Page Content
- [x] Split single-page widgets into multiple reusable widgets
- [x] Renamed "Funding" → "Opportunities"; split into Awards / Grants & Funding / Programs & Communities (by category) plus Past Recipients
- [x] Moved author/tags to bottom of articles
- [x] Detail page titles are heading 2
- [x] Added hidden screen-reader text to all buttons
- [x] "About the CTL" restructured into a folder with subpages (Meet the CTL Staff, Faculty Development Committee, Impact & Reports)
- [x] Instructional resource image captions styled distinctly from body content
- [x] Renamed "Get Help" → "Request Support"
- [x] Created shared stylesheet (`styles/ctl-widgets.css`), loaded sitewide via the shared contact-info content block
- [x] Reworked Our Services cards: removed availability badge, added a "Next Steps" section
- [x] Converted Our Services to a tile grid; added per-tile Learn More expand/collapse
- [x] Split static intro/overview copy into a separate `content blocks/` folder
- [x] Made all `.ctl-hover-lift` tiles fully clickable (stretched-link)


