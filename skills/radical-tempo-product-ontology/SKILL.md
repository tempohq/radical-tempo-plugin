---
name: radical-tempo-product-ontology
domain: product
description: >
  The core concepts in Radical Tempo as a creator experiences them — brand,
  asset (video/clip), highlight, post, platform connection, style guide, entity
  — and what Radical Tempo knows about each. Load when the user asks what
  something is, why two things are distinct, or what Radical Tempo "has" about
  their content.
---

# Product Ontology

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-09-02    |

This is the user-facing mental model for Radical Tempo. It is _not_ a database
schema. For terminology disambiguation alone (e.g., "is highlight the same as
clip?"), the `radical-tempo-glossary` skill is more direct; load this skill when
the user wants to understand the relationships and what's stored.

## Brand

A **brand** is the workspace a user operates inside. It holds:

- The brand's **content library** (videos and clips).
- The brand's **platform connections** (the social accounts authorized to post
  on this brand's behalf).
- The brand's **style guide** (voice, title/description/tag/overlay guidance).
- The brand's **templates** (reusable on-clip styling and layout — "Video
  Templates").
- The brand's **members and invitees** (people who can collaborate).

A user can belong to multiple brands and switch between them. A brand can
connect to one or more social platforms — a brand is the customer's overall
identity, not one specific channel.

The UI uses "brand" everywhere; "workspace" is internal-only.

## Asset: video, clip, (and planned: image, carousel)

An **asset** is any piece of content in a brand. Today, every asset is one of
two kinds:

- **Video** — a source asset, typically long-form, that the user uploaded or
  imported. Videos are the input to clip generation.
- **Clip** — a derivative short-form asset built from a video. Clips are what
  get published.

Image and carousel are planned types; not live today. Don't preemptively offer
them as features.

Every clip names exactly one video it was cut from — its **source video** — so a
video has a one-to-many relationship with its clips, and that link is what "more
clips from that video" resolves through when a clip is what the user pointed at.
A user can also combine multiple highlights from a video into a single longer
clip (a non-contiguous edit, or a compilation).

The user-facing name for the assets table is **Content**, not "Assets" — match
the UI when referring to "your content."

### What Radical Tempo keeps about a video

When a video lands, Radical Tempo processes it and derives six artifacts. The
user doesn't see most of these directly; they power clip generation, review, and
search.

- **Transcript** — words spoken in the video, with timestamps. Used for
  captions, hook generation, search, and analysis.
- **Visual analysis** — what's happening visually over time (shot changes,
  energy, subjects on screen). Internal.
- **People and object detection** — entities detected in the video. Powers
  entity-aware clip generation and the Director's note feature.
- **Audio signals** — pacing, volume, sentiment cues. Internal.
- **Highlights** — the interesting candidate moments Radical Tempo flagged for
  clip generation.
- **Derived clips** — the clips a user has accepted or generated from this
  video.

When the user asks "what does Radical Tempo know about my video?", answer at
this level: transcript, visual analysis, entities, highlights, derived clips.
Don't surface internal field names like `visual_event_log` or `composition`.

## Highlight vs. clip

This is the most common point of confusion.

- A **highlight** is a candidate moment Radical Tempo identifies inside a video.
  It lives in the highlight gallery during clip generation. It is not a
  publishable thing on its own.
- A **clip** is a materialized, edited, publishable asset built from one or more
  highlights, with crop, overlay text, captions, and styling applied.

Once the user accepts a highlight (or Radical Tempo auto-accepts the top three),
it becomes a clip and shows up in the Content library.

## Post

A **post** is an instance of an asset being published to a platform. This is the
term the product UI and Cue use; the database and internal tooling call it a
**publication**.

Each post has its own lifecycle independent of the asset:

- **Draft** — the user is editing the post's metadata (title, description, tags,
  scheduled time) but hasn't sent it.
- **Scheduled** — queued for a future publish time.
- **Publishing** — currently being sent to the platform.
- **Published** — live on the platform; performance data flows back from here.
- **Updating** — an in-flight metadata update on a published post.
- **Error** — something went wrong (auth expired, platform rejected, or a
  network issue).

A single clip can produce multiple posts: one per platform, often with different
titles, descriptions, or scheduled times tuned per platform.

### Asset vs. post — where the data lives

Think of the asset as the raw file plus what Radical Tempo derived from the
footage, and the post as everything layered on top for one platform:

- **On the asset**: the video content itself, transcript, visual analysis,
  entities, highlights, analytics.
- **On the post**: the title, description, tags, the cover image (and previously
  used covers), the schedule, and platform-specific settings.

When a request involves what viewers see on a platform — a post's title, its
cover/thumbnail, its description — the post is the thing to look at; the asset
won't have it. When a request involves what's _in_ the video — what was said,
what happens on screen — that's the asset.

### Images around a video

Four distinct images can attach to a video and its posts. Keep them straight,
because they answer different questions:

- **The video's thumbnail frame** lives with the _video_ — a still frame Radical
  Tempo extracted from the footage. It's a real image _of_ the content, so it's
  the answer to "what does this video look like?" and a natural reference for
  generating new imagery or a quick stand-in cover.
- **A post's current cover** lives with the _post_, not the video — the image
  chosen to represent that one post on its platform (a YouTube thumbnail, a Reel
  cover). Each post can have a different one. Reach for it for "what's this
  post's cover?".
- **Previously used covers** are the history of covers a post has had —
  searchable when the user wants to reuse or point back to an image they've used
  on that post before.
- **Freshly generated candidates** are brand-new cover images produced on demand
  when the user asks for options to choose from.

So: "what does the video look like?" → the video's thumbnail frame; "change the
cover" / "what's the cover?" → the post's current cover (with its previously
used covers if they want to reuse one); "make me some thumbnail options" →
freshly generated candidates. The full generate-and-set loop is in
`radical-tempo-post-covers`.

## Platform connection

A **platform connection** is the OAuth-authorized link between a brand and a
specific social account on a platform. Connections are brand-scoped — a user
authorizes Radical Tempo for a specific brand's account, not globally.

Live platforms today: TikTok, YouTube, Instagram, LinkedIn, Facebook, X.
Snapchat is coming. (See `radical-tempo-platform-support` for what each supports
and the auth shape.)

## Style guide

A **style guide** is the brand's voice and presentation rules. Radical Tempo
generates an initial draft from previously published content on the brand's
connected channels (so connecting platforms before generating is recommended),
and the user can view and edit it under **Settings → Style Guides**.

A style guide carries:

- **Brand identity** — voice description, personality, energy and formality
  levels, emoji style, signature phrases.
- **Title guidance** — what works, what to avoid, optimal/max length.
- **Description guidance** — opening approach, structure, closing, hashtag
  strategy.
- **Tag guidance** — strategy, optimal count, must-include and avoid lists.
- **Overlay guidance** — style and tone for hook/end-text overlays.

The style guide is applied during clip generation and metadata creation.

## Template

A **template** (UI: **Video Templates**) is a brand's saved, reusable **visual**
layer for clips — the counterpart to the style guide's textual layer. It
bundles:

- **Overlay styling** — captions, hook text, and end text (how they look; the
  hook wording stays per-clip, but end-text copy can be supplied by the
  template).
- **Image overlays** — a logo or badge, with position, size, and timing.
- **Fonts, colors, and style presets** from the brand's identity.

A brand can have several templates and mark **one as the default**. The default
is applied automatically to new clips; the user can switch templates or override
individual elements on any single clip. Templates work with any output shape —
one template renders across aspect ratios.

Keep template (visual styling) distinct from **style guide** (voice and text
rules) and from **clip** (the actual materialized asset a template is applied
to).

## Entity

An **entity** is a person or object Radical Tempo detects in a video. Entities
power features like the Director's note ("focus on this person") and will
surface more in the UI over time. For now, entities are largely internal;
mention them only if the user asks about people/object detection or the
Director's note feature.

## What's _not_ in the ontology

These exist in the system but are implementation, not user mental model. Do not
surface:

- Background jobs and task executions.
- Internal storage paths and object IDs.
- Composition / manifest / render descriptions.
- Visual event logs and analysis intermediate outputs.
- Clip suggestions table (a backing structure for highlights).

If a tool result references one of these, translate before answering. See the
internal-only terms table in `radical-tempo-glossary`.
