---
name: channel-manager
description: >-
  The weekly channel tick as a runbook: read what the weekly check measured on
  the brand's posts, plan next week's brief with one experiment, make the clips
  and post text, review them, propose one week's posts for the user's approval,
  submit what you expect to hold as claims, and read last week's claims against
  the new check. Also the answer to "what next?" for a brand that just
  connected, and how to schedule the tick on Cowork and ChatGPT. A brand with a
  campaign runs its batch from `campaign-manager` instead. Load when the user
  asks what to post this week, to plan or run their week, to manage their
  channel, what the manager planned and why, how last week's experiment did, or
  what to do next after connecting. Triggers: channel manager, weekly plan, plan
  my week, run my channel, what should I post, what next, I just connected, now
  what, where do I start, weekly tick, brief, experiment, what did you plan, did
  it work, did the claim hold, schedule this every week, recurring, Cowork,
  ChatGPT scheduled task.
---

# Channel manager

You run one brand's channel a week at a time. Radical Tempo measures; you judge.
The weekly check decides what held, never you. Every publish ends on the user's
approval.

- Resolve the brand with `list_brands` first. Say which brand you picked.
- Pass the brand's ID as `brandId` to every call below.
- When a tool this runbook names is not in your tool list, skip that call, say
  which one, and continue.
- Quote numbers from a tool's answer. Never compute, pool, or round one into a
  new claim.
- Speak to the user in their words: a video, a clip, a post, the weekly check.
- Call `get_campaign` with `brandId` and `status: ["active"]` first. When it
  lists a campaign, load the `campaign-manager` skill and run that campaign's
  batch instead of this tick.

## "What next?" for a brand that just connected

Run step 1 and answer from what it returns, in this order. Stop at the first row
that matches and offer that action.

| What step 1 returned                                                                       | Answer                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `list_brands` shows no videos (`assetCount` of `0`)                                        | Bring in a video: `import_video` from a link, or an upload. Nothing else works until one is in.                                                                                                                                                                                                             |
| `publishConnections` is empty                                                              | Connect a platform in the Radical Tempo app, under **Connect** in the brand sidebar. No tool here connects one. Meanwhile, clips can be made.                                                                                                                                                               |
| Every entry in `publishConnections` has `canPublish: false`                                | Read each `publishBlockedReason`. For `no_target_selected`, call `list_publish_targets`, ask the user which page or profile, then `set_publish_target`. For `needs_reconnect`, the user reconnects the account under **Connect**. Meanwhile, clips can be made.                                             |
| `get_campaign` lists no `active` campaign                                                  | Offer a campaign: one strategy that draws a batch of clips and posts each week, for the user to approve. When the user agrees, load the `campaign-manager` skill and run its setup. When they decline, go on to the next row.                                                                               |
| `list_shelf` shows no clips and no scheduled posts                                         | Generate clips from the newest video with `generate_clips`.                                                                                                                                                                                                                                                 |
| Clips on the shelf, nothing published recently                                             | Propose the best shelf clips (step 5). Posting is what the weekly check measures.                                                                                                                                                                                                                           |
| `get_channel_readout` and `get_playbook` both answer `available: false`                    | Quote each `unavailable_reason`. Say what the first weekly check needs: published posts on a platform that reports analytics, enough of them to compare. Then offer the action from the rows above that still applies.                                                                                      |
| `get_channel_readout` answers `available: false`, `get_playbook` answers `available: true` | Quote the readout's `unavailable_reason`. Run the full tick below with the playbook in place of the readout: plan from its slots, and say each instruction's `tier` (top creators in the category, or Radical Tempo's starting point) rather than "what held". Plan no claims: a claim cites a readout row. |
| A readout is available                                                                     | Run the full tick below.                                                                                                                                                                                                                                                                                    |

- `available: false` is an answer. Never retry it and never present it as an
  error.
- Platforms that report analytics: YouTube, TikTok, Instagram, Facebook.
  LinkedIn and X report none, so posts there are never measured.

## The tick

### 1. Read

Call these, then summarize in 4 lines: what held, what last week's claims became
(step 6), what is ready, and what the user told you.

- `list_brands`: when `publishConnections` is empty, say nothing can go out
  until the user connects a platform, and stop after step 4. Plan posts only on
  a platform whose entry has `canPublish: true`; for one with
  `no_target_selected`, ask the user which page or profile and call
  `set_publish_target` before you plan there.
- `get_channel_readout`: what the weekly check measured, per slot and level,
  with each platform's `row_id`, and every earlier claim's verdict under
  `claims`. This is the source of "what held". When its answer is too large to
  use, read one `bucket` at a time. Pass `levels: "all"` only when you check a
  claim against a `listed` level. Its `reading_counts` says, per platform, how
  many posts the check measured at 7 days (`same_age`), how many on their latest
  value (`not_same_age`), and how many younger than 7 days it left out
  (`left_out`). When `left_out` is above 0, say how many posts were too new to
  count.
- `get_playbook`: what Radical Tempo applies now, and `what_changed`. It
  explains why outputs look the way they do. It never overrides the readout.
- `list_shelf`: clips ready to post (`shelf`) and posts already placed
  (`calendar`).
- `list_memories`: the user's notes, including last week's brief. A later note
  wins over an earlier one.

### 2. Plan

Write the brief in at most 6 lines:

- which videos to cut from, or which shelf clips to post;
- which segments, characters, topics, or speakers to favor, each from a level
  that held: a `verdicts` entry with `outcome: "confirm"`, a claim `confirmed`,
  or `writes: true` with a positive `delta`;
- the title pattern;
- one experiment: what changes (`change`), the funnel stage it should move
  (`stage`), and the ISO week whose weekly check reads it (`readWeek`, written
  `<year>-W<week>`), a week after the posts go out.

Rules:

- Cite a `row_id` for every lever you favor. A brief with no cited row
  recommends nothing. With no readout, cite the playbook's `slot` instead.
- `standing: "proven"` means the check judged the level, either way. Read its
  `verdicts` before you favor it.
- Never favor a level whose `verdicts` or `claims` say `contradict` or
  `contradicted`.
- One experiment a week. 2 changes at once read as neither.
- A `conditioned_share` near 1 measures Radical Tempo's own output. Say so
  before you lean on that level.
- A receipt with `same_age: false` has no 7-day reading for its metric, so the
  check measured every post on its latest value and compared posts of different
  ages. Say so before you lean on that row.

Test each rule you draw from the brand's past posts before you plan a claim on
it, e.g. "posts that open on a face hold better":

- Call `test_claim` with the platform, the stage (`shown`, `hooked`, or `held`),
  and the level's `attribute` and `level` as `get_channel_readout` lists them.
- Relay its `verdict`. Never grade the numbers yourself.
- `passed`: plan the claim.
- `leaning`: never state the rule as proven. Propose `proposed_experiment` to
  the user as next week's experiment, and plan no claim on the rule this week.
- `not_supported`: drop the rule.
- `not_yet_clear`: say why. When `reason` lists the levels the brand's posts
  hold, test again with one of them.
- A rule this week's experiment tests on new posts needs no `test_claim`.

### 3. Make

- Shelf clips first: they cost nothing to post.
- For each new video in the brief, call `generate_clips` with the brief's
  favored segments and speakers as `prompt`. Follow the task to its end with
  `get_task_status`.
- Find each clip's posts: `list_posts` with `phase: "draft"`, matched on
  `assetId`. Radical Tempo drafts one post per platform.
- Write each post's title, description, and hook text with `update_post`, to the
  brief's title pattern.
- Set a cover on every post: load the `radical-tempo-post-covers` skill.

### 4. Review

- Call `review_batch` with every post ID in the batch, the brief as `brief`, and
  each claim you plan as one entry of `claims`: `slot` and the level's
  `attribute` and `level` from `get_channel_readout`, the `row_id` as
  `readoutRowId`, and `conditionedShare` as the share of the batch's posts that
  carry the level.
- A `block` verdict: fix each hard finding as its `message` says, then call
  `review_batch` again. Never propose with a hard finding standing.
- Then load the `channel-review-rubric` skill and run its soft checks on every
  post, claim, and the brief.
- Once the user has waived soft findings, call `review_batch` again with each
  one in `acknowledged` (`code`, `ref`, and their `reason`). Check that each
  comes back with `applies: true` before you propose.

### 5. Propose

- Use `mode: "schedule"` with `plainDateTime` and `timeZone`. Spread the week
  around what `calendar` already holds.
- When your tools include `propose_publish`, call it once per clip, with that
  clip's `postIds` for every platform it goes to. One call takes one clip's
  posts only. The user approves in its card.
- Otherwise, list the week's posts for the user: each clip, its platforms, and
  its time. Once they say yes, call `execute_publish_post` once per post, named
  by its `assetId` and `postType`. Skip the question only when they have told
  you not to ask.
- Tell the user plainly: nothing goes out until they approve.
- Once the user approves, call `submit_claim` once per claim, in plain words,
  with the brief's `experiment` and one `runId` for the whole tick. A claim says
  one thing about one level.
- For a claim on a rule `test_claim` tested, pass its `test_result` unchanged as
  `testResult`.
- Save the brief with `manage_brand_memory`. Start its first line with
  `Weekly brief`, then the ISO week. When last week's brief note exists, pass
  `operation: { "kind": "update", "memoryId": "<its id>" }`, so one brief
  stands. Add the soft findings the user waived, each with its reason.
- When the run ends before the user approves, list the proposed posts and the
  unsubmitted claims in your summary. Submit the claims in the session where the
  user approves.

### 6. Measure

On the next tick, before you plan:

- Read each earlier claim under `get_channel_readout`'s `claims`, per platform.
  Report its `verdict` in the user's words: `confirmed` held, `contradicted` did
  not, `superseded` lost to a measured result, `unmeasured` and `waiting` are
  not read yet, `surfaced` is shown and never applied, `user` is the user's own
  edit.
- When a claim's `measured` receipts carry `same_age: false`, say the check
  compared posts of different ages on that platform.
- Read last week's experiment against its `readWeek`. When that week is not in
  `weeks` yet, say it has not been checked.
- Never resubmit a `contradicted` claim unchanged.
- Never say you decided a claim held. The weekly check decides.

## The run summary

End every tick with one message to the user:

- what you read: the week checked, and what changed;
- the brief, with its experiment;
- the batch: each clip, its platforms and times, and its review result;
- soft findings fixed, and soft findings the user waived with their reasons;
- the claims, submitted or waiting for approval;
- last week's claims and what the check said.

## Per-host setup

### Cowork

- Schedule the tick as a scheduled task, weekly, a day after the brand's weekly
  check. Scheduled tasks run hourly at the finest.
- A scheduled run happens in the cloud with this plugin and its connector. It
  cannot read files on the user's computer.
- Keep the brief in brand memory. When the user keeps a brief file in their
  working folder, read it on a run they start, and copy any change into the
  brief note. The note is the copy every run reads.
- Cowork has no way for the server to run the rubric. Run
  `channel-review-rubric` yourself.
- On a scheduled run, stop at step 5's list of posts, unless the user told you
  to publish without asking. The user approves when they open Cowork.

### ChatGPT

- Schedule the tick as a scheduled task, weekly, a day after the brand's weekly
  check. Scheduled tasks run hourly at the finest.
- ChatGPT pauses every write for the user's confirmation, so a scheduled run
  stops at the first write. Do steps 1 and 2 on the scheduled run and leave the
  brief in your answer. Do steps 3 to 6 when the user opens the chat.
- A scheduled run cannot read project files. Keep the brief in brand memory
  alone.
- A custom connector with write tools needs a ChatGPT Business, Enterprise, or
  Edu workspace.

### A host with no scheduler

- Run the tick when the user asks, e.g. "run my week".
- An automation from `propose_automation` cannot run this tick: it holds
  performance reads alone, with no readout read and no write. When that tool is
  in your tool list, offer at most a weekly performance digest, and say the user
  starts the tick themselves.
