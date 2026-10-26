---
name: radical-tempo-content-lifecycle
domain: product
description: >
  How a video moves through Radical Tempo from upload to an analyzed post,
  framed in user-visible stages (uploaded → processing → ready, then per post:
  draft → scheduled → publishing → published → analyzed). Includes typical
  processing times by video length. Load when the user asks where their content
  is, what happens next, why something is taking time, or how something flows
  end-to-end.
---

# Content Lifecycle

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-08-03    |

A video moves through two related lifecycles in Radical Tempo: the **asset
lifecycle** (one video, one path through processing into being clip-ready) and
the **post lifecycle** (each clip can produce one post per platform, and more
than one over time, each with its own state).

## Asset lifecycle

These are the stages a single video moves through, from the user's view.

### 1. Uploaded / Imported

The source is in the brand. The user sees the asset appear in the **Content**
library. Processing begins automatically — the user does not need to trigger it.

If the source is an import (Drive, Dropbox, S3), Radical Tempo pulls the file in
first, which adds time before processing starts.

### 2. Processing

Radical Tempo analyzes the video. While in this state the user sees a
**Processing** label on the asset. What's happening internally (don't narrate
this in detail; mention only as much as the user asks):

- Radical Tempo transcribes the audio (for captions, hooks, and search).
- It analyzes the video visually — shots, energy, subjects on screen.
- It detects people and objects.
- It does not yet compute highlights; that happens once the user triggers clip
  generation. Processing only prepares the inputs.

The user does not have to wait for this state to end before asking for clips.
**Generate clips** is offered on a still-processing video and the request is
queued: the clips are cut as soon as processing finishes. Editing, reframing,
and publishing do need a Ready video.

**Typical processing time** (rough; varies with platform load and content
complexity):

| Video length | Typical processing time |
| ------------ | ----------------------- |
| < 2 minutes  | ~150 seconds (~2.5 min) |
| 2 – 5 min    | ~180 seconds (~3 min)   |
| 5 – 15 min   | ~250 seconds (~4 min)   |
| 15 – 30 min  | ~400 seconds (~7 min)   |
| 30 – 60 min  | ~600 seconds (~10 min)  |
| 60 – 120 min | ~900 seconds (~15 min)  |

If a video has been processing much longer than these ranges, something has gone
wrong. Suggest checking the asset's status and, if available, retrying the
import or contacting support. Do not promise a service-level agreement (SLA).

### 3. Ready

Processing is done. The user can now:

- **Generate clips** (highlights are computed at this step) — available during
  Processing too, as above.
- **Review the asset** — get a quality assessment if a review tool is available.
- **Edit, reframe, or publish** the source video (or any clip derived from it).

Thumbnails and search indexing may finish slightly after the asset is otherwise
ready. Per-post data is available after a post has been published through
Radical Tempo, or if it's a pre-existing post, on a rolling cycle of daily
analytics ingestion. Users rarely notice; mention it only if asked why something
specific is delayed.

### 4. Failed

If processing failed, the asset shows **Failed**. The user can dismiss the
failure from the upload widget. (Retry behavior depends on the failure mode —
don't promise a specific retry path.)

## Post lifecycle

Once a clip exists, the user can create **posts** for it on specific platforms.
Each post moves through its own states, independent of other posts for the same
clip.

### Draft

The user is preparing the post — title, description, tags, scheduled time.
Nothing has been sent to the platform yet. The user can edit freely or save and
come back later.

### Scheduled

The post is queued for a specific future date and time. Radical Tempo will
publish at the scheduled moment. Until publishing begins, the user can still
edit the post, cancel the schedule in the app, or ask Cue to cancel it.

### Publishing

Radical Tempo is currently sending the post to the platform. This is usually
brief but can take longer for large videos or when a platform is slow.

### Published

The post is live on the platform. From here, performance data flows back from
the platform into Radical Tempo's analytics. This is also where analytics
surfaces show the post.

### Updating

Radical Tempo is applying a metadata update (title, description, or similar) to
a published post. Some platforms support this; some don't.

### Error

Something went wrong during publishing — a platform connection expired, the
platform rejected the post, or a network issue. The user can review the error,
fix the underlying problem (e.g., reconnect the platform), and retry.

## After publishing: analyzed

Performance data is fetched from each platform and surfaced under the
**Performance** section (the **Analyze** item in the brand sidebar). Different
platforms expose different metrics, and the data takes time to populate after a
post goes live (commonly hours to a day for early signal). The user sees:

- **Overview** — top-level performance for the brand.
- **Top content** — strongest posts.
- **Data** — per-post metrics (views, watch time, and engagement signals,
  depending on platform).

Performance data also feeds back into how Radical Tempo generates the next round
of clips for this brand.

## What this lifecycle does _not_ cover

- The internal pipeline stages (visual event log generation, moment discovery,
  materialization, per-platform uploaders). Not user knowledge — stays in tools
  and code.
- Job-queue mechanics, retry policies, and worker types.
- Live status of any specific asset or post. The KB describes the shape of the
  lifecycle; current state is a tool call.

For "what stage is _my_ asset in right now?", call a tool. For "what does
processing involve?", this skill.
