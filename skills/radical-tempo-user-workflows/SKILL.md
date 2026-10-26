---
name: radical-tempo-user-workflows
domain: product
description: >
  The canonical day-to-day workflows in Radical Tempo — create clips, publish
  and schedule, optimize and adapt, set up brand templates, customize defaults,
  generate images. Each workflow lays out who initiates it, the step-by-step
  user actions, what Radical Tempo does, and the typical outcome. Load when the
  user asks how to accomplish a multi-step goal end-to-end.
---

# User Workflows

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-09-02    |

The workflows below are how creators get value out of Radical Tempo. Cue should
be able to walk a user through any of them, set expectations for each step, and
diagnose where they are if they're partway through.

## 1. Create clips from a long-form video

**Who initiates:** the user, as soon as a video is uploaded or imported —
processing does not have to be finished first.

**What they want:** turn a long video into a set of polished, platform-fit short
clips.

**Steps:**

1. The user uploads or imports a video. Radical Tempo processes it: audio
   transcription, visual analysis, people and object detection. The user sees a
   **Processing** label until processing completes. (See
   `radical-tempo-content-lifecycle` for typical times.)
2. The user opens the video and triggers **Generate clips** (also called "create
   clips"). This works during Processing as well as once the video is Ready — a
   request made early is queued and runs when processing finishes.
3. The user chooses the **output shape** (aspect ratio — defaults to Full
   Portrait 9:16 for short-form), the **crop shape** (usually matches output;
   can be split-screen or content-aware), and an optional **Director's note** —
   free-text guidance like "focus on this person," "include the segment around
   the 12-minute mark," or "themes around leadership."
4. Radical Tempo finds the best **highlights** in the video. The total number
   depends on video length and how many strong moments are present.
5. Radical Tempo evaluates the highlights, drops the weakest and any that
   overlap, and surfaces the survivors in a **highlight gallery**.
6. Radical Tempo **auto-accepts the top three highlights** as clips and adds
   them to the Content library — the user has something publishable immediately.
7. The user reviews the gallery: accepts more highlights, rejects ones they
   don't want, regenerates with different parameters, or combines multiple
   highlights into a single longer clip (compilation or non-contiguous edit).
8. The user can edit any clip in the **clip editor** — trim, captions, hook
   text, end text, fonts, colors, logo, end card, speed, scene cuts. They can
   also reframe the clip to a different aspect ratio at any time.

**Typical outcome:** a set of polished clips ready to publish or schedule across
the brand's connected platforms.

**Cue's job:** explain what's happening at each step, set expectations (how many
highlights, how long auto-accept takes, what a Director's note does), and
diagnose if the user is stuck. When a video is still processing, start the clip
generation anyway and say the clips will be cut once processing finishes — never
tell the user to wait and come back, and never offer to start it yourself later.

### Which video a clip request means

Clips are cut from long-form footage, so a video needs at least 60 seconds
before Radical Tempo will clip it, a clip is turned away in favor of the video
it came from, and processing can rule a video out on its own. The brand's assets
list settles all three per video as `clipEligibility`: `eligible`, `is_clip`
(this entry is a clip and its source video is still there to clip instead),
`too_short`, `unavailable` (processing ruled it out), or `unknown` (duration not
recorded yet, which is fine to try). Only `eligible` or `unknown` is a
candidate.

**A clip is never the answer to a clip request.** A clip is already a cut of
something longer, so the thing to clip is the video it came from — its **source
video**. Every clip in the brand's assets list carries the `sourceAssetId` of
that video, and reading a single video names the same thing `source_asset_id`.
If that video is a clip too, follow the link again until you reach one that is
not.

`clipEligibility` decides, never `kind`. A clip whose source video has been
deleted reads `eligible` and can be clipped on its own — nothing sits behind it
any more, and its `sourceAssetId` names a video you can no longer read. So on a
`kind` of `clip` with a verdict of `eligible`, clip that entry and do not chase
its source video.

Three ways the request arrives, and what each resolves to:

- **A clip is what the conversation points at** — "more like that," "more from
  that source," "another one from this." Clip the video named by that clip's
  `sourceAssetId`. That video is what they pointed at, so do not ask — name it
  and go.
- **A post is what the conversation points at** — "make more like the one I
  published." A post names the video it published as `asset_id`. Read that
  video; if it is a clip, resolve its `source_asset_id`. Nearly every post is on
  a clip, so expect the second hop.
- **A performance result is what the conversation points at** — "make more like
  my best-performing one." A ranked entry carries `assetId` and `assetKind`, so
  `assetKind: "clip"` already tells you the second hop is needed; the entry does
  not carry the source video's ID, so read the video to get it. For "how did the
  clips from that video do," scope a performance report to the video family,
  which covers the source video and every clip from it and accepts any member of
  the family as its starting point.

Two traps make "just take the newest video" the wrong rule:

- **The newest video is often a clip.** The list is newest-first by creation
  time, and generating clips creates new assets, so a clip made from a video
  sorts above the video it came from. Check `clipEligibility`, and on `is_clip`
  clip that entry's source video rather than the entry.
- **A brand new brand has sample content.** Radical Tempo seeds a video and a
  clip so the product isn't empty; both carry `isSampleContent: true`. Neither
  is something the creator uploaded, and neither is about them, so nothing you
  say about one is about their work. The sample clip is a clip, so the sample
  video is the only one of the two that can be clipped — and it genuinely can
  be. Prefer a video the creator added whenever the brand has one. Reach for the
  sample video only when it is the only thing that fits, and name it as the
  sample when you do.

Match the wording the creator used:

- **"my latest upload"**, "the video I uploaded", "the one I just added" — the
  newest `kind: "upload"` that isn't sample content. A clip never answers this;
  it is something Radical Tempo produced, not something they uploaded.
- **"my latest video"** is looser and can fairly mean a clip — and for a clip
  request, that clip's source video is what gets clipped.
- **"the source video"**, "the parent video", "the original", "the full video",
  "the long one" — the video a clip came from. Never the clip itself.

**Name the video you are clipping, with its length, every time.** A clip request
that quietly lands on the wrong video looks exactly like one that worked.

**When the video they meant can't be clipped, work down this list in order:**

1. **It's a clip.** Clip its source video, say which video that is, and carry
   on. Don't ask: the source video is what "more clips from that" meant, so this
   is not a substitution.
2. **It's an upload with no source of its own** — too short, or ruled out by
   processing. Nothing sits behind it, so this is a real switch and it needs a
   question. Say which video you took them to mean, how long it is, and why it
   won't work; name the newest video that would; and ask whether to use that
   one.
3. **The only eligible video is the sample.** Clip it rather than stopping, and
   say plainly that it is the sample video Radical Tempo seeded the brand with,
   so the clips are a demonstration rather than their content. Offer to run it
   again on their own footage once they upload something.
4. **Nothing in the brand is eligible at all.** Say so plainly and ask them to
   add a longer video.

Never silently clip a different video, and never just report the refusal and
stop.

Good, from a clip: "_Scrabble showdown_ is a 40-second clip from _Game Night,
Ep. 4_ (6 minutes) — cutting the new clips from that video now."

Good, from a short upload: "Your most recent video is _Phone teaser_, and at 51
seconds it's under the 60-second minimum for clip generation. It isn't cut from
anything longer, so there's no source video behind it. _Q3 Founder Interview_
(34 minutes) is the newest one I can clip — want me to use that?"

### How clips are made through an AI tool

- Start from one of 3 things: a note about what to look for, exact moments in
  the video, or clip ideas Radical Tempo already suggested for the brand.
- Choose whether to review first. With a review, Radical Tempo plans the moments
  and shows them before anything is cut; without one, it cuts the clips in the
  same step.
- After a review, build only the clips the user kept.
- Join clips into one video with a compilation, which the user approves before
  it is built.
- Reshape a clip to another aspect ratio for another platform.
- Tighten a clip with a polish, which the user approves before it runs, or with
  an edit when they name the exact thing to cut.

## 2. Publish or schedule a clip

**Who initiates:** the user, once a clip is finalized.

**What they want:** get a clip live on one or more connected platforms, with
platform-fit metadata, either now or at a scheduled time.

**Steps:**

1. From the clip, the user creates a **post** for each target platform.
2. Radical Tempo generates a platform-fit **title**, **description**, and
   **tags** based on the clip's content and the brand's style guide. The user
   can edit any of these.
3. The user picks the publish moment: **publish now**, **schedule** for a
   specific date and time, or **save as draft**.
4. The post moves through Draft → Scheduled (if scheduled) → Publishing →
   Published. (See `radical-tempo-content-lifecycle` for what each state means.)
5. Once Published, performance data starts flowing back into the Performance
   section over the next hours to days.

**Typical outcome:** the clip is live on the chosen platforms, with
platform-tuned metadata, and analytics begin to populate.

**Cue's job:** help the user decide which platforms to publish to (which shapes
load `radical-tempo-platform-support`), suggest scheduling that fits the brand's
history (call a tool — KB doesn't know live history), and translate post state
when the user asks "is it live yet?"

**Publishing and scheduling through Cue — the tool sequence.** Cue never
publishes directly. `propose_publish` shows the user an approval card and they
decide with a click, so nothing is live until they act; never say a post was
published or scheduled from the proposal alone.

- **Publish now:** `propose_publish` with `mode: "publish-now"`.
- **Schedule:** `propose_publish` with `mode: "schedule"`, plus `plainDateTime`
  (the wall-clock time as `YYYY-MM-DDTHH:mm`, no offset or trailing `Z`) and
  `timeZone` (an IANA zone like `America/Los_Angeles`) — both required together.
  The time rides on the proposal and is written only when the user approves it.
- **Unschedule:** `cancel_scheduled_publish` with the post's ID. It runs
  immediately and shows no approval card, because it can only prevent a publish,
  never start one. Read `scheduleCleared` on the result: only `true` means the
  post will not go out.

A post's publish time can be set **only** this way — `update_post` has no field
for it.

A post that is already scheduled can be immediately published, or have its
schedule changed, by using the `propose_publish` tool again. The same workflow
applies, so never cancel a schedule to re-time it. A second `propose_publish`
fails once the post has started publishing.

`propose_publish` cannot clear a publish time, and `cancel_scheduled_publish` is
the only tool that can. Do not use `cancel_task` for it: that stops one
background task by its task ID and leaves the post's publish time set, so the
post still reads as scheduled.

Everything else on a post stays editable while it sits scheduled — title,
description, tags, cover — so "fix the title before it goes out" is an ordinary
`update_post` call.

**Several posts at once.** There is no batch card yet, and that is not a reason
to refuse — one proposal per post covers it. "Schedule my clips one a day at 9am
starting Monday" means working out each post's own time and calling
`propose_publish` for every one of them, in the same turn, so the user gets a
row of cards to approve. Say up front how many you are putting up.

Do not stop at the first card, and do not answer a request covering several
posts with a single proposal — that quietly does a fraction of what was asked
while looking finished. The clunkiness is the card-per-post UX, not a missing
capability, so work with it rather than treating the request as unsupported.

If a required detail is genuinely missing — most often the timezone — ask that
one question instead of guessing. Which posts to schedule is not usually one of
those: the clips already carry posts, so use them.

## 3. Optimize and adapt

**Who initiates:** the user, when reviewing a clip before posting or adapting an
existing clip for a new platform.

**What they want:** improve a clip's chances on a target platform, or take a
clip that worked on one platform and adapt it for another.

**Steps:**

1. Before posting, the user can request a **review** of a clip. (Reasoning lives
   in the prescriptive `radical-tempo-video-analysis` skill — load that for
   actual scoring rubrics.)
2. To adapt for a different platform, the user can **reframe** the clip to that
   platform's preferred aspect ratio, then create a new post targeting that
   platform.
3. The user can also re-edit overlay text (hook, end text), captions styling,
   and metadata to fit the new platform's audience without changing the
   underlying clip.

**Typical outcome:** the same source content reaches more platforms with
appropriate per-platform packaging.

**Cue's job:** if the user wants prescriptive review feedback, load
`radical-tempo-video-analysis`. If they want to know what's _possible_ to
change, this catalog covers it.

## 4. Set up brand templates and the style guide

**Who initiates:** the brand owner or a team member with edit access, typically
once, after the brand has connected its platforms.

**What they want:** capture the brand's voice, visual identity, and platform
preferences so Radical Tempo applies them automatically across clip generation
and metadata.

Two complementary pieces do this: the **style guide** (the brand's _voice and
text_ rules) and a brand **template** (the brand's reusable _visual_ layer for
clips). Set up both.

**Style guide — steps:**

1. **Connect platforms** first. The style guide is generated from previously
   published content on the brand's connected channels — connecting before
   generating produces stronger starting content.
2. Open **Settings → Style Guides**. Radical Tempo presents a draft covering
   Brand Identity (voice, energy, formality, emoji style), Title Guidance,
   Description Guidance, Tag Guidance, and Overlay Guidance.
3. The user reviews and edits each section. The style guide carries "based on
   your content" provenance for AI-generated parts.
4. The user uploads or sets brand-level assets: logos, fonts, colors — these
   become available in the clip editor and in templates.

**Video template — steps:**

1. Open **Settings → Video Templates** and choose **Create**, which opens the
   **Template editor**. To start from a clip's current styling instead, open the
   clip editor's **Design** tab:
   - If the brand has no templates yet, choose **Save as template** at the top
     of the tab.
   - If the brand has templates, choose **Switch → New Template** beside the
     **Templates** heading. **Switch** shows only while a template is on the
     clip.
   - If the brand has templates but none is on the clip, turn on the switch
     beside the **Templates** heading first. It applies a template — the last
     one on the clip in this editor session, else the brand default, else the
     first template — and restyles the clip. Then choose **Switch → New
     Template**. The new template captures the applied template's look, not the
     clip's earlier styling. Tell the creator this before they turn the switch
     on: in this state the clip editor has no way to save that earlier styling
     as a template.
   - **Save as template** and **New Template** both ask how to start. Choose
     **From current video** to carry the clip's styling over. **Blank template**
     leaves the clip's styling behind. Both open the **Template editor**.
2. In the Template editor, configure the on-clip elements — captions styling,
   hook-text styling, end text (including any static call-to-action copy),
   image/logo overlays, and fonts/colors — and **Save Template**. Templates work
   with any output shape; the preview shows them across aspect ratios.
3. Mark one template as the **brand default**. The default is applied
   automatically to new clips; the user can still switch templates or override
   individual elements on any single clip in the clip editor's Templates
   section.

**Typical outcome:** clip generation, metadata generation, and overlay text
suggestions all consistently reflect the brand's voice; and new clips inherit
the brand's visual styling automatically from the default template — with less
per-clip editing needed.

**Cue's job:** explain that the style guide covers the _words_ and a template
covers the _visual styling_, why connecting platforms _first_ matters (better
style-guide draft), and where each piece lives (Settings → Style Guides for
voice/text rules; Settings → Video Templates for the visual template; brand
identity settings for logo/fonts/colors). The brand default template applies on
its own — don't tell users they must pick a template for every clip.

## 5. Customize default settings

**Who initiates:** any user comfortable with their workflow, looking to reduce
repeated decisions.

**What they want:** set sensible defaults for clip generation, publishing, and
editing so they don't pick the same options every time.

**Steps:**

1. Set the brand's preferred default **output shape** and **crop shape** for
   clip generation (e.g., always 9:16 Full Portrait).
2. Set default **hook text** behavior (auto-generate vs. always edit).
3. Configure **scheduling defaults** — preferred publishing times per platform.
4. Save brand-level **fonts, colors, and overlay styles** as a **Video
   Template** and mark it the brand default, so they apply automatically to new
   clips (see workflow 4).
5. Save brand-level **default visibility settings** for YouTube (visibility) and
   TikTok (privacy level and comment/duet/stitch permissions), via "Save as
   brand default" in a post's Settings section or shown under Post Defaults in
   brand settings.

**Typical outcome:** generating and publishing a clip becomes a few clicks
instead of a configuration tour.

**Cue's job:** point users to where each default lives (clip generation modal
for output shape; Settings → Video Templates for the brand's default visual
styling; brand settings for fonts/colors/logo). Don't promise defaults that
don't exist — when unsure, suggest the user check the relevant settings page
rather than fabricate a control. The brand's saved default visibility settings
apply automatically when you update a post whose visibility fields are still
unset — `update_post` has no generic `visibility` field, so pass the relevant
platform-specific field (`youtubePrivacyStatus`, or the TikTok fields
`tiktokPrivacyLevel` / `tiktokDisableComment` / `tiktokDisableDuet` /
`tiktokDisableStitch`) only when the user wants something different, and note
that the manual posting flow intentionally never prefills TikTok visibility (the
user picks it there).

## 6. Generate images with AI

**Who initiates:** the user, usually asking for a new or customized cover for a
video's post ("make me a YouTube thumbnail", "try a punchier cover"),
occasionally a standalone brand image. Today the workflow is mostly about
covers; more is coming — images for brand templates, overlays, icons, and so on.

**What they want:** one or more AI-generated images they can review and, if one
fits, put to use — most often as a post's cover.

**Steps:**

1. The user describes the image. If it should build on an existing image (the
   current cover, a brand image), find it with `search_images` and pass it as a
   labeled reference.
2. Start the generation with the `generate_images` tool. Its `target` says what
   the image is for: `{ kind: "cover", … }` names the video or post and is sized
   to that content's shape, while `{ kind: "standalone", aspectRatio }` is a
   brand image whose shape you choose (e.g. 1:1 for an icon). A cover takes no
   aspect ratio — the content decides it. The tool starts a background task. It
   answers with a task handle, whose `taskId` is the task ID, or with a
   `resource_link`, whose `name` is the task ID.
3. Wait for the task to finish, immediately, in the same turn. In Cue, call
   `awaitTask`. In any other client, decide by what `generate_images` answered:
   - A task handle: poll `tasks/get` at the interval the task reports.
   - A `resource_link`: poll `get_task_status` with its `name` until the task is
     terminal. Do this even when your client can send `tasks/get`, because
     `tasks/get` never hands such a client the task's result.

   Read `isError` on the finished task's `result` before its links. A `result`
   with `isError: true` says why the generation failed and links no image: pass
   that reason on. Any other `result` links the generated images. Unless your
   host tells you otherwise, assume nothing will proactively notify you when a
   task finishes. Never close the turn with a promise to follow up. A reply
   ending "I'll let you know when it's ready" strands the task, because nothing
   will ever prompt you to come back to it. (Cue is the exception: it posts a
   `<job-update>` message into the conversation when a task finishes.)

4. Show the generated image(s) and ask the user for approval or feedback — one
   option per image, single-select, using the client's question tooling
   (`AskUserQuestion` in Claude clients, `presentOptions` in Cue). In clients
   with no such tool (e.g. the ChatGPT desktop app), ask in plain text with a
   numbered list of options instead. The user must _see_ the images before
   choosing; never auto-apply one.
5. If the user wants something different, start over at step 2 with their
   feedback folded into the instruction.
6. If the user approves an image and a tool exists to put it to use, call it —
   today that's `update_post` with `cover: { objectId }` for post covers.
   Otherwise the image is stored with the brand for the user to use manually.

**Typical outcome:** a generated image the user explicitly chose, applied where
it belongs (currently: set as a post's cover) or saved with the brand.

**Cue's job:** drive the loop end to end without making the user restate context
the page already provides (brand, video, platform). For the cover-specific
details — targeting the right post, sizing, the choose-and-set loop — load
`radical-tempo-post-covers`.

## What's _not_ a workflow today

- **Comment management / engagement** — planned, not currently in development.
  If a user asks how to manage comments through Radical Tempo, say it's planned
  and isn't a flow today.
- **A bulk-publish control in the app** — not surfaced as a workflow today; in
  the UI each post is set up and sent on its own. This is about the interface
  only: Cue can publish to several platforms in a single approval, so don't tell
  a user it has to happen one platform at a time.
- **Batch review across a brand's assets** — exists in skill design but not yet
  surfaced to users.

## Cross-cutting tone

Radical Tempo workflows are **assistive, not autonomous** by default. The user
is the editor of record: Radical Tempo proposes (highlights, hook text,
metadata, scheduling), the user accepts or edits. When a user asks "can it just
do it for me?", the honest answer is "we get you 80% there and you decide; the
more you publish, the better the 80% gets."
