---
name: radical-tempo-platform-support
domain: product
description: >
  Which social platforms Radical Tempo supports, what each one allows
  (publishing, analytics, scheduling), the auth shape, and per-platform format
  expectations. Load when the user asks whether a platform is supported, how to
  connect one, what's different between platforms, or what format/aspect ratio
  fits a platform best.
---

# Platform Support

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-09-22    |

Radical Tempo connects to social platforms via OAuth on a per-brand basis. Once
connected, the brand can publish (now or scheduled). Analytics flow back into
Performance from TikTok, YouTube, Instagram, and Facebook.

## Platforms at a glance

| Platform         | Status      | Publishing | Analytics | Notes                                                         |
| ---------------- | ----------- | :--------: | :-------: | ------------------------------------------------------------- |
| TikTok           | Live        |     ✓      |     ✓     | Production-ready, can connect and publish.                    |
| YouTube          | Live        |     ✓      |     ✓     | Channel-scoped; supports both Shorts and long-form.           |
| Instagram        | Live        |     ✓      |     ✓     | Reels.                                                        |
| LinkedIn         | Live        |     ✓      |     —     | Personal profiles and organization (company) pages.           |
| Facebook         | Live        |     ✓      |     ✓     | Page-scoped; Reels and standard videos.                       |
| X (Twitter)      | Live        |     ✓      |     —     | Production-ready, can connect and publish.                    |
| Snapchat         | Coming      |     —      |     —     | Connection infra exists; user-facing arrival later this year. |
| TikTok Marketing | Not exposed |     —      |     —     | Internal infra only; not a user-facing connection.            |

If a user asks about a platform _not_ in this table — Threads, Pinterest, and so
on — say it's not currently supported and don't promise a timeline.

## Connecting a platform

The user manages connections under **Connect** in the brand sidebar (the
Platforms page). Connecting a platform always uses OAuth — the user is sent to
the platform's auth screen, approves access, and is returned to Radical Tempo.

Each connection is **brand-scoped**: connecting an account on one brand does not
connect it on another. A user can connect multiple accounts of the same platform
across brands.

### Re-authorization

Platform tokens can expire (the platforms enforce this, not Radical Tempo). When
that happens, publishing fails with an Error state and the user is prompted to
reconnect. Reconnecting reuses the same brand connection slot — no data is lost.

## Per-platform notes

### TikTok

- Production-ready.
- Vertical short-form (9:16) is the sweet spot.
- Standard organic publishing through the connected TikTok account.
- The separate "TikTok Marketing" surface is internal infrastructure, not a
  user-facing connection — don't offer it.

### YouTube

- Channel-scoped — the user authorizes a specific YouTube channel.
- Supports both **YouTube Shorts** (vertical 9:16) and **long-form videos**
  (typically 16:9). Radical Tempo can publish whole videos to YouTube as well as
  clips.
- Rich analytics: views, watch time, traffic sources, demographics (subject to
  the channel's analytics permissions).

### Instagram

- Reels-focused — vertical 9:16.
- Connection is per Instagram account.
- Insights flow back as analytics.

### LinkedIn

- **Personal profiles and organization (company) pages.** When the user connects
  LinkedIn, organizations they're an admin on become available as publish
  destinations alongside their personal profile. Each post picks one destination
  at compose time, so a brand can mix personal-voice and company-voice content
  from the same connection.
- 16:9 and 1:1 work well; vertical short-form is acceptable.
- Professional, longer-form pacing fits the platform.
- No analytics yet. A LinkedIn post publishes, and its views and reactions do
  not flow back, so LinkedIn is absent from Performance and from every
  performance report. Say so rather than reporting it as zero.

### Facebook

- Page-scoped.
- Supports both **Reels** (vertical 9:16) and standard video posts (16:9 or
  1:1).
- Analytics flow back from the connected Page.

### X (Twitter)

- Production-ready as of 2025-10-03.
- Video publishing supported.
- Vertical and landscape both viable; X is format-flexible.
- No analytics yet. An X post publishes, and its views and reactions do not flow
  back, so X is absent from Performance and from every performance report. Say
  so rather than reporting it as zero.

### Snapchat

- **Coming later this year.** Underlying connection infrastructure exists, but
  the user-facing capability is not yet live.
- If a user asks today, say "not yet, planned for later this year." Don't give a
  precise date.

## Format and duration sweet spots

These are general guidance — actual best fit varies by content. The
clip-generation flow defaults to vertical 9:16 because most short-form platforms
are vertical-first.

| Platform          | Aspect ratio       | Duration sweet spot |
| ----------------- | ------------------ | ------------------- |
| TikTok            | 9:16               | 15 – 60 s           |
| Instagram Reels   | 9:16               | 15 – 30 s           |
| YouTube Shorts    | 9:16               | 30 – 58 s           |
| YouTube long-form | 16:9               | 8 – 15 min          |
| LinkedIn          | 16:9 or 1:1        | 30 – 90 s           |
| Facebook Reels    | 9:16               | 15 – 30 s           |
| Facebook video    | 16:9 or 1:1        | 60 – 180 s          |
| X (Twitter)       | 9:16, 1:1, or 16:9 | 15 – 60 s           |

For more prescriptive review and scoring of a specific clip against a platform
target, load the `radical-tempo-video-analysis` skill — it has the actual
rubrics. This leaf is descriptive only.

## Per-platform constraints creators run into

- **Field-length caps on titles, descriptions, and hashtags** vary by platform.
  Radical Tempo's metadata generator respects platform caps.
- **Hashtag rules** vary (Instagram allows more hashtags than most; LinkedIn
  works best with fewer; X is flexible). The brand's style guide tag-strategy
  carries per-platform preference.
- **Rate limits** on the platform side can delay publishing during heavy traffic
  or when posts are scheduled close together. The user sees this as a Publishing
  state taking longer than usual, occasionally failing into Error with a retry
  option.

## What this skill does _not_ cover

- Live connection status for the user's brand. ("Is _my_ TikTok connected?" is a
  tool call, not the KB.)
- Live performance numbers. ("How is _my_ post doing on YouTube?" is a tool
  call.)
- Internal API mechanics, rate limit thresholds, OAuth scope details, webhook
  plumbing — engineering reference, not user education.
