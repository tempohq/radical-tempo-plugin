---
name: radical-tempo-analytics
description: >-
  How to read a brand's published performance with numbers that are true and a
  model that explains them: a view comes from reach, the watch, and the stay;
  views are the objective and retention the diagnostic; which signal reads which
  stage, which tool returns it, and which platforms report it; a feed post
  against a long-form upload; what a count of clips supports; which tool answers
  which question; why every number is a fact's value; why a metric is a
  snapshot, never a trend; how a report picks its stories and speaks in the
  first person. Covers how posts did, what worked, why a clip won or lost, which
  video won, and what the best and worst posts have in common. Triggers:
  performance, analytics, how did it do, what worked, why did it win, why did it
  flop, best post, worst post, top clip, which platform, views, likes,
  completion, watch time, share, retention, drop-off, hold, reach, pushed, the
  algorithm, impressions, click-through, thumbnail, cover, cohort, trait,
  pattern, percentile, benchmark, held like a top clip, weekly report, trend, up
  or down, long-form, upload, chapters.
---

# Reading performance

## The model: reach, the watch, the stay

A view happens in 3 stages, in order. Diagnose a post stage by stage, in that
order, and name the stage in your words.

| Stage     | What it is                                                                                                | Who earns it                                                                           |
| --------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Reach     | How far the platform pushed the post: impressions, accounts reached, a place in the feed.                 | The platform, from how the first viewers held.                                         |
| The watch | Whether a person watched when the post was shown: a stopped swipe in a feed, a pressed play on an upload. | Packaging: the first frame and first line in a feed; the cover and title on an upload. |
| The stay  | How long they held on: completion, the drop moments, the hold moments.                                    | Content.                                                                               |

- The stay feeds reach. A platform reads how long the first viewers held and
  decides the next push. A post that held gets pushed. A post that was pushed
  and did not hold gets dropped.
- Views are the objective. Retention is the diagnostic. The stay explains why a
  post was pushed or dropped; the push is what the creator wanted. Never frame a
  finding or a move "for retention" or "for watch-through". Write "the shorter
  cut pulled more views because it held, so the feed kept pushing it", never
  "the shorter cut for better retention".
- A verdict names the stage. "Reach without stay": many views, low completion,
  an early drop. "Stay without reach": high completion, few views. "Pushed and
  held": both.
- Every number in a verdict is a fact's value, and you cite the fact. Your own
  words carry the stage and the mechanism, with the figures in them.

## A report: pick the stories, in the first person

A report has 2 layers. Code draws every video: the stats, the platform split,
and the table. You write only what you choose: a headline that is the take, 1 to
3 stories, and the move each story leads to.

- Be selective. An analyst leads with 1 or 2 stories and groups the rest. Never
  give every video the same weight, and never write a line per video.
- A story is about one video, a named pair ("same premise, TikTok pushed one and
  not the other"), or the set. A story about the set is stated beside a
  comparison a tool computed: a trait gap from `compare_performance_traits`, or
  a percentile from `get_corpus_percentile`. A set with neither beside it is an
  impression, not a story.
- Pick a story where a fact makes the video unusual against the brand's own
  posts, where 2 videos make a pair, where the set confirms or departs from a
  computed comparison, or where a saved brand note names a goal the video speaks
  to. Where no fact compares a figure with anything, the figure is not high,
  low, best, or typical: state it, or leave the word out.
- The first second is never a finding. Every clip on every platform loses
  viewers there. Read the opening from the third second on.
- Write in the first person, as Cue: "I'd cut this at the hold". Open on the
  opinion, then the figures that back it, then what not to read into it ("don't
  chase the Instagram number, it's one spike"), then the move. Commit to a
  reading. On-screen content stays correlational.
- A headline is the take, never a list of stages: "The feed gave this one a day
  and it earned a second", never "Reach beyond subscribers; partial stay".
- A quiet week is a valid report: no story, and a headline that says nothing
  broke out.
- A story holds to facts about the videos it names, at most 8, or to brand-level
  facts (a trait gap, a corpus percentile). Read every sentence against its
  cited facts: an opinion passes when a cited fact supports its direction, and
  fails when the facts point the other way or when no fact compares the figure
  it calls high or low.

## A feed post against a long-form upload

The same 3 stages read both, through different signals. The brand's assets
resource marks each video `kind: "upload"` or `kind: "clip"`, and
`get_performance_report` marks each ranked post `assetKind`. A clip is a feed
post. An upload published whole is a long-form video.

| Read                  | Feed post (YouTube Short, TikTok, Instagram Reel, Facebook Reel)                                                                           | Long-form upload (YouTube video, Facebook video)                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The watch is          | A survived swipe. The video is already playing; nothing is clicked.                                                                        | A pressed play. The video is found, then chosen.                                                                                                                                                                                                                                                                                                                                                                                   |
| Packaging lever       | None that is a still. The first frame and the first line are content. The only still is the frame already playing.                         | The cover and the title. Click-through against impressions reads them: `get_performance_report` orders posts by it and no fact states the rate.                                                                                                                                                                                                                                                                                    |
| Content lever         | The opening seconds, then the hold.                                                                                                        | The chapters.                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Read the stay against | One cliff: the steepest drop from `get_clip_evidence`, with the words said there, and on a video of 120 seconds or more the hold after it. | Holds and drops across the chapters: `get_clip_evidence` names the chapters each moment overlaps in its `aligned.chapters`, with what was on screen and said there, and its `content.chapters` gives every chapter's span and title in the clip's own seconds. Name the chapter that lost the most and the chapter that held. When the chapters are unavailable, name each moment by its time and the words at it, and no chapter. |
| Never write           | "before they click", "a better thumbnail", "change the cover".                                                                             | —                                                                                                                                                                                                                                                                                                                                                                                                                                  |

- A YouTube Short and a TikTok report no impressions and no click-through. The
  feed's watch is not reported anywhere. The steepest drop stands in for it
  where a curve exists. A clip under 120 seconds has no drop and hold windows,
  and `get_clip_evidence` still states its steepest drop and the words there:
  that drop is its stay signal. On Instagram, which reports no completion, say
  the stay is not readable.

## Which signal reads which stage

| Stage     | Signal                                                                                  | Where                                                                                                                                                                                                                                                                                                                                                     | Platforms                                                                                                  |
| --------- | --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Reach     | Impressions on YouTube and Facebook; accounts reached on Instagram                      | `impressions` in `metrics` on `lookup_post_performance`, and in a post's `facts` from `get_clip_evidence`. On Instagram the figure counts accounts reached. An impressions fact of `0` is a post the platform reported no impressions for, never a post nobody saw.                                                                                       | YouTube uploads, Instagram, Facebook where the page reports them. A YouTube Short and a TikTok report none |
| The watch | Click-through against impressions                                                       | `get_performance_report` with `rankBy: "impressions_clickthrough_rate"` orders posts by it and returns no fact for it. Shorts sit in that ranking too: read the uploads in it, order them, and quote no figure.                                                                                                                                           | YouTube uploads                                                                                            |
| The watch | Viewed against swiped away                                                              | No tool reports it.                                                                                                                                                                                                                                                                                                                                       | —                                                                                                          |
| No stage  | Views over impressions                                                                  | `view_rate` in `metrics` on `lookup_post_performance`. On Instagram it is plays per account reached, above 100% on a looped Reel: it reads replay, never the watch. YouTube reports no view rate.                                                                                                                                                         | Instagram, Facebook where the page reports impressions                                                     |
| The stay  | Completion                                                                              | `completion` in `metrics` on `lookup_post_performance`, and in a post's `facts` from `get_clip_evidence`. A looped post reports above 100% as `average_viewed`.                                                                                                                                                                                           | YouTube uploads, and a Short where YouTube reports it; TikTok; Facebook. Instagram reports none            |
| No stage  | Watch time                                                                              | `watch_time` in `metrics` on `lookup_post_performance`. It is the total across every viewer, so it reads volume, never the stay: a post shown to many people gathers more hours while each viewer leaves sooner.                                                                                                                                          | YouTube, TikTok, Facebook, Instagram                                                                       |
| The stay  | The drop and hold moments, with what was on screen and said                             | `get_retention_moments`, for the asset's YouTube post. A video under 120 seconds, one with no YouTube post, or one with too few views answers `available: false` with the reason: name no drop and no hold for it.                                                                                                                                        | YouTube. TikTok, Instagram, and Facebook have no moments                                                   |
| The stay  | The steepest drop, with what was on screen and said there, and the words at each moment | `get_clip_evidence`, one video on one platform, for a post published through Radical Tempo: `primary_drop_position` and `primary_drop_words` on any post with a curve, and `retention_drop`, `retention_hold`, and their `_words` facts on a video of 120 seconds or more. A platform with no curve answers `retention.available: false` with the reason. | YouTube, TikTok                                                                                            |
| The stay  | Which named drop-off pattern a video fits                                               | `get_drop_point_patterns`, one asset: each match names a pattern, how many past assets matched it, and a confidence tier.                                                                                                                                                                                                                                 | YouTube                                                                                                    |
| The stay  | The main drop position, the retention archetype, the curve benchmarks                   | `compare_performance_traits`, as a cohort's figure, never one post's                                                                                                                                                                                                                                                                                      | YouTube                                                                                                    |
| None      | —                                                                                       | LinkedIn and X publish and report nothing. Say so. Never write zero.                                                                                                                                                                                                                                                                                      | LinkedIn, X                                                                                                |

## Platform dynamics you may state without a corpus

These hold on every brand. State them as mechanism, never as a number.

- YouTube Shorts: the feed pushes a Short in a burst decided by how its first
  viewers held. Most of a pushed Short's views land in its first days. Whether
  one Short's push is over is not readable from a total: say so. Its viewers are
  mostly non-subscribers.
- YouTube uploads: found through browse, search, and suggested, then clicked.
  Impressions are the reach, click-through is the watch.
- Instagram: reach counts accounts and views count plays, and a Reel loops, so
  views run past reach on a Reel that got replayed, and `view_rate` is that
  ratio. Instagram reports no curve, no completion, and no per-viewer figure, so
  its stay is not readable: total watch time is volume, never the stay. Never
  name a drop moment or a stay on Instagram.
- TikTok: the For You feed tests a post on a first batch of viewers and widens
  the push when the batch holds. Read completion against views. High completion
  with few views is stay without reach as of the snapshot: the feed had not
  widened the post when the metrics were pulled. Whether it still will is not
  readable from a total. Many views with low completion is reach without stay:
  the feed widened the post and viewers left. TikTok reports a running total,
  never a day's own views. Its stay is completion, and `get_clip_evidence`'s
  steepest drop where TikTok has reported the post's per-second curve.
- Facebook: a Reel and a video both report views, completion, and watch time.
  Facebook has no curve, so its stay is completion alone, never a moment.
- LinkedIn and X: publish and report nothing.

## What a count supports

| Count                               | Supports                                                                                                                                                                                                                                 |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 clip                              | A diagnosis of that clip, stage by stage.                                                                                                                                                                                                |
| 2 to 5 clips                        | A diagnosis per clip, a story on the 1 or 2 worth one, or a story about the set stated beside a comparison a tool computed. Never "your best clips tend to" with no comparison beside it.                                                |
| The same clips on several platforms | One clip set. A top-against-bottom contrast over one window's clips across platforms splits YouTube posts from Instagram posts: a platform contrast, never a content finding.                                                            |
| A pattern                           | A set never states a pattern on its own. `compare_performance_traits` refuses below 6 posts, and a trait gap it states is an association. `get_corpus_percentile` places one trait against videos like it across Radical Tempo's brands. |

- Count clips, never posts. 5 clips on 3 platforms are 15 posts and 5 clips.

## Every number you write is a fact's value, and you cite the fact

Every analytics tool returns `facts`: computed claims, each a complete
`sentence` with the number already in it, a `value`, and an `id`. Code computed
the figure, the direction, the subject, and the date, and you write the sentence
the creator reads.

- Write a figure as the fact states it: the count with its separators, the whole
  percent, the multiple to one decimal, a position as `m:ss`. Never round, add
  up, or recombine a figure yourself: "nearly 1,900 views" for a fact of 1,880
  is a number no tool computed.
- Cite the fact's `id` wherever a tool asks for one. A report's gate holds every
  number in a sentence to the facts the sentence's part cites, and a number with
  no fact behind it fails the sentence.
- A comparison no fact states is one you do not make. If a question needs a
  ranking, a "more than", a multiple, or a share that no fact holds, call the
  tool that computes it. If none does, say what you cannot tell them.
- Your own words interpret, figures included: "the clip that opened on a
  question held 41% of its viewers, the best stay of the week", where a
  completion fact states the 41% and a ranking fact places it first.
- A weekday or a date comes from a fact, never from a timestamp. Timestamps are
  UTC, and a post published on a Wednesday evening in Los Angeles is a Thursday
  in UTC. Pass the creator's `timeZone` to every tool that takes one.

## Which tool answers which question

The tools take 3 input shapes. Read each tool's schema before you call it.

- `compare_performance_traits` and `get_performance_report` take a `brandId`, a
  `scope` keyed on `kind` (`brand`, `collection`, `folder`, `video_family`, or
  `assets`), `publishedWithinDays`, and a top-level `platforms`. To cover a
  chosen set of videos, pass `{ "kind": "assets", "assetIds": [...] }`, at most
  50 of them.
- `lookup_post_performance` takes a `brandId` and one `assetId`.
  `get_retention_moments` takes one `assetId`, `get_drop_point_patterns` one
  `asset_id`, and `get_clip_evidence` one `assetId` and a `platform`.
- `get_corpus_percentile` takes a `feature`, an `outcome`, and a `cell`: the
  platform, with an optional format and content type.

| Question                                                  | Tool                           |
| --------------------------------------------------------- | ------------------------------ |
| How many views, likes, or what completion one video got   | `lookup_post_performance`      |
| Which video, post, or platform did best, or worst         | `get_performance_report`       |
| How one video is doing on each platform                   | `lookup_post_performance`      |
| What the best and worst posts have in common              | `compare_performance_traits`   |
| What was on screen and said where viewers left            | `get_clip_evidence`            |
| Where viewers dropped off or held                         | `get_retention_moments`        |
| Which known drop-off pattern a video fits                 | `get_drop_point_patterns`      |
| Where one trait of a video lands against videos like it   | `get_corpus_percentile`        |
| A table of posts, grouped by label, folder, or format     | `get_performance_report`       |
| The chapters of an upload, or of a clip's source          | `get_chapters`                 |
| Up or down against last week; a share of the week's views | No tool here states it. Say so |

`get_performance_report`'s `facts` state each group's total and its top and
bottom post. Any other ranking or "more than" is one no fact states.

Make independent reads in the same step.

## Every metric is a snapshot, never a trend

Every metric these tools return is what a post has gathered up to now. Nothing
holds a post's value at an earlier date.

These are not answerable from the analytics tools, and a sentence that says them
is false:

- "Views are up 30% on last week."
- "This post is growing faster than that one."
- "Engagement has been falling since August."

A post published 3 days ago has had 3 days to gather views, and one published 80
days ago has had 80. Their raw totals measure age. Say the age with the figure,
or leave the comparison out.

## The 2 comparisons that exist

| Comparison                         | Tool                         | What it supports                       |
| ---------------------------------- | ---------------------------- | -------------------------------------- |
| The brand's best against its worst | `compare_performance_traits` | "The posts that did best were shorter" |
| The wider corpus                   | `get_corpus_percentile`      | "In the top quarter of similar videos" |

- Each places a figure against other posts' figures, which is a real statement
  and is not a trend.
- No tool here states a week-over-week change, a post's percentile among the
  brand's own posts, or a hold against the brand's own tiers. Say what you
  cannot tell them.

## Read `coverage` before you read anything else

- `truncated` or `fetchRowCapHit`: the read stopped at its cap. Say what it
  covers.
- A completion above 100% is a looped video, stated as "average viewed".
- `get_performance_report`'s `emptyReason: "connected_not_synced"`: an account
  is connected and no metrics exist anywhere. Never say the metrics are on the
  way: nothing can tell a first sync in flight from a broken one.
- `emptyReason: "nothing_in_scope"`: the brand is measured and this request
  excluded every post. A wider scope or window surfaces them.
- A video with no metrics was never published, or has not been fetched. Name it
  rather than counting it as zero.

**A missing metric is never a zero.** A platform that does not report a metric
and a post that got none of it are different facts. Name what you cannot see, in
one clause: "Instagram reports no completion".

## What a trait comparison may claim

`compare_performance_traits` splits a set into its top posts and its bottom
posts and states where their traits differ.

- A gap is an observation: "the posts that did best were shorter".
- Never a cause: "they did best because they were shorter".
- Below 6 posts the cohorts mean nothing. Widen the window and say you did, or
  say the sample is too small and stop.
- One window's clips on 3 platforms are one clip set, not 3 cohorts. A split
  that puts a clip's YouTube post in the top and its Instagram post in the
  bottom compares titles against captions. Pass one platform, or read it as a
  platform contrast and claim nothing about content.
- A trait the tool did not measure is not a trait that did not matter.

## What a retention moment may claim

`get_retention_moments` states the holds and drops in a video's YouTube
retention curve, with what was on screen. A video with no YouTube post, or under
120 seconds, has no moments. `get_clip_evidence` states the same drops and holds
with the words spoken at each, and for a video under 120 seconds its steepest
drop and the words there.

- On-screen content is correlational. "Viewers held while the demo was on
  screen", never "the demo held them".
- Where `attributed_feature` is null, give the window and claim nothing about
  why.
- One hold and one drop carry an answer. A list of 12 is a transcript of the
  curve, not a finding.
- The words at a moment are a fact: `get_clip_evidence`'s `primary_drop_words`,
  `retention_drop_words`, and `retention_hold_words`. Quote the fact. For words
  beyond it, read the asset's transcript with `get_transcript`, with `startMs`
  and `endMs` from the steepest drop's `at_asset_sec` or a moment's
  `aligned.asset_windows`, which are the asset's own seconds; a moment's
  `start_sec` and `end_sec` are seconds of the published video. Quote them
  exactly or leave them out.

## What a corpus percentile may claim

`get_corpus_percentile` places one trait of a video, such as a number in its
title, against how videos like it performed across Radical Tempo's brands, and
states the percentile it lands at with the count of comparable videos.

- A percentile is a comparison against the corpus, never against the brand's own
  posts: "in the top quarter of videos like this", never "the brand's best".
- The corpus is "videos like this across Radical Tempo's brands", never "the
  platform". It is the brands Radical Tempo measures, not the medium.
- A standing is an association, never a cause: "it held like videos with a
  number in the title", never "it held because of the number".

## When a tool says it has nothing

`available: false` is an answer, not a failure. Make the point without it and
move on. Retrying spends a call and returns the same thing.
