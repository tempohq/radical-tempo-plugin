---
name: strategy-long-form-promo-clips
description: >-
  The campaign strategy for a brand that publishes one long-form episode a week,
  e.g. a podcast or a show on YouTube: cut the newest episode into short clips
  for YouTube Shorts, TikTok, and Instagram Reels that send viewers to the
  episode, graded on the episode's views after each clip rather than the clip's
  own views. Says when to recommend it, which videos to cut from, the weekly
  rhythm, the checks a clip may not fail, the first batch's experiments as bets,
  and the defaults with their evidence. Load when a campaign runs this strategy,
  when you choose a strategy for a brand with a weekly episode, or when the user
  asks how promo clips are graded. Triggers: strategy, campaign, long-form, long
  form, episode, weekly episode, podcast, show, full video, promo clips, promote
  the episode, trailer, teaser, drive views to the episode, first batch, bet,
  experiment, which strategy.
---

# Strategy: long-form plus promo clips

The episode is the product. The clips exist to send viewers to it. A clip
succeeds when the episode gains views after the clip goes out, whatever the
clip's own views.

- Run a campaign on this strategy with the `campaign-manager` skill.
  `plan_batch` applies this strategy's rhythm, measure, and default bets for
  you.

## When to recommend it

- Recommend it when the brand publishes a long-form video, 15 minutes or more,
  on YouTube each week, and growing those episodes is the goal.
- Recommend `strategy-talks-to-shorts-and-tiktok` instead when the clips are the
  product and no episode needs viewers.

Prerequisites:

- YouTube connected, with publishing allowed. The episode lives there. TikTok
  and Instagram add reach for the clips.
- At least one long-form video in the library.

## Source

- Cut from the newest episode only.
- Use the episode's clip-idea cards from `get_clip_ideas` as leads.
- At most 3 clips from the episode in one week. Post each clip to every platform
  the campaign runs on; one clip on 3 platforms counts once.

## Outputs

| Platform  | Output                           |
| --------- | -------------------------------- |
| YouTube   | A Short, 30 to 60 seconds        |
| TikTok    | A TikTok video, 30 to 60 seconds |
| Instagram | A Reel, 30 to 60 seconds         |

- Every post names the episode and where to watch it, in the title or the
  caption.

## How a clip is graded

- The measure on each platform: the episode's views in the read window after the
  clip goes out, over its views in a window of the same length before it. The
  clip's own views do not count.
- The read times: 72 hours and 7 days after the post goes out.
- The campaign's clips may not drop on the `hooked` stage while the episode
  gains. A clip that loses viewers in its first seconds sends nobody.
- Quote each check from the campaign's `checks` in `get_scorecard`, or in
  `plan_batch`'s `dossier.scorecard`: one per stage and platform, graded against
  the brand's other posts of the last 12 months. `dropped` is a failed check.
  `not_dropped` is a passed one. Never grade a check from the numbers yourself.
- A check with `outcome: not_yet_clear`, or with no `outcome`, was not read.
  Report it as not read, with its `reason` or with "no post on this platform has
  a 7-day reading yet". Never report it as passed.
- A clip with many views and no gain for the episode did not work. Say so.

## Rhythm and mixing

- 3 posts per platform per week, spread across the days after the episode goes
  out.
- At least 20 hours between 2 of the campaign's posts on one platform.
- Read 7 days of calendar ahead before you place a post.
- One experiment per batch. Every other post carries a default bet.

## First-batch bets

Give each post one bet. Pick from these until the readout or the playbook names
a better one.

| Bet                    | Change                                                                   | Stage   | Direction | Comparison                 | Platforms                  |
| ---------------------- | ------------------------------------------------------------------------ | ------- | --------- | -------------------------- | -------------------------- |
| `open-question-ending` | End the clip on the open question the episode answers, not on the answer | `held`  | up        | another post in this batch | YouTube, TikTok, Instagram |
| `clip-before-episode`  | Post the first clip the day before the episode goes out                  | `shown` | up        | earlier posts              | YouTube                    |

- `open-question-ending` is the default for batch 1. Give it to half of the
  batch's posts and compare them with the other half.
- Run `clip-before-episode` as the batch's one experiment only when the user
  shares the episode's release time before it goes out.

## Defaults and their evidence

- Clip length 30 to 60 seconds. Radical Tempo's starting point for a short: it
  fits every platform's short format. Use the playbook's length slot once the
  weekly check measures the brand.
- Post at 14:00 in the brand's time zone. Radical Tempo's starting point. Use
  the playbook's slot once the weekly check measures a better hour.
- Keep the user's title pattern from brand memory over every default here.
