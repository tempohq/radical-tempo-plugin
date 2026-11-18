---
name: strategy-back-catalog-gems
description: >-
  The campaign strategy for a brand with a large library of older videos and few
  new ones: every week, cut moments nobody has posted yet from videos at least
  90 days old and post them to one platform, graded on each clip's views against
  the brand's own earlier posts on that platform. Novelty and spacing matter:
  never a moment already posted, and a rest between clips of one video. Says
  when to recommend it, which videos to cut from, the weekly rhythm, the checks
  a clip may not fail, the first batch's experiments as bets, and the defaults
  with their evidence. Load when a campaign runs this strategy, when you choose
  a strategy for a brand with a big library, or when the user asks how a library
  campaign is graded. Triggers: strategy, campaign, back catalog, back-catalog,
  library refresh, old videos, archive, evergreen, gems, hidden gems, never
  clipped, repurpose, one platform, first batch, bet, experiment, which
  strategy.
---

# Strategy: back-catalog gems to one platform

The library holds moments the brand never posted. Each batch brings a few of
them to one platform. A clip succeeds when it beats the brand's earlier posts on
that platform, so a clip that only repeats an earlier post never counts.

- Run a campaign on this strategy with the `campaign-manager` skill.
  `plan_batch` applies this strategy's rhythm, measure, and default bets for
  you.

## When to recommend it

- Recommend it when the library holds 10 or more videos, new videos arrive
  rarely, and `get_clip_ideas` reports eligible sources with unused cards.
- Recommend `strategy-talks-to-shorts-and-tiktok` instead when new talks arrive
  every week, and `strategy-long-form-promo-clips` when a weekly episode is the
  thing to grow.

Prerequisites:

- One of YouTube, TikTok, Instagram, or Facebook connected, with publishing
  allowed. The campaign posts to one of them.
- At least 10 videos in the library.

## Source

- Cut only from videos at least 90 days old.
- Rank sources by unused clip-idea cards from `get_clip_ideas`. Take a
  never-clipped video first.
- Never post a moment the brand already posted on any platform. Check the shelf
  and the brand's posts before you cut.
- At most 1 clip from one video in one week.
- Rest a video 28 days after the campaign posts from it.

## Outputs

| Platform  | Output                              |
| --------- | ----------------------------------- |
| YouTube   | A Short: vertical, 30 to 60 seconds |
| TikTok    | A TikTok video, 30 to 60 seconds    |
| Instagram | A Reel, 30 to 60 seconds            |
| Facebook  | A Reel, 30 to 60 seconds            |

- The campaign posts to the one platform the user picked. Ask when they have not
  said.
- A clip has to make sense to a viewer who never saw the original video.

## How a clip is graded

- The measure on the campaign's platform: the clip's views over the median views
  of the brand's earlier short posts on that platform at the same age.
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

- 3 posts per week on the campaign's platform.
- At least 36 hours between 2 of the campaign's posts.
- Read 21 days of calendar ahead before you place a post.
- One experiment per batch. Every other post carries a default bet.
- Mix topics: never 2 clips on one topic in a row.

## First-batch bets

Give each post one bet. Pick from these until the readout or the playbook names
a better one.

| Bet                       | Change                                                                            | Stage    | Direction | Comparison                 | Platforms |
| ------------------------- | --------------------------------------------------------------------------------- | -------- | --------- | -------------------------- | --------- |
| `never-clipped-first`     | Cut from a video the brand has never clipped                                      | `shown`  | up        | earlier posts              | all 4     |
| `standalone-context-line` | Add one on-screen line that makes the clip stand alone without the original video | `hooked` | up        | another post in this batch | all 4     |

- `never-clipped-first` is the default for batch 1.
- Run `standalone-context-line` as the batch's one experiment: give the line to
  half of the batch's posts and compare them with the other half.

## Defaults and their evidence

- Clip length 30 to 60 seconds. Radical Tempo's starting point for a short: it
  fits every platform's short format. Use the playbook's length slot once the
  weekly check measures the brand.
- Post at 14:00 in the brand's time zone. Radical Tempo's starting point. Use
  the playbook's slot once the weekly check measures a better hour.
- Keep the user's title pattern from brand memory over every default here.
