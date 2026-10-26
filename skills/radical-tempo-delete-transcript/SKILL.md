---
name: radical-tempo-delete-transcript
domain: product
description: >
  How to delete one of a video's transcripts. Load when the user asks to delete,
  remove, or get rid of a transcript, a translation, or a language on a video —
  "delete the Spanish transcript", "remove that translation", "I don't need the
  French one anymore". Covers naming a language, showing the pick-list, the
  confirmation step, and what the video reads afterwards.
---

# Delete a Transcript

A video keeps every transcript it has been given — the one it was uploaded with,
and each translation or copy made since. This is how the user deletes one they
are done with.

## When to use

The user asks to delete, remove, or drop a transcript, a translation, or a
language on a video. Load this even when they only name the language ("get rid
of the Spanish one"), because the language is what the tool takes.

## Say it cannot be undone

Deleting a transcript is permanent in Radical Tempo. Say so before they confirm,
in your own words, and never offer to put one back or to undo it. If they ask
for a deleted transcript later, tell them it is gone and offer to translate the
video again.

## The flow

`propose_delete_transcripts` drives the whole thing. It puts the deletion in
front of the user however their client can show it, and you never delete
anything they have not agreed to.

1. **Know which video.** Use the video in the conversation's context. Ask which
   one they mean only when nothing in the conversation names it.
2. **Call `propose_delete_transcripts`.**
   - The user named a language — pass it in `languages`, as they said it
     ("Spanish", "es", "pt-BR" all work). They are then asked to confirm.
   - They named none — omit `languages`. They are offered every transcript they
     can delete, and confirming is a separate step after they pick.
   - They named several ("delete the Spanish and French ones") — pass both.
3. **Stop and wait.** The call deletes nothing, so never say a transcript is
   deleted. How the user answers depends on their client, and the result tells
   you which case you are in:
   - **No `approvalToken`** — read the tool's own text, which says which of 2
     cases you are in. Either the user is answering somewhere you cannot see, so
     say nothing more and wait for their next message, or you are being told to
     ask them and call `propose_delete_transcripts` again with their answer in
     `languages`. Do not call `delete_transcripts` either way.
   - **An `approvalToken`** — ask them to confirm in your own words, and call
     `delete_transcripts` with that token once they say yes.

## When the tool refuses

It refuses rather than guessing, and the refusal carries what the user may
delete instead. Read it back to them and offer those.

3 rules keep a transcript whatever the user asks:

- The video's **original** transcript — the words it was uploaded with.
- One **another post or edit reads**, because deleting it would change what that
  post says.
- The **last one left**, because a video has to have something to read.

So a video can hold several transcripts and still have none to delete. Say which
rule applies rather than saying it failed.

## What the video reads afterwards

Deleting the transcript a video currently reads switches the video to another of
its own. `fallbackLabel` names which one before they confirm, and
`currentTranscriptLabel` names it once the deletion is done. Mention it — the
video's words change, and that is worth a sentence.

## What this does not cover

- **Making a transcript.** A video is transcribed when it is processed, and a
  translation is made from the transcript picker in the video editor.
- **Switching which transcript a video reads** without deleting anything. That
  is the transcript picker in the video editor.
- **Deleting a video.** Different thing entirely, and this tool never touches
  the video itself.
