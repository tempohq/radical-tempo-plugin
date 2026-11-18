---
name: strategy-talks-to-shorts-and-tiktok
description: >-
  The campaign strategy for a brand whose videos are short talks that arrive on
  a cadence, e.g. 5-minute conference or event talks: every week, cut one idea
  per talk into a clip and post it to YouTube Shorts and TikTok, graded on each
  clip's views on each platform's feed against the brand's own earlier posts.
  Says when to recommend it, which videos to cut from, the weekly rhythm, the
  checks a clip may not fail, the first batch's experiments as bets, and the
  defaults with their evidence. Load when a campaign runs this strategy, when
  you choose a strategy for a brand of talks, or when the user asks how their
  talks campaign is graded. Triggers: strategy, campaign, talks, talk clips,
  conference talks, Ignite, speaker, lecture, keynote, Shorts and TikTok, weekly
  clips, every week, cadence, views per clip, first batch, bet, experiment,
  which strategy.
---

# Strategy: talks to Shorts and TikTok weekly

Each talk becomes clips that stand alone on a short-video feed. A clip succeeds
when the feed shows it to more people than the brand's earlier clips reached.

- Run a campaign on this strategy with the `campaign-manager` skill.
  `plan_batch` applies this strategy's rhythm, measure, and default bets for
  you.

## When to recommend it

- Recommend it when most of the brand's videos are talks or segments of 3 to 15
  minutes, each with one speaker and one idea, and new ones arrive every week or
  two.
- Recommend another strategy when the brand posts one long episode a week to
  grow (`strategy-long-form-promo-clips`), or when few new videos arrive and the
  library is large (`strategy-back-catalog-gems`).

Prerequisites:

- YouTube or TikTok connected, with publishing allowed. Both is the design
  center.
- At least 3 videos in the library.

## Source

- Cut from the newest talks first. Each talk once, until every new talk has
  clips.
- Use a talk's clip-idea cards from `get_clip_ideas` as leads, then shelf clips
  from `list_shelf`, before you make new clips.
- At most 2 clips from one talk in one week. One clip posted to both platforms
  counts once.

## Outputs

| Platform | Output                                       |
| -------- | -------------------------------------------- |
| YouTube  | A Short: vertical, 45 to 75 seconds          |
| TikTok   | The same clip as a TikTok video, own caption |

- One clip carries one idea of the talk, from its setup to its payoff.
- Name the speaker in every post.

## How a clip is graded

- The measure on each platform: the clip's views over the median views of the
  brand's earlier short posts on that platform at the same age.
- The read times: 72 hours and 7 days after the post goes out.
- The campaign's clips may not drop on the `hooked` stage or on the `held` stage
  while they win on views.
- Quote each check from the campaign's `checks` in `get_scorecard`, or in
  `plan_batch`'s `dossier.scorecard`: one per stage and platform, graded against
  the brand's other posts of the last 12 months. `dropped` is a failed check.
  `not_dropped` is a passed one. Never grade a check from the numbers yourself.
- A check with `outcome: not_yet_clear`, or with no `outcome`, was not read.
  Report it as not read, with its `reason` or with "no post on this platform has
  a 7-day reading yet". Never report it as passed.
- A clip with more views and a failed check did not work. Report it as mixed,
  never as a win.

## Rhythm and mixing

- 3 posts per platform per week.
- At least 20 hours between 2 of the campaign's posts on one platform.
- Read 14 days of calendar ahead before you place a post.
- One experiment per batch. Every other post carries a default bet.
- Mix speakers: never 2 clips of one speaker on consecutive days on one
  platform.

## First-batch bets

Give each post one bet. Pick from these until the readout or the playbook names
a better one.

| Bet              | Change                                                    | Stage    | Direction | Comparison    | Platforms       |
| ---------------- | --------------------------------------------------------- | -------- | --------- | ------------- | --------------- |
| `open-mid-claim` | Open on the speaker's claim mid-sentence, not on an intro | `hooked` | up        | earlier posts | YouTube, TikTok |
| `question-title` | Title the post as the question the talk answers           | `shown`  | up        | earlier posts | YouTube         |
| `end-on-payoff`  | End on the payoff line, with no ask to the viewer         | `held`   | up        | earlier posts | YouTube, TikTok |

- `open-mid-claim` is the default for batch 1. A talk's first seconds are an
  introduction, and a slide talk shows the speaker's face only briefly, so an
  opening on the face does not fit.
- Run `question-title` as the batch's one experiment when the readout has no
  measured title level yet.

## Defaults and their evidence

- Clip length 45 to 75 seconds. Ignite's first weekly run (4 Nov 2026) cut 53 to
  57 seconds per talk clip, and every post passed review.
- Post at 14:00 in the brand's time zone. That run kept the brand's existing
  daily slot. Use the playbook's slot once the weekly check measures a better
  hour.
- Keep the user's title pattern from brand memory over every default here.
