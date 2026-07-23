# Instructional Resource Detail Widget — Stress Test Notes

17 new items in `IR_sample_import_stress-test.csv`, additive to your existing 15 (none of the
`urlTitle` slugs collide). Each targets a specific thing the widget does — or doesn't — handle.

## Before you import

**`thumbnail` and `videoFile`** on 3 rows use a placeholder value:
`REPLACE_WITH_ASSET_PATH/...`. Since binaries import by referencing an existing asset's
path/identifier, swap these for two or three real assets already in your file library (one image,
one small video) before running the import — otherwise those rows will just import with those
fields empty. Rows affected: `stress-full-multimedia-kitchen-sink` (both fields, plus an embedUrl
that should lose to the local video), `stress-local-video-only`, `stress-max-config-modest-length`.

**`relatedEvents` and `relatedTechSupport`** are left blank on every row. `relatedEvents` would
need real `CtlActivity` identifiers from your instance to test meaningfully — worth hand-linking
1–2 existing events to one row after import if you want to exercise that block. `relatedTechSupport`
can't be populated at all yet since `CtlTechResource` doesn't exist as a content type — see the gap
below.

## ⚠️ Gap found while reading the widget: attachments aren't handled at all

I read through the whole widget and there's no loop, field reference, or relationship pull for
`CtlAttachment` anywhere in it — not in the media block, not near citations, nowhere. So there's
no test data to add for it in this round, because there's nothing on the page that would render it
yet. Worth deciding: is attachment display supposed to live in this widget (e.g. a "Downloads"
section near citations) and just hasn't been built, or does `CtlInstructionalResource` not actually
relate to `CtlAttachment` at all currently? Once that's settled I can build a proper attachment
stress set (multiple files, mixed types, missing thumbnails on the `BinaryMap` for non-image files).

## What each row is for

| urlTitle | Targets |
|---|---|
| `stress-full-multimedia-kitchen-sink` | Local video **and** embedUrl both set — confirms video wins per the `#if/#elseif`. Also maxes out topics (20), frameworks (8), a heavy multi-element body (nested list, table, blockquote, inline image, an iframe embed inside the body itself, an H1 inside body content to check for heading collision with the page's own H1), and 8 related resources including a self-reference. |
| `stress-local-video-only` | `videoFile` set, `embedUrl` blank — baseline for the local-video branch alone. |
| `stress-embed-youtube-only` | `embedUrl` only, YouTube. Baseline for the "Watch Video" button branch. |
| `stress-embed-vimeo-only` | Same branch, non-YouTube host — checks nothing assumes a YouTube URL shape. |
| `stress-embed-malformed` | `embedUrl` with no `https://` scheme and pointing at a non-video page — the widget just wraps whatever string is there in an `<a href>`, so this checks that a bad or non-video URL doesn't break rendering, even if the resulting link is nonsensical. |
| `stress-bare-minimum` | Every optional field empty. Walks every `#if("$!item.X" != "")` guard down the empty path at once. |
| `stress-xss-injection-test` | `<script>`, `onerror`, `onclick`, and `<svg onload>` payloads in title, shortDescription, and body. `$item.resourceTitle` and `$item.body` are both output unescaped (`$item.resourceTitle` even gets reused raw inside the `<script>` block at the bottom that sets `document.title` and rewrites breadcrumbs) — worth specifically checking whether the title injection breaks that script block, not just whether the body injection fires. |
| `stress-extremely-long-title` | ~240-character title — H1 and the JS-rewritten breadcrumb/page-title-banner text wrapping. |
| `stress-unicode-emoji-rtl` | Accented Latin, emoji, RTL Arabic (`dir="rtl"`), and CJK text in title, shortDescription, author, and body — encoding and bidi rendering. |
| `stress-topic-framework-overload` | 20 topics across categories + 8 frameworks, isolated from every other stress factor, so pill-cloud and chip-row wrapping can be judged on their own. |
| `stress-multi-formattype` | `formatType` given 3 categories instead of 1. If `formatType` is meant to be single-select, this checks what the tag row does with more than expected — and separately, whether the *listing* widget's radio-button filter (single-select by definition) handles an item that has multiple format categories underneath it. |
| `stress-missing-reviewedby` | `reviewedBy` blank. Per your existing QC gate this should be excluded from the *listing*, but should still resolve fine at its direct detail URL — confirms the detail widget itself doesn't independently gate on `reviewedBy`. Load it by typing/pasting the URL, not by clicking through the listing. |
| `stress-self-referencing-related` | `relatedInstructionalResources` includes its own `urlTitle` — checks for a related-resource card that links back to the page you're already on. |
| `stress-many-related-grid-overflow` | 8 related resources on the 2-column grid — well past one row, for spacing/alignment/any implicit cap. |
| `stress-related-points-to-gated-item` | Its one related resource is `stress-missing-reviewedby` — a target that fails the listing's QC gate. Checks whether a related-resource card still renders normally even though its target wouldn't show up in the main listing. |
| `stress-citations-malformed-html` | 8 citations with a deliberately unclosed `<li>` partway through — how forgiving the raw HTML dump in the citations block is of broken markup. |
| `stress-max-config-modest-length` | Every field populated but kept short — a control row to screenshot-diff against the bare-minimum and kitchen-sink extremes. |

## A couple of things worth eyeballing while you're in there

- The byline (`By {author} · Updated {date}`) — `stress-bare-minimum` has neither, so confirm the
  `·` separator doesn't print orphaned when both sides are empty.
- `stress-embed-malformed`'s embedUrl has no scheme (`www.cortland.edu/...`) — an `<a href="www.cortland.edu/...">`
  without `https://` will resolve as a relative link off your current page rather than an external
  one. Good real-world case to have caught in test data rather than in production content.
