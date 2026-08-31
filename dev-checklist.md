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
- [ ] Build Funding (L1) landing page content
- [ ] Build the Past Recipients award-winners content — needs the CtlPastRecipient content type created in dotCMS first
- [ ] Build the Impact & Reports subpage content — needs the CtlReport content type created in dotCMS first
- [ ] Write mission/vision overview copy for the About the CTL landing page
- [ ] Migrate Welcome to the CTL (homepage) to its 3 split widgets in live dotCMS
- [ ] Migrate About the CTL > Meet the CTL Staff / Faculty Development Committee to their split widgets in live dotCMS
- [ ] Migrate Events listing to its 3 split widgets in live dotCMS (add `ctl-widget_happening-now-events.html` above Upcoming Events)
- [ ] Migrate Request Support to its split widgets in live dotCMS — needs a `sectionHeading` Text field added to the staff-profiles widget's content type first
- [ ] Migrate Funding (+ Current Opportunities / Past Recipients) to its split widgets in live dotCMS
- [ ] Update CtlInstructionalResource's URL Map Pattern to `/centers/ctl/resources/instructional-design-resources/{urlTitle}`
- [ ] Verify `ctlAttachmentfile` downloads/thumbnails actually work live
- [ ] Build the CtlTechSupport content type + For Technologies page
- [ ] Delete orphaned widgets: `ctl_widget_detail-router`, `CTL_widget_event-calendar_detail`, `CTL_widget_opportunities_detail`
- [ ] Fix CtlAttachment thumbnail letterboxing — need a crop-based URL pattern instead of `.thumbnailUri`
- [ ] Clean up leftover `$EDIT_MODE` debug scaffolding in the `DEPRECATED/` stub files
- [ ] Fill in real numbers for the Outcomes stat tiles (currently placeholders)
- [ ] Resolve style mismatches in `styles/ctl-widgets.css`: Bootstrap default buttons on the 4 listing widgets vs. `.ctl-btn` elsewhere; `ctl-site_instRes-detail`'s chip/tag styling vs. the listing/homepage version; Missed Opportunities' inline "Expired" badge/tags vs. the shared `.ctl-badge-closed`/`.ctl-tag` classes
- [ ] Swap the 4 listing widgets' inline "listing card" styles over to the shared `.ctl-listing-card` class

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
- [x] Renamed "Opportunities" → "Funding" (Current Opportunities / Past Recipients)
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


