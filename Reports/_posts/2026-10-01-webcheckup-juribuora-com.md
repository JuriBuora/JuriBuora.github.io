---
layout: post
title: "WebCheckup Report: External Check-Up of juribuora.com, With Fixes and a Re-Test"
summary: "I ran my own website check-up service against my own site. It found nine things. Six were fixed and one partly fixed the same day, and re-checked. Two are still open, and the tool got two things wrong."
date: 2026-10-01
categories: reports
tags: [Cybersecurity, Reports, WebSecurity, SecurityHeaders, Accessibility, WebPerformance, EmailSecurity]
number: 8
---

## Summary in plain language

I looked at juribuora.com from the outside, the way a visitor, a browser or a search engine does. Nothing needed a password and nothing on the server was touched.

The site was already in good shape on the basics: it is served only over HTTPS with a valid certificate, it loads no trackers, and it scored 100 for search visibility. The check-up still found nine things worth acting on. None was urgent.

- **Six are fixed and one is partly fixed**, and I measured the site again afterwards to confirm it.
- **Two are still open.** Neither can be fixed in the site's code: they need a change at the hosting level.
- **The tool was wrong twice**, and it missed three problems I found by hand. All of that is written down below, because a report that hides the limits of its own tooling is not worth much.

| Measure | Before | After |
| --- | --- | --- |
| Accessibility score | 86 | 100 |
| Performance score (phone, slow 4G) | 96 | 99 |
| Main content visible (median of 3 runs) | 2.6 s | 2.0 s |
| Slowest of the 3 runs | 3.1 s | 2.1 s |
| Data downloaded for the home page | 429 kB | 237 kB |
| Best-practices score | 100 | 100 |
| Search-visibility score | 100 | 100 |

## Scope and method

**What was checked.** The public home page of `https://juribuora.com`, its response headers, its certificate, its DNS records for email, and how it loads and reads on a phone.

**How.**

- The WebCheckup engine, the same one I use for paying customers. It makes ordinary web requests, reads headers and certificates, and looks up public DNS records.
- Lighthouse 13.4.1, phone profile on a simulated slow 4G connection, three runs each time, reporting the median and the range.
- Every finding was then confirmed by hand with `curl` and `dig`, so nothing below rests on one tool's word.
- After the fixes went live, the whole check-up was run again against the live site.

**What was not done.** No login testing, no scanning for hidden pages, no attack attempts, no load testing. This is a check-up, not a penetration test, and it says nothing about anything behind a login because this site has none.

**Date.** 1 October 2026. Both the "before" and "after" measurements were taken that day.

## Priority table

| # | Finding | Priority | Status | Who can fix it |
| --- | --- | --- | --- | --- |
| 1 | No policy limiting what the page may load | Medium | Partly fixed | Site code (done), hosting (rest) |
| 2 | The site can be shown inside another site's frame | Medium | Open | Hosting |
| 3 | Home page slower than recommended on a phone | Medium | Fixed | Site code |
| 4 | Accessibility problems (4) | Medium | Fixed | Site code |
| 5 | Pictures missing in older posts | Medium | Fixed | Site code |
| 6 | Page content only exists after JavaScript runs | Medium | Fixed | Site code |
| 7 | No DMARC record for the domain's email | Low | Fixed (monitoring mode) | DNS provider |
| 8 | Missing file-type protection header | Low | Open | Hosting |
| 9 | Activity chart stopped at day 240 | Low | Fixed | Site code |

## Findings in detail

### 1. No policy limiting what the page may load

**What I observed.** The server sent no `Content-Security-Policy`. That header tells the browser which scripts, styles and images a page is allowed to load. Without it, if a script on the site were ever tampered with, nothing would limit what it could pull in.

**Evidence.** `curl -sI https://juribuora.com/` returned only one security header, `strict-transport-security`.

**What was done.** The site is hosted on GitHub Pages, which does not let a site owner set response headers. So the policy is now declared inside each page instead. It allows scripts only from the site itself, blocks plugins and form submissions, and allows images over HTTPS. One inline script had to become a separate file so the policy needed no exception for it.

**How I checked it.** I opened nine pages of different types in a real browser with a listener for policy violations. It recorded none, and every page still worked, including posts with code blocks and remote images.

**What is left.** A policy declared in the page is weaker than one sent as a header: it cannot stop framing (finding 2) and cannot report violations. The engine only looks for the header, so it still lists this finding. That is correct as far as it goes.

### 2. The site can be shown inside another site's frame

**What I observed.** Neither `X-Frame-Options` nor a `frame-ancestors` rule is sent. Another site could display juribuora.com inside an invisible frame and trick a visitor into clicking something on it.

**How much it matters here.** Little, today. The site has no login, no forms and nothing a click can change. It would matter the day any of those are added.

**What to do.** This cannot be fixed in the page. It needs something in front of the site that can add headers, such as a CDN or a different static host. Until then it stays open, on purpose and written down.

### 3. Home page slower than recommended on a phone

**What I observed.** On a phone over slow 4G the main content took 1.9 to 3.1 seconds to appear across three runs, median 2.6. Google's "good" threshold is 2.5.

**Cause.** The background picture at the top of the home page was a 221 kB JPEG, 1920 pixels wide, sent at full size to every screen, and the browser only learned about it late.

**What was done.** The picture is now served as WebP in two widths (29 kB for phones, 80 kB for larger screens) and marked as high priority.

**After.** 1.95 to 2.13 seconds, median 1.98. Total download for the page went from 429 kB to 237 kB.

### 4. Accessibility problems

Lighthouse scored accessibility 86 and named four problems.

| Problem | Evidence | Fix |
| --- | --- | --- |
| Section headings too faint | Dim green on the dark background measured 2.17 to 1. The minimum for small text is 4.5 to 1. | Colour changed in both light and dark themes to clear 4.5 to 1. |
| No main landmark | Screen-reader users could not jump to the content. | Every page type now marks its main content. |
| Progress bar had no name | A screen reader announced "progress bar" and nothing else. | It now says what it measures. |
| Touch targets too small | About 240 chart squares roughly 12 px wide on a phone, and about 1,500 tag chips 19 px tall. The minimum is 24 px. | On phones the chart squares are no longer links (the post list below is). On larger screens the chart has fewer, bigger columns. Tag chips are now 24 px tall. |

**After.** Accessibility 100, with no failing checks.

### 5. Pictures missing in older posts

**Found by hand, not by the tool.** While testing the new policy I noticed that 12 pictures across 6 older posts did not load. The posts are written for a different site generator. Their picture paths either pointed at files that only exist on the original site, or used template code that this site does not run.

**What was done.** Picture paths are now resolved to the original site when a post is displayed. 11 of the 12 load.

**The twelfth.** One picture in one post referred to a file by name only, and the file had never been published. The reference was removed from the post.

### 6. Page content only exists after JavaScript runs

**Found by hand, from the tool's own raw numbers.** The HTML the server sends contains 39 characters of visible text and no heading. Everything a visitor reads is built by JavaScript afterwards. The engine's link checker therefore checked 0 links: from where it stood, the page was empty.

**Why it matters.** Google runs JavaScript, so search results are fine, and the search-visibility score is 100. But many other readers do not: some link-preview services, several AI crawlers, and anyone with scripts disabled.

**What is already in place.** Each page has its own static title, description and share picture, so links shared on social networks look right.

**What was done.** Fixed later the same day: the readable text of every page is now generated at build time (prerendering), and the browser takes over from there. Measured again on the live site, the home page went from 39 characters of visible text and no heading to 86,291 characters and one heading, and the link checker went from 0 links checked to 25, none broken.

**What it cost, and the follow-up.** At first the home page download grew from 237 kB to 308 kB, because every post card was in the page itself, and the phone performance score moved from 99 to 98. The same evening the home page was changed to carry the 50 newest posts and add the rest as the reader scrolls. Measured again: 279 kB, performance back at 99, main content visible at 2.0 seconds (median of three runs), and 22,782 characters of visible text in the HTML. The full list stays on the blog and labs archive pages for readers without JavaScript. The "After" column in the table at the top was measured before these changes.

### 7. No DMARC record for the domain's email

**What I observed.** The domain receives mail (it has MX records) and declares who may send for it (it has an SPF record). It has no DMARC record. DMARC is the piece that tells other mail servers what to do with messages that fail those checks, and where to send reports.

**Evidence.** `dig +short TXT _dmarc.juribuora.com` returned nothing.

**Why it matters.** Without it, someone can more easily send email that claims to come from this domain.

**What to do.** Add one DNS record at the domain provider. Start in monitor mode, read the reports for a few weeks, then tighten:

    _dmarc.juribuora.com.  TXT  "v=DMARC1; p=none; rua=mailto:<reports address>"

**Done the same evening.** The record was added at the DNS provider in monitor mode (`p=none`) and confirmed by asking the domain's own name server. The next step is to tighten it once a couple of weeks have shown nothing legitimate failing.

### 8. Missing file-type protection header

**What I observed.** `X-Content-Type-Options: nosniff` is not sent. It stops a browser guessing a file's type, which closes off a class of old tricks.

**What to do.** Same as finding 2: it can only be sent as a header, so it waits for a hosting change. Low priority.

### 9. Activity chart stopped at day 240

**Found by hand.** The home page chart of daily posts was built for a 240-day goal. The log is now past day 240, so the newest days had no square and the progress line read "101%".

**What was done.** The chart grows with the log, and the line now says the goal was reached and by how many days.

## Where the tool was wrong

Two of the engine's seven original findings do not apply to this site:

- **"Physical address not visible on the home page."** True, and irrelevant. This is a personal site, not a shop.
- **"Google cannot find the business's structured data."** Same reason: there is no local business to describe.

The engine is tuned for small local businesses. On any other kind of site these two need a human to dismiss them. I left them out of the priority table and note them here instead of pretending the output was clean.

## What was already right

- HTTPS only: plain HTTP redirects to HTTPS, and `www` redirects to the main address.
- `Strict-Transport-Security` is sent, so browsers refuse to downgrade.
- TLS 1.3, certificate from Let's Encrypt valid until 18 November 2026 and renewed automatically by the host.
- No trackers detected, so no cookie banner is needed.
- Compressed responses, a page title of sensible length, a description, and share-preview tags.
- Best practices 100 and search visibility 100, before and after.

## Open items

| Item | Needs | Priority |
| --- | --- | --- |
| Framing protection and file-type header (findings 2, 8) | Hosting that can send headers | Medium, rises if a login or form is added |
| Tighten DMARC from monitoring to rejecting (finding 7) | A couple of weeks of monitoring first | Low |

## Technical appendix

| Measurement | Value |
| --- | --- |
| Final address | `https://juribuora.com/` |
| Response | 200, first byte in 0.32 s |
| Server | GitHub Pages |
| HTTP to HTTPS | 301 redirect |
| Certificate | Let's Encrypt, expires 18 Nov 2026, TLS 1.3 |
| Security headers sent | `strict-transport-security: max-age=31556952` only |
| Policy declared in the page (after) | `default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:; connect-src 'self'; object-src 'none'; base-uri 'self'; form-action 'none'` |
| Email DNS | MX present, SPF present. DMARC absent at the time of the check, added later the same day |
| Trackers | None detected |
| Lighthouse | 13.4.1, phone profile, simulated slow 4G, 3 runs |
| Scores before | Performance 96, accessibility 86, best practices 100, search 100 |
| Scores after | Performance 99, accessibility 100, best practices 100, search 100 |
| Main content visible | Before 1.93 to 3.13 s (median 2.61). After 1.95 to 2.13 s (median 1.98) |
| Home page download | Before 428,548 bytes. After 237,171 bytes |
| After prerendering (finding 6) | Performance 98, accessibility 100. Main content visible 2.03 to 2.43 s (median 2.10). Home page download 308,186 bytes. Visible text in the HTML 86,291 characters, 25 links checked |
| After the home list was shortened | Performance 99, accessibility 100. Main content visible 1.88 to 2.25 s (median 1.95). Home page download 278,763 bytes. Visible text in the HTML 22,782 characters, 25 links checked |

## Limits of this report

This is an external, non-invasive review of a public website. It does not include penetration testing, aggressive scanning, login testing or exploit attempts. It covers the home page in depth and other page types only where a fix had to be verified. A clean result here does not mean the site is secure; it means these specific checks found nothing more.

<!-- 01-10-2026 22:59 -->
