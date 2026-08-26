# Consolidated snag results (Manual + AI)

Customer-facing snag detection evaluation reports — Manual vs CONXAI — for
units Q844, Q845 and Q545, served via GitHub Pages.

Entry point: `index.html`. Each report is a self-contained page; evidence
photos live under `assets/<unit>/` (`img_*` thumbnails, `full_*` lightbox
photos, extracted from the original single-file reports to stay under GitHub's
100 MB per-file limit — rendering is unchanged).

Source of truth: Google Drive folder `snag results` (1Mn6qKyQ6Va1IOL-zo-7Pkig6UIQl4kh4).

## Expert review persistence

Each report's per-snag verdicts and comments save to the reviewer's browser
(localStorage, as before) **and** sync to PostHog (EU project 249682,
"Default project") as `snag_review` events via the public client token —
write-only by design, so embedding it here leaks nothing. No sign-in needed;
reviewers should fill the "Reviewer name" field so events are attributable.

Latest state per snag (HogQL, in PostHog or via API):

```sql
SELECT properties.unit AS unit, properties.review_id AS review_id,
       argMax(properties.review, timestamp)   AS review,
       argMax(properties.comment, timestamp)  AS comment,
       argMax(properties.reviewer, timestamp) AS reviewer,
       max(timestamp) AS updated_at
FROM events
WHERE event = 'snag_review' AND timestamp >= now() - INTERVAL 90 DAY
GROUP BY unit, review_id
```

For push-based subscription, add a PostHog realtime destination (webhook) on
`event = snag_review`. The full event log is the audit trail; the query above
collapses it to current state.

## Layout notes

Reports render as the full table down to 1361 px viewports and switch to the
stacked card layout below that (raised from 1080 px so ~1280 px laptops are not
stuck with a clipped table). Rows use `content-visibility:auto` so 400+ rows
with photos scroll smoothly.
