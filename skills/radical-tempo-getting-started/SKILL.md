---
name: radical-tempo-getting-started
domain: product
description: >
  The first-time creator path in Radical Tempo — the five-step onboarding
  checklist (upload, generate clips, connect a platform, publish, invite a team
  member), what to expect at each step, and what a brand-new user should do
  first. Load when the user is new, asks "what should I do first?", or has just
  signed up.
---

# Getting Started

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-08-03    |

A brand-new user lands inside a brand and sees an onboarding checklist guiding
them through their first end-to-end loop. The checklist's exact order matters —
each step makes the next one better.

## The onboarding checklist

The checklist guides the user through five steps in order:

1. **Upload or import** — bring a video into the brand.
2. **Generate AI clips** — let Radical Tempo turn it into shorts.
3. **Connect a platform** — authorize at least one social account.
4. **Publish video** — get a clip live.
5. **Invite a team member** — add a collaborator.

(These are the exact labels the user sees in the checklist.)

The checklist is shown on the Content view of a new brand and dismisses once
completed. Progress is per-user/per-brand.

## What to do, step by step

### 1. Upload or import a video

Get a source video in. The fastest path is direct upload (drag a file into the
Content view). Alternative imports: pick from Google Drive or Dropbox, or pull
from S3. Any of them lands in the same Content library and starts processing
automatically.

What to expect: the asset appears with a **Processing** label. Typical times by
length range from ~2.5 minutes (for short videos) to ~15 minutes (for hour-plus
videos). See `radical-tempo-content-lifecycle` for the full table.

While the user waits, step 3 (connect a platform) is a productive use of the
time — and it makes step 2 produce better output.

### 2. Generate AI clips

Open the video and trigger **Generate clips** — no need to wait for **Ready**,
since a request made while it is still processing is queued. Pick an output
shape (default Full Portrait 9:16 is the safe choice for short-form), keep the
matching crop shape, and add a **Director's note** only if there's a specific
focus the user wants ("focus on the interview portion," "include the segment
around the 12-minute mark").

What to expect: Radical Tempo finds and evaluates highlights, drops the weak
ones, presents a highlight gallery, and **auto-accepts the top three** as clips
immediately added to the Content library. The user can accept more, reject some,
regenerate with different parameters, or combine highlights into a longer clip.

For first-time users, accepting the auto-three is the fastest path to something
publishable. They can refine later.

### 3. Connect a platform

Go to **Connect** in the brand sidebar (the Platforms page). Pick a platform
(TikTok, YouTube, Instagram, LinkedIn, Facebook, X), authorize via OAuth on that
platform's auth screen, and return.

**Doing this earlier in the flow pays off twice**: connected platforms let
Radical Tempo generate a starter style guide from the brand's existing posts
(improves clip generation), and they unlock publishing in step 4.

If a user has a TikTok and a YouTube, connecting both is reasonable — each
connection takes a minute or two of OAuth.

### 4. Publish a clip

From any clip, create a post targeting one of the connected platforms. Radical
Tempo generates a platform-fit title, description, and tags from the clip's
content and the brand's style guide. The user edits any of those, then chooses
**publish now** or **schedule** for later (or saves as a draft).

What to expect: the post moves through Draft → Publishing → Published, usually
quickly. Once Published, performance data starts flowing back into Performance
over the next hours to days.

### 5. Invite a team member

Under brand settings, invite a collaborator by email. The brand has one
**owner** with full control; everyone else invited joins as a collaborator with
shared access. Invitees get an email; once they accept, they can see and act
inside the brand.

If the brand is solo, this step can be skipped — the checklist still counts
toward "complete" if the user hasn't invited anyone, since this is more of a
"ready when you need it" step than a strict prerequisite.

## What's _next_, after the checklist

The onboarding checklist gets the user to their first post. Beyond that, the
natural next steps are:

- **Review performance** — open **Analyze** in the brand sidebar (the
  Performance section) to see how published content is doing. Early data takes
  hours to a day to populate meaningfully.
- **Refine the style guide** — once a few posts are live, the brand's voice is
  clearer. The user can edit voice, title/description/tag/ overlay guidance
  under **Settings → Style Guides**.
- **Generate more clips** — from existing videos or new uploads. Each round
  benefits from the prior round's performance data.
- **Adapt clips for more platforms** — reframe a successful clip to a different
  aspect ratio and publish to additional platforms.

## Common first-day questions Cue should preempt

- _"Why is my video still processing?"_ — explain the processing-time table
  (`radical-tempo-content-lifecycle`).
- _"Why are clips already in my library before I picked any?"_ — the top three
  highlights are auto-accepted as clips so first-time users have something
  publishable immediately.
- _"Why does Radical Tempo know my voice already?"_ — once a platform is
  connected, the style guide is generated from the brand's existing posts on
  that channel.
- _"Where do I see how it's doing?"_ — the Performance section, under
  **Analyze** in the sidebar. Data takes hours to a day to populate.
- _"Can I download my clips?"_ — yes, clips are downloadable from the Content
  library.

## What this skill does _not_ cover

- The full feature surface — load `radical-tempo-feature-catalog`.
- Per-platform setup details — load `radical-tempo-platform-support`.
- Live progress on a specific user's onboarding (which steps they've done) —
  that's a tool call.
