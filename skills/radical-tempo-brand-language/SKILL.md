---
name: radical-tempo-brand-language
domain: product
description: >
  How to set the language a brand's content comes out in. Load when the user
  asks for their titles, descriptions, or on-screen text in another language, or
  asks to change, clear, or check a brand's content language. Covers naming a
  language, picking one from a card, and what the setting does and does not
  reach.
---

# Brand Content Language

Set the language Radical Tempo writes a brand's content in — the titles,
descriptions, and on-screen text it generates for that brand's videos. Two MCP
tools drive it:

- `update_brand` with `language` — writes the setting.
- `show_brand_language_options` — shows the user a card of the languages
  available, and sets the one they pick.

If your tool list has no `show_brand_language_options`, skip every card step
below: ask the user to name a language in text, then pass it to `update_brand`.

## When to use

The user asks for their content in another language: _"put my titles in
Spanish"_, _"our brand should be in Brazilian Portuguese"_, _"go back to
whatever the video is in"_, _"what languages can you write in?"_.

## What the setting reaches

Say this whenever you set it. It is the part users get wrong.

- It changes **generated text**: titles, descriptions, on-screen text.
- It applies to **videos processed from now on**. It does not redo videos that
  are already processed.
- It does **not** translate anything. The words spoken in a video stay as they
  are, and so does the transcript.

## The three paths

### The user names a language you can write in

Call `update_brand` with the brand ID and `language`. A call that changes only
the language needs no `expectedUpdatedAt`. Do not make them pick from a card as
well — they already told you.

Then confirm in one short message: name the language, and state the scope above.

### The user names a language you cannot write in

`update_brand` rejects it. Say plainly that Radical Tempo does not write in that
language, then call `show_brand_language_options` so they can see the ones it
does. Do not guess at a nearby tag — "Cantonese" is not a licence to set
Chinese.

### The user names no language

Call `show_brand_language_options` straight away. The card lists every language
plus "match each video's language", which is the default.

Picking a row sets the language and says so in the conversation. When that
message arrives, confirm it and state the scope — do not call `update_brand`
again.

A card retires once it is picked from, so a pick that **failed** cannot be
retried on it. When the message says the pick failed, say what went wrong in
plain words and call `show_brand_language_options` again, so they have a live
card to pick from.

## Target the brand

Use the brand the conversation already gives you, and do not make the user
restate it. Ask only when they have several brands and named none.

## Clearing it

"Match each video's language" is the default and the way back. Pass
`language: "match-each-video"` to `update_brand`, or let the user pick that row
on the card.

## Translating a video you already have

The setting does nothing to a video that exists. If the user has a video in
context and wants **that** video in the new language, they want a transcript
translation, which is a different thing:

- If a transcript-translation tool is available to you, offer it for that one
  video.
- If it is not, say that translating an existing video is not something you can
  do yet, and point them at the brand's content language for what comes next.

Either way, translating a batch of videos at once is not supported. Say so
rather than starting them one at a time.

## Don't

- **Don't set a language the user didn't ask for.** A question about which
  languages exist is not an instruction to change anything.
- **Don't promise a translation.** Setting the language translates nothing.
- **Don't set `language` with `update_brand` after showing the card.** The card
  does the write itself.
