---
name: radical-tempo-product-atlas
domain: product
description: >-
  Always-on index of Radical Tempo product knowledge. Read this first on every
  invocation: it says what Radical Tempo is, who uses it, and which skill to
  load for any user request. Routes to the product-knowledge skills (ontology,
  feature catalog, content lifecycle, platform support, user workflows,
  glossary, getting started) and to the action skills, including the
  `channel-manager` weekly tick and the `campaign-manager` batch. Triggers: what
  is Radical Tempo, which skill, where do I start, what can it do, product
  knowledge, index.
---

# Product Atlas

| Owners       | Last reviewed |
| ------------ | ------------- |
| product, eng | 2026-11-03    |

## What Radical Tempo is

Radical Tempo is an AI content operations platform that turns long-form video
into platform-optimized shorts, publishes them everywhere, and uses the
performance data to learn what works — so every clip gets smarter than the last.

Marketing leads with: _"Your personal AI social media studio. End-to-end
optimization: clip creation, editing, publishing, analytics and data science."_

## Who uses it

Social media managers running channels themselves (creators) or on behalf of
content owners — agencies and media companies with multiple channels. Real
customer mix spans solo creators, agencies, media companies, and studios.

## What to remember about Radical Tempo

- **It compounds.** The more platforms a customer connects and the more they
  publish through Radical Tempo, the more it learns about their audience and
  content. Each clip is meant to outperform the last. The weekly check measures
  what held on the brand's own posts, and the playbook is what Radical Tempo
  applies from it; `channel-manager` is the runbook that plans a week from both.
- **It is end-to-end, starting from footage the creator brings.** Import →
  analyze → generate clips → edit → reframe → publish/schedule → analyze
  performance — all inside the same product. Import is the entry point, not a
  step Radical Tempo can skip: the footage and its audio come from the creator.
  Radical Tempo writes copy and generates cover images on top of a video; it
  never generates the video or the voices in it.
- **Brand-scoped, not user-scoped.** A user works inside a "brand" (a
  workspace), and brands typically span multiple connected platforms. Asking the
  user "which brand?" is normal; asking "which workspace?" is not.
- **Clips are cut from videos, never from clips.** Every clip names the one
  video it came from — its **source video**. "That source," "the parent video,"
  "the original," "the full video," and "the long one" all name that video,
  never the clip. When a request would make new clips and a clip is what the
  conversation is pointing at, resolve to its source video first and say which
  video you used. `radical-tempo-user-workflows` carries the lookup.

## When to load each leaf

Start every conversation with this atlas only. Load a leaf when the user's
intent matches. The depth budget is five leaves per task run; if more would
help, decompose the task instead.

| Leaf                              | Load when the user…                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `radical-tempo-product-ontology`  | …asks what something is, why two things are distinct, or how Radical Tempo "thinks about" their content. Examples: _"what's a clip vs a highlight?"_, _"what's the difference between a brand and a channel?"_, _"what does Radical Tempo know about my videos?"_                                                                                                                                                                                                                                           |
| `radical-tempo-feature-catalog`   | …asks what they can do, what's possible, or whether a capability exists — including one Radical Tempo does not have — **and anything about automating a step or having it run on its own**. Examples: _"can Radical Tempo generate captions?"_, _"can I schedule posts?"_, _"what are all the things I can do here?"_, _"what can you automate for me?"_, _"can this run by itself?"_, _"send me a weekly report"_, _"make me a video of…"_, _"how do I add a voiceover?"_, _"change the text in my intro"_ |
| `radical-tempo-content-lifecycle` | …asks what stage their content is in, what happens next, or how long something will take. Examples: _"why is my video still processing?"_, _"what does Radical Tempo do after I upload?"_, _"what happens when I publish?"_                                                                                                                                                                                                                                                                                 |
| `radical-tempo-platform-support`  | …asks which platforms are supported, what differs between platforms, or how a connection works. Examples: _"can I publish to X?"_, _"is Snapchat supported?"_, _"what's the format for Instagram Reels?"_                                                                                                                                                                                                                                                                                                   |
| `radical-tempo-user-workflows`    | …asks how to accomplish a multi-step goal end-to-end, **or** tells you to act on content already in the conversation. Examples: _"how do I take a long video and turn it into shorts on every platform?"_, _"how should I review before posting?"_, _"how do I set up brand templates?"_, _"make more clips from that video"_, _"more like this one"_, _"do more from that source"_                                                                                                                         |
| `radical-tempo-glossary`          | …uses a term ambiguously, asks for a definition, or seems to be confusing two concepts. Examples: _"what's the difference between a clip and an edit?"_, _"is 'highlight' the same as 'clip'?"_, _"what is hook text?"_ Often loaded alongside `radical-tempo-product-ontology`.                                                                                                                                                                                                                            |
| `radical-tempo-getting-started`   | …is new, asks "what should I do first?", or has just signed up. Examples: _"I just signed up — where do I start?"_, _"what's the fastest way to publish my first clip?"_                                                                                                                                                                                                                                                                                                                                    |

## Action skills

Separate from the product-knowledge leaves above: these are prescriptive
how-to-do-it skills, not user-education. Load one as soon as the user's request
matches — _before_ reaching for the obvious tool — because each one carries the
full multi-step loop (which tools, in which order, with which inputs) that a
single tool's description can't.

| Skill                                  | Load when the user…                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `channel-manager`                      | …asks what to post this week, to plan or run their week, to manage their channel, what to do next after connecting, what the manager planned and why, or whether last week's experiment or claim held. Examples: _"what should I post this week?"_, _"plan my week"_, _"I just connected, now what?"_, _"did last week's claim hold?"_, _"schedule this every week"_. It runs the weekly tick: read the weekly check and the playbook, plan, make, review, propose, measure. |
| `campaign-manager`                     | …asks to set up, start, or run a campaign, for their next batch, or how a campaign's bets and claims did, or the brand has an active campaign. Examples: _"set up a campaign"_, _"run my campaign"_, _"plan my batch"_, _"did last batch's bets hold?"_. It picks a strategy, creates the campaign, and runs each batch: draw it with `plan_batch`, brief, make, review, propose, measure.                                                                                   |
| `channel-review-rubric`                | …asks you to review clips, titles, a batch, or a brief before it goes out, or when `channel-manager` or `campaign-manager` reaches its review step. Examples: _"is this batch ready?"_, _"check these titles before I publish"_.                                                                                                                                                                                                                                             |
| `radical-tempo-brand-language`         | …asks for a brand's titles, descriptions, or on-screen text in another language, or asks to change, clear, or check that setting. Examples: _"put my titles in Spanish"_, _"what languages can you write in?"_.                                                                                                                                                                                                                                                              |
| `radical-tempo-brand-logo`             | …asks to set, change, or replace a brand's logo using an image they supply — attached, linked, or already in the brand. Examples: _"use this as my logo"_, _"replace the brand logo with this PNG"_.                                                                                                                                                                                                                                                                         |
| `radical-tempo-delete-transcript`      | …asks to delete or remove a transcript, a translation, or a language from a video. Examples: _"delete the Spanish transcript"_, _"I don't need that translation anymore"_.                                                                                                                                                                                                                                                                                                   |
| `radical-tempo-image-layer`            | …asks to put an image on top of a video or on a brand template — a logo, watermark, badge, or graphic. Examples: _"add this logo to the corner of the clip"_, _"put our watermark on all my videos"_. Only if your tool list has `propose_video_edit`. Without it, tell the user to add the image in the app's Template editor.                                                                                                                                              |
| `radical-tempo-post-covers`            | …asks to create, generate, change, redo, or **update/edit** a cover or thumbnail for a post. Examples: _"make me a YouTube thumbnail"_, _"update this cover with clickbait text and arrows"_, _"try a punchier thumbnail"_.                                                                                                                                                                                                                                                  |
| `radical-tempo-transcript-translation` | …asks for a video, its transcript, or its captions in another language, asks for captions in 2 or 3 languages at once, or asks which languages are available. Examples: _"translate this into Spanish"_, _"can I get a French transcript?"_, _"show English and Spanish captions together"_.                                                                                                                                                                                 |
| `radical-tempo-video-analysis`         | …asks you to evaluate, score, or critique a video's quality (pacing, opening frame, headline, format, duration, edit quality).                                                                                                                                                                                                                                                                                                                                               |
| `radical-tempo-youtube-studio-import`  | …asks to import videos from their YouTube channel, or to import or refresh YouTube Studio metrics. Examples: _"import my latest 25 videos"_, _"bring in my back catalog"_, _"refresh my YouTube metrics"_. It runs the checks before any download, one browser batch per video, the upload, and the recovery steps.                                                                                                                                                          |

## What the KB does not cover

The KB is user education. It does not cover:

- **Live data** — analytics, asset status, post state, account info. Use tools,
  not the KB. Never answer _"how did my last post perform?"_ or _"what's the
  status of asset X?"_ from the KB alone.
- **Internal mechanics** — proprietary scoring, prompts, evaluation rubrics,
  pipeline internals. Stays in tools and Python backend, never surfaced.
- **Other brands' content or data.** Workspace isolation is enforced by RLS; do
  not narrate around it.

For doing things rather than explaining them — generating a cover, evaluating a
video, planning the week — load the matching entry from "Action skills" above.
