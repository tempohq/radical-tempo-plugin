---
name: channel-review-rubric
description: >-
  The soft checks a week's batch passes before it is proposed: hook and title
  grounded in the transcript, a payoff inside the clip with no cut sentence, the
  speaker named and their idea kept, a brief that follows from the readout rows
  it cites, and nothing that would embarrass the brand. Gives the finding shape
  `review_batch` uses, so your soft findings sit beside its hard ones, and says
  when a soft finding can be waived. Load at the review step of the
  `channel-manager` weekly tick, or whenever the user asks you to review clips,
  titles, a batch, or a brief before it goes out. Triggers: review, review the
  batch, check the clips, quality check, rubric, soft check, soft finding,
  waive, is this ready, before I publish, hook, title, payoff, cut sentence,
  speaker, off-brand, embarrassing, brief check.
---

# Channel review rubric

Hard checks belong to the server: `review_batch`, `propose_publish`, and the
publish itself run them, and none of them can be waived. This rubric is the soft
half. It needs judgment, so you run it. Never repeat a hard check here:
duration, caption and hashtag counts, cadence, duplicates, and the claim and
brief fields `review_batch` already answers.

## The finding shape

Write each soft finding in the shape `review_batch` uses for its hard ones:

```json
{
  "code": "hook_not_grounded",
  "severity": "soft",
  "message": "The hook says 'she quit her job'; the clip's transcript never says that. Use her line at 0:04: 'I walked out the same day.'",
  "ref": "transcript:<videoId>@4000-9000"
}
```

- `code`: one of the codes below. Never invent another.
- `severity`: always `soft`.
- `message`: what is wrong, then the change that fixes it. A finding that names
  no change is invalid.
- `ref`: the evidence the finding rests on. A finding with no evidence is
  invalid; drop it.

| `ref` form                               | Evidence                                                            |
| ---------------------------------------- | ------------------------------------------------------------------- |
| `transcript:<videoId>@<startMs>-<endMs>` | The span of `get_transcript` you read, on that video's own timeline |
| `post:<postId>`                          | A post field: its title, description, or hook text                  |
| `readout:<row_id>`                       | A `row_id` from `get_channel_readout`                               |
| `memory:<id>`                            | A brand note from `list_memories`                                   |
| `brief`                                  | The week's brief itself                                             |

Group the findings per item the way `review_batch` answers: `posts` by `postId`,
`claims` by `index`, and `brief`. An item with no soft finding has an empty
list.

## The checks

Read the evidence before you judge. Read each clip's own transcript with
`get_transcript`, passing the clip's video ID; a clip's transcript starts at
zero.

| Code                       | Item  | Fails when                                                                                                                                                                                                                | Evidence to cite                                                    |
| -------------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `hook_not_grounded`        | post  | The hook text promises or states something the clip's transcript does not say or show.                                                                                                                                    | The span the hook points at, or the whole clip when nothing matches |
| `title_not_grounded`       | post  | The title claims something the clip does not contain: a fact, a quote, an outcome, a person.                                                                                                                              | The span, or `post:<postId>`                                        |
| `payoff_outside_clip`      | post  | The setup is in the clip and the line that resolves it is not: the punchline, the answer, the reveal.                                                                                                                     | The span that ends the clip                                         |
| `sentence_cut`             | post  | The first segment starts mid-sentence, or the last segment ends mid-sentence.                                                                                                                                             | The first or last segment's span                                    |
| `speaker_not_named`        | post  | The brand requires the speaker named (a brand note says so) and the title, description, or hook text does not name them.                                                                                                  | `memory:<id>` and `post:<postId>`                                   |
| `idea_misattributed`       | post  | The one idea the post's text says the clip carries is not the idea the speaker was making: a quote out of context, a joke read as a claim.                                                                                | The span, read with the segments around it                          |
| `brief_not_following`      | brief | The brief's recommendation does not follow from the readout rows it cites: it favors a level the rows show as flat or negative, or cites no row. With no readout, the same test runs against the playbook slots it cites. | `readout:<row_id>`, or `brief` with the slot named in `message`     |
| `brief_number_not_in_rows` | brief | The brief states a number no cited row states. Rounding a row's own number is not a new number.                                                                                                                           | `readout:<row_id>` or `brief`                                       |
| `claim_not_following`      | claim | The claim's plain words say more than the cited row: another platform, another level, a stronger effect.                                                                                                                  | `readout:<row_id>`                                                  |
| `off_brand`                | any   | The item would embarrass the brand: a slur or insult, a mocked guest, a private detail, a misleading promise, or a topic a brand note rules out.                                                                          | The span, `post:<postId>`, or `memory:<id>`                         |

- Judge each post on its own clip. Never pass a post because another post of the
  same clip passed.
- `speaker_not_named` fails only where a brand note asks for the speaker's name.
  With no such note, skip it.
- `off_brand` reads the brand's notes first. A topic the brand's own videos
  cover often is on-brand.

## What a finding asks of you

- **Fix:** make the change the message names, with `update_post` for a post's
  text or a new clip for a cut that lands wrong. Then run the check again on
  that item. Dropping a post from the batch is a fix too.
- **Waive:** only the user waives a soft finding, and only with a reason. Keep
  the code, the `ref`, and their reason in the run summary and in the week's
  brief note. Never waive one yourself on a scheduled run with no user there:
  fix it or drop the post.
- A hard finding is never waived, by anyone. Fix the item and review again.
- Pass each waived soft finding to `review_batch` in `acknowledged`, as
  `{ code, ref, reason }`: its `code` and `ref` unchanged, the user's reason as
  `reason`. The server echoes it with `applies: true`. It echoes a hard code
  with `applies: false`, because that waives nothing. Nothing is stored, so pass
  the same list on every `review_batch` call of the tick.

## What to tell the user

- Lead with what blocks: the hard findings, then the soft findings you could not
  fix.
- Quote the `message` and point at the evidence: "0:04 in the clip", "the
  title", "last week's check, row `…`".
- Say which soft findings you fixed, and which the user waived and why.
