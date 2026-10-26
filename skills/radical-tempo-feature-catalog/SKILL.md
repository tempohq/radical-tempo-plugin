---
name: radical-tempo-feature-catalog
domain: product
description: >
  What creators can do in Radical Tempo today — import, analyze, generate clips,
  edit, reframe, publish, review performance, automate a repeated step — and the
  three it does not: generate video from a prompt, create or alter audio, alter
  anything baked into the footage. Load when the user asks what's possible,
  whether a capability exists, what can be automated or run on its own, or for
  any of those three. Frames Radical Tempo as end-to-end: the value compounds
  across the full pipeline.
---

# Feature Catalog

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-09-28    |

This catalog covers the verbs — what a creator can do. For the nouns (what each
thing is), pair with `radical-tempo-product-ontology`.

## Why use the full pipeline

Radical Tempo is end-to-end on purpose. Each step feeds the next:

- **Connecting platforms** seeds the **style guide** (voice and metadata
  guidance are inferred from your existing posts).
- **Generating clips** uses the style guide to pick stronger highlights and
  write better hook text and descriptions.
- **Publishing through Radical Tempo** captures performance back into the brand,
  so the next round of clip generation knows what's working.
- **Reviewing analytics** surfaces patterns that inform the next batch.

This compounding loop sets Radical Tempo apart: the more platforms a brand
connects, and the more it publishes through Radical Tempo, the better each new
clip gets.

## Bring video in

The user can get a video into Radical Tempo four ways. All routes land in the
same Content library.

- **Upload** — direct file upload (drag and drop, or pick a file).
- **Google Drive** — pick a file via the Drive picker.
- **Dropbox** — pick a file via the Dropbox picker.
- **AWS S3** — pick a file from a connected S3 bucket.

After import or upload, processing starts automatically. (See
`radical-tempo-content-lifecycle` for what happens during processing and how
long it takes.)

## Browse and search content

The Content library holds every video and clip in the brand, with filtering by
status (Published, Scheduled, Draft) and other facets. Search supports finding
content by what is in it, its title, and its metadata.

Folders organize it. The library opens on the top level, which shows the videos
that are in no folder alongside the folders themselves; opening a folder shows
what is in it. A video lives in at most one folder and a clip lives wherever its
source video does. All content shows the flat list of everything at once,
whatever folder each video is in.

## Generate clips

This is the core flow. The user picks a video and triggers **Generate clips**
(also called create clips). They choose:

- **Output shape** (aspect ratio): Full Portrait (9:16), Dynamic Splits, Short
  Crop (3:4), Square Crop (1:1), Widescreen Crop (16:9), or Adaptive Crop
  (Beta). Defaults to 9:16 for vertical-first short-form.
- **Crop shape** — usually matches the output shape. Can also be split screen or
  chosen dynamically based on content type.
- **Director's note** _(optional)_ — free-text guidance: focus on a specific
  person, timestamp, theme, or event. (UI label: "Director's note".)

Radical Tempo finds the best highlights in the video, evaluates them, removes
overlapping ones, and surfaces the survivors in a **highlight gallery**. Radical
Tempo auto-accepts the top three highlights as clips, so the user has something
publishable right away. The user can then:

- **Accept** more highlights from the gallery.
- **Reject** highlights they don't want.
- **Re-generate** with a different Director's note, output shape, or parameters.
- **Combine** multiple highlights into one longer clip (a compilation or a
  non-contiguous edit).

The gallery is a page of its own, one per source video (its heading reads "Clip
Builder"), and it holds every generation run for that video side by side — so it
is where a user goes to see the clips a run just produced, and the page to send
them to when a clip generation finishes.

Accepted clips are also ready to edit, reframe, or publish from the Content
library, which lists them alongside every other asset in the brand.

## Edit a clip

The clip editor is where the user fine-tunes a generated clip. Capabilities:

- **Trim** start and end points.
- **Captions** — auto-generated from the transcript; the user can edit wording,
  styling, fonts, and colors. Captions can also stack up to 3 languages at once,
  one caption set per transcript — see **Caption languages** below.
- **Hook text** — auto-suggested overlay text at the start of the clip; the user
  can edit, write their own, or disable.
- **End text** — user-defined call-to-action overlay at the end of the clip.
- **Logo and image overlays** — apply brand-level images.
- **Fonts and colors** — pick from brand-defined typography and palette.
- **End cards** — upload images that play at the end of the clip.
- **Speed** — adjust playback speed.
- **Scene cuts** — remove sections inside the clip.
- **Reframe** (also a separate capability — see below).

Edits made in the clip editor save as the new state of that clip. The styling
choices (captions, hook/end text, logo and image overlays, fonts, colors) can be
saved as a reusable **template** so future clips inherit the same look — see
**Brand templates** below.

### Caption languages

A clip's captions can show up to 3 languages at once, stacked one above the
other, each from one of the video's transcripts. A video with a single
transcript has one language to show, so a second language starts with a
translation — see `radical-tempo-transcript-translation`.

In the editor, the captions settings on the **Design** tab list every transcript
the video has, each with its own switch:

- Turn a switch on to add that language to the stack, and off to take it out.
- Up to 3 switches can be on at once. With 3 on, turn one off before another can
  go on.
- The last switch that is on cannot be turned off, because captions that are on
  always show one language at least. To hide captions entirely, turn captions
  off.
- Drag a row up or down to move that language up or down the stack.

No tool sets these switches, so you cannot pick a clip's caption languages.
Point the user at the switches in the editor instead. A translation you start
can still change the captions of a clip that follows its template — see
`radical-tempo-transcript-translation`.

## Reframe

**Reframe** changes a clip's output aspect ratio and re-applies a crop to fit.
The user can pick any of the supported aspect ratios (Full Portrait 9:16,
Dynamic Splits, Short Crop 3:4, Square Crop 1:1, Widescreen Crop 16:9, or
Adaptive Crop (Beta)) at any time — at generation, in the editor, or after the
clip exists.

Radical Tempo suggests a default aspect ratio and crop based on content type and
the platform a clip is being prepared for. The user can override any default.

## Brand templates

A **template** (the UI calls these **Video Templates**) is a saved, reusable
bundle of on-clip styling and layout that a brand applies to its clips, so they
come out consistently branded without re-styling each one. A template can carry:

- **Hook-text** styling — the styling only; the hook wording itself stays
  per-clip and AI-generated.
- **End text** — including static call-to-action copy the template supplies
  (e.g. "Like and subscribe").
- **Captions** styling, plus a words-per-line setting and 2 language settings:
  - **Max languages shown** — how many languages a video using the template
    stacks, from 1 to 3. The template fills the stack from the video's newest
    transcripts, one per language.
  - **Prefer source language** — whether the transcript the video reads always
    takes the top slot.

  A video whose languages the user picked in the editor keeps that pick, and the
  template's settings no longer decide it.

- **Image overlays** — a logo or badge, with position, size, and timing.
- **Fonts, colors, and style presets** drawn from the brand's identity.

Templates **work with any output shape** — the same template renders across
aspect ratios (Portrait, Landscape, Short, Square).

**Where it lives.** Brand **Settings → Video Templates** lists the brand's
templates with a **Create** button; each opens the full-screen **Template
editor** (with `Template Elements`, a live preview, and **Save Template**).
Inside the clip editor's **Design** tab, a **Templates** section lets the user
turn a template on, **Switch** between the brand's templates, and override
individual elements on that one clip.

**The brand default.** A brand can mark **one** template as the **brand
default**. The default is **applied automatically** to new clips — the user
doesn't pick it each time — and they can still switch to a different template or
override elements per-clip. A clip that already has a template applied, or
that's been finalized, keeps what it has rather than absorbing the default.

**Templates vs. the style guide.** They're complementary: the **style guide**
governs the _words_ (voice, titles, descriptions, tags, overlay wording), while
a **template** governs the _visual layer_ (how captions, hook/end text, and
overlays look and where they sit). The style guide writes the copy; the template
styles the frame.

## Generate metadata

When a user prepares a post, Radical Tempo generates platform-fit **title**,
**description**, and **tags** based on the clip's content and the brand's style
guide. The user can edit any of these before publishing.

## Publish or schedule

From a clip, the user can create a **post** to one or more connected platforms.
For each post, they can:

- **Publish now** — send immediately.
- **Schedule** — queue for a future date/time.
- **Re-time a schedule** — propose a new time; it replaces the old one.
- **Cancel a schedule** — clear the time so the post won't go out, and stop the
  publish queued for it. The post returns to draft, ready to schedule again.
- **Save as draft** — keep the metadata but don't send.

Posts carry their own state: Draft → Scheduled → Publishing → Published. (Plus
Updating and Error.) A single clip can have one post per platform, and more than
one over time, each with its own metadata tuned for that platform.

Several platforms can go out together: one publish proposal can carry every
target, and the creator approves them as a set with a per-platform toggle rather
than confirming each one.

For platform-specific capabilities and constraints, load
`radical-tempo-platform-support`.

## Style guide

The brand's **style guide** captures voice (description, energy, formality,
emoji style, signature phrases) and presentation guidance for titles,
descriptions, tags, and overlays. Radical Tempo generates a draft from
previously published content on the brand's connected channels — so connecting
platforms before clip generation produces better starting content.

Style guides are viewable and editable under **Settings → Style Guides**.

## Performance and analytics

The **Performance** section — reached via the **Analyze** item in the brand
sidebar — surfaces analytics for published content across connected platforms:

- **Overview** — high-level dashboard.
- **Top content** — the brand's strongest posts.
- **Data** — per-post detail with platform metrics (views, watch time, and
  engagement, depending on platform).
- **CSV export** for offline analysis.

Performance data feeds the brand's understanding of what works. New clip
generation rounds use this data implicitly.

## Automations

An **automation** is a standing instruction that runs on its own. One trigger,
one thing it does, repeated until the user pauses or deletes it.

There are two doors into them:

- **The Automations tab**, in the brand sidebar, lists the automations Radical
  Tempo ships ready-made. **Enable** turns one on; **Configure** opens a short
  form for its settings. The same tab shows what is running for the brand and
  what each one last did, with controls to pause, edit, or delete it. One
  ready-made automation ships today: **Clips on upload**, which cuts clips from
  every video the user adds, steered by a note they write once.
- **Cue** sets up a custom one in conversation. The user says what they want
  repeated, Cue puts the automation in front of them, and it exists only once
  they approve it.

**What an automation does.** One of 2 things per run:

- **Media work on a video the user adds**: make clips, plan clips, build clips
  from a plan, combine highlights, reframe, polish a clip, translate a
  transcript, generate cover images, or import a video. Every run is the same
  work with the same settings.
- **An analysis on a clock**, daily or weekly or monthly: Cue reads how the
  brand's posts performed over the period and saves a report the user opens from
  the run, or writes a short summary. The user describes what they want in their
  own words — _"every Monday, tell me which openings held viewers"_ — and Cue
  tries it once in the conversation first, so they see what each run will
  produce before they approve it. The instructions they approve are shown on the
  approval card word for word.

**What starts one.** Media work runs on a video the user adds, an upload or an
import. An analysis runs on a clock, because a run a clock starts is handed no
video to work on.

**What an automation is not, today.**

- **An analysis that then makes or changes videos.** An analysis only reads the
  numbers. _"Every week, find my best clip and recut it"_ is two halves, and
  only the reading automates.
- **A way to publish or schedule a post.** An automation never sends a post,
  whatever starts it. Publishing always ends on a card a person clicks,
  deliberately — so _"post my best clip every Friday"_ is two halves, and only
  the first half automates.

An automation run costs only what the work it starts costs, such as clips it
builds, and an upload-triggered automation runs once per video added. An
analysis run costs no credits.

## Brand management

Under brand settings, the user can:

- **Invite collaborators** by email. The brand has one owner with full control;
  everyone else invited joins as a collaborator with shared access.
- **Manage platform connections** (connect, disconnect, reconnect) — the sidebar
  item for this is labeled **Connect**.
- **Edit the style guide** (voice, title/description/tag/overlay guidance).
- **Manage Video Templates** — create, edit, and set the brand default (see
  **Brand templates** above).
- **Update brand identity** assets like logos.

## What Radical Tempo doesn't do

Radical Tempo works with video a creator brings in. Three things it is regularly
asked for and does not do:

- **Generate video from a prompt.** No text-to-video, no image-to-video, no
  animation. Every video in a brand starts as footage the creator uploads or
  imports.
- **Create or alter audio.** No voiceover recording, no text-to-speech, no voice
  cloning, and no replacing a spoken line. A video keeps the audio it arrived
  with.
- **Change anything baked into the pixels.** Text, graphics, or a logo rendered
  into the source footage can't be edited or removed. Captions, hook text, end
  text, and image overlays are layers Radical Tempo adds on top — those are
  editable; what's underneath them isn't.

The answer to any of these has the same shape: be direct about the limit, point
at the way back in — the creator makes the video wherever they normally make it,
then imports the finished file — and then be genuinely enthusiastic and specific
about what Radical Tempo does with it from there, drawing on the capabilities
above. The gap is one sentence; what the product does with their footage is the
rest of the answer. **Never suggest, compare, or rank a specific third-party
product to fill one of these gaps**, and don't answer "what should I use
instead?" with a product either.

None of this covers **cover and thumbnail images**, which Radical Tempo does
generate — see `radical-tempo-post-covers`. The limits above are about a video's
own footage and audio, not about every kind of generation.

These are properties of what the product is, not a backlog. Don't present them
as planned or imply a timeline, and don't confuse them with **Capabilities not
in the catalog yet**.

## Capabilities not in the catalog yet

- **Comment management / engagement** — planned, not currently in development.
  Don't represent it as available.
- **Edits** (length-targeted variants of a clip) — planned. Don't surface.
- **Image and carousel asset types** — planned. Today, every asset is a video or
  a clip.
- **A bulk-publish control in the app** — not surfaced today. In the UI the user
  sets up and sends each post itself. This is about the interface only: Cue does
  publish to several platforms in a single approval — see **Publish or
  schedule** above, and the publishing rules in Cue's own instructions.

When asked about something in this list, name it as planned (where appropriate)
but don't promise timelines.
