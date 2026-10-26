---
name: radical-tempo-transcript-translation
domain: product
description: >
  How to translate a video's transcript into another language. Load when the
  user asks to translate a video, its transcript, or its captions ("translate
  this into Spanish", "can I get a French version of the transcript?", "what
  languages can you translate into?"), or for captions in more than one language
  ("show English and Spanish captions"). Covers checking what the video already
  has, offering the languages that are available, confirming before the work
  starts, what to say while it runs, and stacking a translation beside the
  original in the captions.
---

# Translate a transcript

Translating writes a NEW transcript for the video in the language the user
picked. Nothing already there is replaced, and the video keeps reading the
transcript it read before.

## When to use

The user asks for a video in another language, for a translated transcript, or
for translated captions. Also load it when they ask which languages are
available, or for captions in 2 or 3 languages at once.

A request to switch or set a video to a language is not a translation. Call
`set_asset_transcript` with the brand, the video, and that language, and answer
from the outcome it returns, which its own description spells out case by case.
Come back here only where that description tells you to offer a translation.

A request to read the words is neither. That is the video's `asset transcript`
resource, and switching writes.

A request to show a language in the captions beside the one already there, such
as "keep the English and add Spanish under it", is not a switch. Take **Show
both languages in the captions** below, and translate first only when the video
lacks that language.

## Read these two before you answer

1. The video's `transcripts` resource — every transcript it holds, each with its
   language, which one is in effect, and which is the original. This is how you
   know whether the language they asked for is already there. Some videos list
   no transcript of their own and still have words, so read `inEffect.language`
   and `originalLanguage` at the top of that response as well — a language in
   either counts as one the video already has.
2. The `translation-languages` resource, at
   `data://radical-tempo/translation-languages` — the languages translation is
   offered in, each with a BCP-47 `tag` and an English `name`. It is one list
   for every brand and every video, so do not build a URI under either. The set
   is not yours to guess at, and it changes without a release, so read it rather
   than answering from memory.

Before you conclude the video lacks the language, check it against the
`language` of every entry the `transcripts` read returned, one at a time, plus
`inEffect.language` and `originalLanguage`. Do not decide from a summary of the
list you wrote yourself: a video with several transcripts is easy to skim past,
and a language you read as absent when it is there sends the creator into a
translation they are already holding.

Match what the user said against both `tag` and `name`, ignoring case. Pass the
`tag` exactly as `translation-languages` spells it, never the `language` the
`transcripts` read reports — a transcribed transcript often carries a 3-letter
code (`spa`) where translation is offered as `es`. Comparing the two spellings
is the tool's job: it folds them together and refuses a language the video
already has.

A video with no transcript at all has nothing to translate. Say so and say that
its processing has to finish first, rather than picking a language.

## Four ways the request arrives

### They named a language that is available

Go to the confirmation step below, unless the video already has that language —
then take the section on that instead. Do not start translating because they
were specific: the confirmation is not about which language, it is about
spending the time.

### They named a language that is not available

Say plainly that transcripts cannot be translated into that language, then show
the language card described below so the request does not end in a dead end.
Never offer a near neighbor as though it were what they asked for.

### They named no language

Show the language card, then confirm the language they pick.

**The card holds at most 6 options and the list is longer than that**, so choose
the 6 most likely from the conversation — the brand's own language, languages
this video or others already have, languages they have mentioned — and leave the
free-text choice on, saying in the question that any language from the list can
be typed instead. Single-select. Match whatever they type back against
`translation-languages` before you act on it, and treat a language that is not
there as the refusal above.

### The video already has that language

Switch the video to those words in this turn. The user asked for that language,
so asking them to confirm the language they just named costs a turn and adds
nothing.

Call `set_asset_transcript` with the brand, the video, and the language before
you write any reply. Answer from the outcome it returns: its own description
spells out every outcome, including the ones where nothing was written, so
follow that rather than a paraphrase here. Two things it says that this path
turns on:

- **One transcript matches** and the video switches to it. Confirm the language
  in words.
- **Several match** and nothing is written. That is the one question this path
  keeps: put the matches to the user as a card, then call again with the
  `assetTranscriptId` they picked.

Do NOT ask whether to switch, in a card or in prose. "Want me to switch it to
the existing Spanish one?" and "Would you like me to set the existing Spanish
transcript?" both ask, and both cost the user a turn to answer a question they
settled by naming the language. Whether a card renders makes no difference.

Do NOT translate again either, and do not offer to. One question is allowed on
this path: which of several same-language transcripts to use.

The confirmation step below does not apply here, whatever the user's words were.
A translation spends time the user should agree to spending. A switch to words
the video already holds spends nothing and writes no transcript, so there is
nothing to agree to. That holds for a request phrased as a translation too — the
switch is what satisfies it.

Translate again only when the user asks for a fresh translation in their own
words, such as "the Spanish one is wrong, redo it". That call needs
`retranslate: true`. Without it, `translate_transcript` refuses and names the
transcript that already exists.

## Confirm before every translation

A translation you are about to start takes a confirmation. A switch to words the
video already has takes none: it spends no time and writes no transcript, so
there is nothing to agree to, and asking costs the user a turn.

Show a confirmation card on every path that reaches a translation, with one
question, two options — start it, or pick a different language — and say in the
question what the user is agreeing to:

- which language the transcript will be translated into,
- that it runs in the background and that a longer video takes longer. Do not
  name a duration: nothing here reports one, and a figure you invent is the one
  the user will hold you to,
- that it writes a new transcript and changes nothing the video already has.

Start the translation only when they choose to start it.

## Run it

Call `translate_transcript` with the brand, the video, and the `tag`. It returns
a task, and translation is background work that takes longer the longer the
video is, so do not sit on it:

- In Cue, say it has started and that you will post an update in the
  conversation when it finishes, then end the turn. The app posts that update on
  its own. Never call `awaitTask` on this task right after dispatching it.
- In any other client, decide by what the tool answered:
  - A task handle, whose `taskId` is the task ID: poll `tasks/get` at the
    interval the task reports.
  - A `resource_link`, whose `name` is the task ID: poll `get_task_status` with
    that ID until it is terminal. Do this even when your client can send
    `tasks/get`, because `tasks/get` never hands such a client the task's
    result.

The finished task names the transcript the translated words landed in. Say the
translation is there, and that the video still reads the transcript it read
before. Offer to switch it to the new one, and when they say yes call
`set_asset_transcript` with the `assetTranscriptId` the task named. Not by
language: a retranslation leaves 2 transcripts in that language, so a call by
language asks them to pick again. Say too that the captions can show the new
language beside the original instead, which the section below covers.

Where the task names no transcript, nothing ties the video's transcripts back to
the job that wrote one, so do not guess which row is new. Put the video's
transcripts in that language to the user by the date each was added, and switch
to the one they pick.

A failure comes back saying what it was. Pass that on rather than retrying: a
refused language does not become available by asking again, and a translation
that ran out of time on a long video does the same thing twice.

## Show both languages in the captions

A switch replaces the words the video reads. The user can instead keep the
original and show the translation beside it: captions stack up to 3 caption sets
at once, one per transcript. Offer this when the user asks for captions in 2
languages, or asks to keep the original words on screen too.

- The user turns it on in the editor. The captions settings on the **Design**
  tab list every transcript the video has, each with a switch. Up to 3 can be
  on, the last one on cannot be turned off, and dragging a row moves that set up
  or down the stack.
- You cannot turn a language on in the stack: no tool writes these settings. Do
  not call `set_asset_transcript` for this request, because it switches the
  words rather than adding a language.
- Once the user has picked caption languages in the editor, those switches
  decide what the captions show. A later `set_asset_transcript` switch changes
  the words the video reads and leaves the captions on the languages they
  picked. After a switch, tell the user to check those switches if they expect
  the captions to change too.
- A brand template can set how many languages its videos stack, from 1 to 3, and
  whether the transcript the video reads takes the top slot. While a video has
  no picks of its own, the template picks its captions this way:
  - With **Prefer source language** on, the transcript the video reads keeps the
    top slot, so a template showing 1 language shows only that transcript.
  - The template gives every remaining slot to the video's newest transcripts,
    one per language. A new translation is the newest, so it can take a slot and
    push an older language off the captions.

  Do not promise what a translation will do to the captions. Say it can change
  them where the video follows its template, and that picking languages with the
  switches in the editor stops the template from changing them.
  `radical-tempo-feature-catalog` covers both template settings.
