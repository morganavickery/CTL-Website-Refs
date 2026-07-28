# CtlActivity Events — Import Notes

10 events, all `isEvent = true` / `isOpportunity = false`. 4 are in the past relative to today
(Feb–June 2026), 6 are upcoming (Aug 2026–Jan 2027), including one multi-day event where
`eventStartDate` ≠ `eventEndDate`.

## Assumptions I had to make — check these before importing

I don't have your actual category tree or field option lists for this content type, so I guessed
at values that follow the same conventions as the IR content type. If any of these don't match
what's actually configured, the import will most likely just leave that item's field empty/unset
rather than error — worth a quick scan of one imported item afterward to confirm.

- **`categories`** — used one key per row: `orientation`, `workshop`, `guest-speaker-series`,
  `drop-in-support`, `conference-symposium`, `book-club`, `committee-meeting`,
  `certificate-program`. These are guesses at your taxonomy, not pulled from anywhere real.
- **`isEvent` / `isOpportunity`** — used the literal strings `"true"` / `"false"`, matching the
  `CheckboxMap.selectedValues.contains("true")` pattern from `CtlActivity`'s known field behavior.
- **`signupType`** — used three made-up option labels: `External Registration Link`,
  `Internal Sign-Up`, `No Signup Required`. If this is a RadioMap field, its real option values
  are very likely different strings — swap these for whatever the actual radio options are.
- **`urlMapPattern`** — populated with just the slug (e.g. `spring-open-house-2026`), matching
  how the instructional-resource detail widget consumes it: `/centers/ctl/activities/$!rel.urlMapPattern`.
- **`thumbnail` and `attachments`** on 5 rows use `REPLACE_WITH_ASSET_PATH/...` placeholders —
  same as last time, these need real asset paths/identifiers from your library before import or
  they'll just come in blank.

## Field format notes (pulled directly from your template's example row, not guessed)

- `signupDeadline` is date-only (`MM/dd/yyyy`) — no time component.
- `submissionDeadline` is date **and** time (`MM/dd/yyyy hh:mm aa`) — left blank on every row here
  since none of these are opportunities, but flagging the format difference since it's easy to
  mix the two up.
- `eventStartTime` / `eventEndTime` are time-only (`hh:mm aa`), separate from the date fields.
