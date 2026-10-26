---
name: radical-tempo-glossary
domain: product
description: >
  Canonical Radical Tempo terminology — what each term means, how the product
  uses it, and when two terms are different (highlight vs clip vs edit, brand vs
  workspace, asset vs content). Load when the user uses an ambiguous term, asks
  for a definition, or seems to be conflating two concepts.
---

# Glossary

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-09-02    |

This is the source of truth for terminology Cue uses with creators. When a user
uses a synonym or an internal-only term, translate to the canonical form before
answering.

## Core nouns

### Asset

An **asset** is the umbrella term for any piece of content in a brand. Today,
every asset is either a **video** or a **clip**. Images and carousels are
planned as additional asset types.

The user-facing surface for assets is the **Content** library — the table is
labeled "Content," not "Assets." Use "video" or "clip" in conversation unless
the user explicitly says "asset" first.

**The tools say "asset" and you say "video" — both are right.** A tool
description, a parameter, and a rejection message all name the asset, because
what they are talking about is the record: its source video, its clips, and the
posts hanging off it. "Video" cannot carry that — a video is a file, and a file
has no clips. So read "asset" as the thing you are operating on, and say "video"
or "clip" when you tell the user what you did.

### Video

A **video** is a source asset — typically a long-form recording the user
uploaded or imported (from Drive, Dropbox, S3, or a direct file upload). Videos
are the input to clip generation.

### Clip

A **clip** is the final short-form asset that gets published. A clip is produced
from a video by selecting a strong moment, cropping/reframing it, and applying
overlay text, captions, and styling. The user can edit a clip after it's
generated.

### Source video

A clip's **source video** is the one video that clip was cut from. Users say it
many ways: "the source," "the parent video," "the original," "the full video,"
"the long one," "the one this came from." All of them name that video.

- A source video is always a video, never the clip itself.
- Clip generation runs on the source video. "Make more clips from that" said
  about a clip means that clip's source video, and
  `radical-tempo-user-workflows` covers resolving it.
- A clip's source video is the first thing to reach for when the user points at
  a clip and asks for something only a full video supports.

### Highlight

A **highlight** is an interesting moment Radical Tempo identifies inside a video
— a candidate region that could become a clip. The clip generation flow surfaces
highlights in a gallery; the user (or Radical Tempo's auto-accept) promotes a
highlight to a clip.

A highlight is _not_ the same as a clip:

- A **highlight** is the section of the source video Radical Tempo flagged as
  interesting.
- A **clip** is the materialized, edited, publishable asset built from one (or
  several combined) highlights.

If a user asks "where are my highlights?" they mean the highlight gallery inside
the create-clips flow — a page per source video, holding every generation run
for that video.

"Where are my clips?" depends on which clips. Clips from a particular generation
run are on that video's highlight gallery, which is also the only place to
accept or reject them; the Content library holds the brand's clips, alongside
the videos they came from. So a user asking about the clips a run just made
means the gallery, not the library.

The library is organized into folders and opens on the top level, so it shows
the videos in no folder rather than everything at once. A user who expects to
see every video there wants All content.

### Edit (planned)

An **edit** will be a future concept: a version of a clip optimized for a
specific platform or a specific length (e.g., a 45-second edit and a tighter
25-second edit of the same clip, telling the same story at different runtimes).
Different platforms favor different durations.

Edits don't ship today — only mention this if the user asks about
length-targeted variants. Don't preemptively offer "edits" as a feature.

### Post

A **post** is an instance of a clip (or video) published to a platform. This is
the term the product UI and Cue use with creators; the database and internal
tooling still call it a **publication** (the `publications` table, the
`publication_phase` and `publication_type` columns). A post has its own state —
Draft, Scheduled, Publishing, Published, Updating, Error — independent of the
underlying asset.

A single clip can have one post per platform, and more than one over time.

A post's title, description, tags, and cover belong to the **post** itself, not
the underlying video or clip — the asset is the raw content (footage,
transcript, analysis). "The title" of a posted video is always post data.

### Brand (a.k.a. Workspace)

A **brand** is the workspace a user works inside — where their content,
connected platforms, style guide, and team members live. The UI uses "brand"
everywhere; the database table is `workspaces`.

Use **brand** in conversation. "Workspace" is internal terminology only.

A brand typically maps to a customer's overall identity rather than a single
channel. One brand can connect to multiple platforms (e.g., a YouTube channel
_and_ a TikTok account _and_ an Instagram profile, all under the same brand).

If a user says "channel," they may mean either their brand as a whole or a
specific platform connection — clarify if it matters.

### Platform connection

A **platform connection** is the OAuth-authorized link between a brand and a
specific social platform account (e.g., a YouTube channel, an Instagram profile,
a TikTok account). Each connection is brand-scoped and per-user authorized.

### Style guide

A **style guide** is the brand's voice and presentation rules — voice
description, energy/formality levels, emoji style, title/description guidance,
hashtag strategy, and overlay-text guidance. Radical Tempo generates a draft
style guide from previously published content on the brand's connected channels,
and the user can view and edit it under **Settings → Style Guides**.

The user-visible section header is "Brand Identity"; the editable sub-sections
are "Title Guidance," "Description Guidance," "Tag Guidance," and "Overlay
Guidance."

### Template (Video Template)

A **template** is a brand's saved, reusable set of on-clip **visual** styling
and layout — caption/hook/end-text styling, image (logo) overlays, fonts,
colors, and style presets. The UI labels them **Video Templates** (brand
Settings), and each opens in the **Template editor**.

A brand can mark one template as the **brand default**, which is applied
automatically to new clips; users can switch templates or override elements
per-clip in the clip editor's **Templates** section.

Distinguish **template** (visual styling) from **style guide** (voice and text
rules) — the two are complementary, not the same thing. Internal tooling calls a
template a "layout template"; use **template** with users.

### Entity

An **entity** is a person or object Radical Tempo detects in a video —
identified during processing and used to support entity-aware clip generation
(e.g., a Director's note that focuses on a specific person). Entities are
largely an internal concept today; only mention them if the user asks about
people/object detection or the Director's note feature.

## Clip-editor terms

These are exact field labels in the clip editor; use these strings verbatim when
referencing the UI.

### Hook text

**Hook text** is the on-screen text at the very beginning of a clip, designed to
stop a scrolling viewer and pull them into the clip. Radical Tempo generates
hook-text suggestions automatically; the user can edit, disable, or write their
own. Hook text is also available on full videos, not just clips.

### End text

**End text** is on-screen text at the end of a clip, typically a call to action
("Follow for more," "Link in bio," etc.). User-defined.

### Captions

**Captions** are the auto-generated transcript text shown on screen during a
clip — the words the speaker is saying. They let viewers follow along with sound
off.

Don't confuse captions with the platform "caption" field (the description text
that goes _alongside_ a post on platforms like Instagram or TikTok) — that's the
**description** in Radical Tempo's vocabulary.

### Logo

A **logo** is a brand-level image asset; it can be applied to videos and clips
for branding.

### Reframe

**Reframe** is the action of changing a clip's output aspect ratio (and the crop
applied to fit it). Available aspect-ratio options are: Full Portrait (9:16),
Dynamic Splits, Short Crop (3:4), Square Crop (1:1), Widescreen Crop (16:9), and
Adaptive Crop (Beta).

## Internal-only terms — translate before using

These appear in internal documentation, code, and pipeline logs. Do not use them
with users. If a tool result references one, translate to the user-facing term
before responding.

| Internal term      | User-facing equivalent                                                                         |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| `workspace`        | brand                                                                                          |
| `asset`            | video, clip, or "content" — the tools say "asset" on purpose; translate it only when you speak |
| `clip_suggestion`  | highlight (when surfaced) or just "clip" once accepted                                         |
| `moment`           | highlight                                                                                      |
| `visual event log` | (don't surface) — internal analysis output                                                     |
| `composition`      | (don't surface) — internal layout/crop description                                             |
| `publication`      | post                                                                                           |
| `manifest`         | (don't surface) — internal render description                                                  |
| `layout_template`  | template (or "Video Template")                                                                 |

## Per-platform wording

Creators talk about short-form content using each platform's native term. Match
the user's wording when possible.

| Platform  | What creators call a short |
| --------- | -------------------------- |
| TikTok    | Video                      |
| Instagram | Reel                       |
| YouTube   | Short                      |
| Facebook  | Reel or Video              |
| LinkedIn  | Video                      |
| X         | Video                      |
