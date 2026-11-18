---
name: campaign-manager
description: >-
  The campaign runbook: set up a campaign for a brand (pick a strategy with
  `recommend_strategies`, then `create_campaign`), then run each weekly batch
  from one `plan_batch` call: write the brief from the slot sheet, swap a
  candidate only with a cited `row_id`, make the clips with the campaign's
  guidance, give every post its slot's bet, review, save the proposal on the
  batch for the user's approval, submit claims, and read each post's standing
  and the campaign's claims on the next batch. Load when the user asks to set
  up, start, or run a campaign, for their next batch, or how a campaign's bets
  and claims did, and for a brand where `get_campaign` with `brandId` lists an
  `active` campaign. Triggers: campaign, run my campaign, set up a campaign,
  start a campaign, plan my batch, next batch, batch, slot sheet, strategy,
  which strategy, bet, experiment, swap, brief, did the bet hold, campaign
  claims, standing campaign, weekly clips, Ignite.
---

# Campaign manager

You run one campaign of one brand, a batch at a time. A campaign is one brand
running one strategy. `plan_batch` reads everything and draws the batch's slot
sheet in code; you write the brief, make the clips, and propose the posts. The
weekly check decides what held, never you. Every publish ends on the user's
approval.

- Resolve the brand with `list_brands` first. Say which brand you picked.
- Pass the brand's ID as `brandId` to every call that takes one.
- When a tool this runbook names is not in your tool list, skip that call, say
  which one, and continue.
- Quote numbers from a tool's answer. Never compute, pool, or round one into a
  new claim.
- Speak to the user in their words: a video, a clip, a post, a campaign, a
  batch, a bet, the weekly check.

## 1. Find the campaign

- Call `get_campaign` with `brandId` and `status: ["active", "paused"]`.
- The user names a `paused` campaign, or asks to start one again: call
  `update_campaign` with its `campaignId` and `status: "active"`, then run its
  batch. A `paused` campaign runs no batch until then.
- No `active` campaign, and the user asks for none of the `paused` ones: run the
  setup (step 2). Name each `paused` campaign first, so the user can start it
  again instead.
- One `active` campaign: run its batch.
- Several `active` campaigns: run the one the user named. Otherwise list each
  `name` with its `strategy` and ask which.
- To run a batch, call `draft_batch` with `brandId` and `campaignId`: the server
  drafts the batch. Follow the task with `get_task_status`, read the proposal
  with `get_batch_proposal`, and follow its `nextSteps` through `approve_batch`.
- Make the batch by hand, from step 3, only when the user asks to make it
  themselves, or when `draft_batch` is not in your tool list.

## 2. Setup

1. Read `list_brands`. With no videos (`assetCount` of `0`) or no entry in
   `publishConnections` with `canPublish: true`, answer from the
   `channel-manager` skill's "What next?" section, and stop. A campaign needs
   both.
2. Call `recommend_strategies` with `brandId`. Pass `goal` and `content` when
   the user already said them.
   - The server asks the user its 2 questions itself where the client supports
     it. Otherwise `questions` lists what to ask: ask in the words given, then
     call again with the answers.
   - `recommendations` empty: tell the user what each entry of `needs` says, and
     create no campaign.
3. Show each recommendation by `title`, with its `reason` quoted as written. Let
   the user pick. Never pick for them.
4. Call `get_strategy` with the picked `strategyId`. Load the skill of the same
   name (`skills://<strategyId>`): it says how posts are graded and which rules
   the batch follows.
5. Agree with the user on each of these before you create anything:
   - the platforms and posts per week (`cadence`): only platforms with
     `canPublish: true`, at most `platformsPerCampaign`, with
     `rhythm.postsPerWeek` as the default;
   - a `name`;
   - the `titlePattern`, from the user's own pattern when they have one;
   - the `guidance`: what to `favor`, what to `avoid`, the `voice`, and what the
     `hook` may claim;
   - the `approvalMode`: `batch` (one yes for the whole batch) or `post`.
6. Call `create_campaign` with `brandId`, `name`, `strategy` (the `strategyId`),
   `strategyVersion` (the strategy's `version`), `cadence`, and each field the
   user agreed.
   - Never retry a failed `create_campaign`. Call `get_campaign` with `brandId`
     first: the campaign can exist already.
7. Run batch 1 (step 3).

## 3. The batch

### Read

1. Load the strategy skill the campaign's `strategy` names.
2. Call `plan_batch` with `campaignId` and the user's `timeZone`.
3. In `dossier`, a section with `available: false` is an answer: quote its
   `unavailable_reason`. Never retry it. The slot sheet draws without it.
4. Tell the user every entry of `slotSheet.refusals` in plain words: a day
   another campaign holds (`slot_taken`), the platform's posting limit
   (`platform_cap`), another campaign's experiment that week
   (`experiment_taken`), a platform the strategy does not post to
   (`platform_not_in_strategy`), or a platform the brand cannot publish to
   (`platform_not_connected`, `platform_cannot_publish`). For the last 2, tell
   the user what `publishBlockedReason` asks them to fix: link an account,
   reconnect one, or pick a destination.
5. Summarize in 5 lines: what the weekly check measured (`readout`), what
   changed (`playbook.whatChanged`), what is ready (`shelf`, `library`), what
   the bets and claims of earlier batches became (step 7), and where the
   campaign stands (`scorecard`). When a platform's `readout.readingCounts`
   entry has `leftOut` above 0, say how many posts were too new to count.
6. Read `dossier.scorecard` the way the strategy skill's "How a clip is graded"
   says: its `rows` hold reach, per clip, and breakouts on the campaign's
   strategy, its `checks` and `measures` grade the strategy, and its `diagnoses`
   name the stage each platform's next experiment should move
   (`proposed_stage`). Progress on a platform is reach, per clip, and breakouts
   rising together.
   - Quote a figure whose `status` is `below_floor` as `n` of `floor` posts,
     never as a value.
   - A reach block whose `coverage` is `not_covered` is unknown, never 0.

### Write the brief

At most 6 lines:

- the slots: per platform, how many posts and which candidates;
- what to favor, each with a cited `row_id`, from where the swap rules say;
- the title pattern: the campaign's `titlePattern`;
- the experiment: the platform whose slots carry `bet.experiment`, its `change`,
  its `stage`, how many slots sit in each arm, and the ISO week whose weekly
  check reads it (`readWeek`, written `<year>-W<week>`), the week after the last
  post goes out;
- each swap, with its `row_id`;
- each spacing judgment.

Rules:

- Keep each slot's candidate and bet unless a swap rule below applies.
- `plan_batch` draws at most one experiment per platform per week, across the
  brand's campaigns, and splits the platform's slots between its 2 arms. Never
  move a slot to the other arm. Never put a slot without `bet.experiment` in an
  arm.
- A `test` post applies the experiment's `change`. A `comparison` post is made
  without it.
- Read `dossier.experiments` for each experiment's `status` and `posts` per arm.
  When `latestRead.stored` is `false`, say no read exists yet.
- Never favor a level whose `verdicts` or `claims` say `contradict` or
  `contradicted`.
- With no readout, cite no row. Say the brief keeps the sheet as drawn.

Test each rule you draw from the brand's past posts before the batch claims it,
e.g. "posts that open on a face hold better":

- Call `test_claim` with the platform, the stage (`shown`, `hooked`, or `held`),
  and the level's `attribute` and `level` as `dossier.readout` lists them.
- Relay its `verdict`. Never grade the numbers yourself.
- `passed`: the batch may claim it.
- `leaning`: never state the rule as proven, and make no claim on it in this
  batch. Propose `proposed_experiment` to the user for the next batch.
- `not_supported`: drop the rule.
- `not_yet_clear`: say why. When `reason` lists the levels the brand's posts
  hold, test again with one of them.
- A rule this batch's experiment tests on new posts needs no `test_claim`.

### Swap rules

- A swap replaces one slot's candidate with an entry of `slotSheet.backlog` or a
  clip of `dossier.shelf.clips`. Nothing else is a swap candidate.
- Every swap cites one `row_id` from `dossier.readout.levels`, under
  `edges[].rowIds`, that argues for the new candidate over the old one.
  `dossier.playbook` carries no `row_id`, so a playbook slot alone never
  justifies a swap.
- A `row_id` that the same edge also lists in `notSameAgeRowIds` compares posts
  of different ages. You may cite it for a swap. Say in the brief's swap line
  that the row compares posts of different ages.
- Without such a `row_id`, the sheet stands. Tell the user which swap you wanted
  and that no measured row supports it.
- The user may ask for a swap themselves. Make it, and record their words as the
  reason in place of a `row_id`.
- Record each swap in the brief: the `slotId`, the candidate replaced, the
  candidate placed, and the `row_id`.
- A slot with `candidate: null` stays empty. Tell the user no video is left that
  the strategy's rules allow.

### Spacing

`plan_batch` draws days and candidates, and leaves speaker and topic spacing to
you. Apply the strategy skill's "Rhythm and mixing" rules, e.g. never 2 clips of
one speaker on consecutive days on one platform.

- Find each candidate's speaker and topic from its `title` and its video's
  title. When they do not say, read the transcript (step 4).
- To fix a clash, exchange the candidates of 2 slots of this batch. That is not
  a swap and needs no `row_id`.
- When no exchange fixes it, keep the sheet and say so.
- Record each exchange in the brief as a spacing judgment.

### The director's note

Write one note per video you cut from: at most 2 lines, from the campaign's
`levers` and the brief's cited rows. Never write the experiment's `change` into
the note. Pass the slots the video fills to the clip tools (step 4) instead:

- When every slot you pass is a `test` slot of one experiment, code puts the
  change on the clip job. A `length` or `speed` change sets the clip length or
  the playback speed to the bet's `changeValue`. An `opening`, `ending`,
  `pauses`, or `other` change goes into the note of a job that plans.
- Any other mix of slots puts nothing from the change on the job.
- The tool's answer says what code put on the job. Report it to the user.

A swap or an exchange stays in the brief, and the stored sheet keeps each slot's
drawn candidate. The clip tools refuse a slot whose stored candidate is another
video, so pass only the slots whose drawn candidate is this video. Treat a
`test` slot whose video you swapped or exchanged in as a `test` clip of a video
in both arms, below.

When a video's slots sit in both arms, the job carries no `change`, so its
`comparison` clips are made without it. Give each of its `test` clips the change
on its own:

- `caption_style`: write the change into that post's text with `update_posts`.
- Any other kind changes the cut: build that clip with `build_clips` and `plan`,
  with segments that make the change and the slot's `change` on the clip.

- Never restate the campaign's `guidance`. `campaignId` on the clip tools
  carries it to the clip maker.

## 4. Make

For each slot, by its `candidate.kind`:

- `shelf_clip`: the clip exists. Nothing to cut.
- `card`: call `build_clips` with `brandId`, the card's `assetId`, the `cardId`s
  of that video's slots as `clipIdeaIds`, and `campaignId`. One call per video.
- `source`: call `generate_clips` with `brandId`, `assetId`, `review: true`, the
  video's director's note as `prompt`, `campaignId`, the batch's `runId` as
  `campaignRunId`, and the `slotId`s of the slots whose drawn candidate is this
  video as `slotIds`. With no such slot, omit `campaignRunId` and `slotIds`.
  When the task finishes, read the clip ideas with `get_clip_ideas` and
  `assetId`, show them with `propose_clip_plan`, and call `build_clips` with the
  `clipIdeaIds` the user kept, `campaignId`, `campaignRunId`, and the same
  `slotIds`. Keep as many as the video's slots, at most the strategy's
  `maxClipsPerSourcePerWeek`.

Follow every task to its end with `get_task_status`.

Then write each post:

- Find each clip's posts with `list_posts`, `phase: "draft"`, matched on
  `assetId` and `platform`. Keep each post's `postId`: every later step names
  the post by it.
- Before you judge a clip's opening, ending, or a cut sentence, read the clip's
  transcript with `get_transcript` and its cut with `get_video_edit`. Only the
  ranges in `trimSections` (milliseconds on the clip's timeline) play: judge
  only the words inside them.
- Call `update_posts` once with `brandId` and `posts`. Give each entry:
  - `postId`;
  - the `title`, `caption`, `description`, and `hookText` the post type takes,
    to the campaign's `titlePattern`;
  - `campaignId`, and the batch's `runId` as `campaignRunId`;
  - `slotId`: the slot the post fills. Leave `bet` out: the post takes the
    slot's bet and its arm.
  - For a slot whose bet has `comparison: "batch_post"`: the other post in the
    batch that differs only in the change, in `comparisonPostIds`.
- Never pass `bet` for a slot's post. A batch post takes its bet and arm from
  its slot: a `bet` that differs from the slot's bet refuses the whole entry,
  with nothing changed, and the refusal names the slot's bet.
- A batch post with no bet and no `slotId` gets no bet: pass its `slotId`.
- Report each entry's `betNote` to the user.
- Set a cover on every post: load the `radical-tempo-post-covers` skill.

## 5. Review

- Call `review_batch` with `brandId`, every `postId` of the batch, `campaignId`,
  the experiment as `brief` (`change`, `stage`, `readWeek`), and each claim you
  will submit as one entry of `claims`: `slot`, the level's `attribute` and
  `level`, the `row_id` as `readoutRowId`, and `conditionedShare`.
- A `block` verdict: fix each hard finding as its `message` says, then call
  `review_batch` again. Never propose with a hard finding standing.
- `post_of_another_campaign`: drop that post from the batch.
- `post_has_no_bet`: pass the post's `slotId` to `update_posts`.
- Load the `channel-review-rubric` skill and run its soft checks on every post,
  claim, and the brief. Check each post's bet against its slot's bet too.
- Pass the soft findings the user waived in `acknowledged`, as the rubric says.
  Check that each comes back with `applies: true` before you propose.

## 6. Propose

- Call `save_batch_proposal` with `brandId`, `campaignId`, the batch's `runId`,
  the brief, `posts` (each `postId` with its `slotId`), `pending` (each slot
  still waiting on its clip, with one line on what the user does next), the
  `acknowledged` you passed to `review_batch`, and `claims`: each claim you
  passed to `review_batch`, with the claim in plain words as `claim`, and
  `experiment` on the experiment's claim, and `testResult` (the `test_result`
  `test_claim` answered, unchanged) on a claim whose rule you tested. A claim
  says one thing about one level. It stores the brief, the posts, the claims,
  and a fresh review on the batch, so the approval and the next batch read them.
- A refused save lists every reason: a post with no bet, a post not in this
  batch, a slot the batch does not hold, or a batch already approved. Fix each
  one and save again.
- A `reviewVerdict` of `block`: fix each hard finding (step 5) and save again.
- List the batch for the user: each clip, its platforms, its time from
  `scheduledFor` in the user's time zone, its bet, and which arm each post sits
  in. Say plainly that nothing goes out until they approve.
- With `approvalMode: "batch"`, one yes approves the list. With `post`, ask for
  each post.
- A slot still waiting on its post blocks the approval. Finish it and save
  again, or save without it.
- Once approved, call `approve_batch` with `brandId`, `campaignId`, and the
  batch's `runId`. It schedules each stored post at its slot's time, records the
  approval, and submits the stored claims.
  - `approvalMode: "batch"`: pass no `postIds`.
  - `approvalMode: "post"`: pass the posts the user approved as `postIds`. Call
    again as they approve the rest.
  - Pass each post the user declined as `dropPostIds`.
- Read each post's `outcome` and report its `note`. `slot_time_passed`: ask the
  user whether to drop the post or schedule it at another time with
  `schedule_posts`. `not_scheduled`: fix what the `note` says and call again.
- Never schedule a batch's posts with `schedule_posts`, `propose_publish`, or
  `execute_publish_post` in place of `approve_batch`: none of them records the
  approval or submits the claims.
- Never call `submit_claim` for a stored claim. `approve_batch` submits each one
  once. For a claim it answers as `failed` or `unrecorded`, read
  `get_channel_readout` with `campaignId` before you submit it by hand.
- When the run ends before the user approves, list the proposed posts and the
  stored claims in your summary. Call `approve_batch` in the session where the
  user approves.

### Where the brief goes

- `save_batch_proposal` stores the brief, with its swaps and spacing judgments,
  and the waivers in `acknowledged`. `plan_batch` shows them under
  `dossier.lastBatch` on the next batch.
- Never save a campaign's brief, bets, or waivers with `manage_brand_memory`. A
  brand note applies to every campaign of the brand.
- When the user gives a lasting direction for this campaign, offer to add it to
  the campaign's `guidance` with `update_campaign`. Carry over every field that
  still holds: `guidance` replaces the whole guidance.

## 7. Measure

On the next batch, before the brief:

- Call `list_posts` with `campaignId` and `phase: "published"`. Read each post's
  `bet.reads`: its `standing` among the brand's own posts at the same age, and
  at 72 hours its `diagnosis.label`. A read with no `standing` has not run.
- One post's standing says how that post did, never whether its change worked.
  An experiment's result compares its arms; the weekly check decides what held.
- Call `get_channel_readout` with `brandId` and `campaignId` for this campaign's
  claims. Report each claim's `verdict` as the `channel-manager` skill's
  "Measure" step says.
- Read the last batch's experiment from `dossier.lastBatch.brief` against its
  `readWeek`. When that week is not checked yet, say so.
- Never resubmit a `contradicted` claim unchanged.

## 8. The run summary

End every batch with one message to the user:

- the campaign and the week (`slotSheet.isoWeek`);
- what you read, and each section that answered `available: false`;
- the brief, with its experiment, swaps, and spacing judgments;
- the refusals;
- the batch: each clip, its platforms and times, its bet, and its review result;
- soft findings fixed, and soft findings the user waived with their reasons;
- the claims, submitted or waiting for approval;
- earlier posts' standings and earlier claims, and what the weekly check said.

## Per-host setup

Follow the `channel-manager` skill's "Per-host setup", with the batch in place
of the tick, and with these changes:

- Never keep the brief in brand memory.
- `plan_batch` saves the batch, so ChatGPT pauses on it. On a ChatGPT scheduled
  run, stop before `plan_batch` and tell the user the batch is ready to draw.
- On a Cowork scheduled run, stop at the list of posts in step 6, unless the
  user told you to schedule without asking.
