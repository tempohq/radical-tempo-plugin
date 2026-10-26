---
name: radical-tempo-post-covers
domain: product
description: >
  How to generate and set a cover image for a post with AI. Load when the user
  asks for a new cover or thumbnail for a post (YouTube thumbnail, Instagram
  cover, etc.), or to change an existing one. Covers the full loop: pick the
  target post, generate candidates, wait for them, present them, and on the
  user's choice set it as the cover.
---

# Post Covers

Generate cover images for a post and set the one the user picks. This works the
same whether you are Cue in the web app or a Claude client on the MCP server —
it is driven entirely by MCP tools.

## When to use

The user asks for a new cover / thumbnail for a post, or to redo an existing one
(e.g. "make me a YouTube thumbnail", "generate a cover for this", "try a
punchier thumbnail").

## First: generate something new, or use something that exists?

Read which one the user asked for, and don't substitute generation for a request
to look at what's already there ("find an image we have that could work", "what
covers have we used before?", "is there something in the library for this?").

For the existing-image path the flow is **`search_images` → `show_images` → they
pick → `update_post`** — the same pick-then-set ending as generation, just
without steps 1–4. Showing is not optional: a search returns links the user
cannot see, so a turn that searches and then describes the results in prose has
answered nothing. Put them on screen and let them choose.

Only fall back to generating when the search comes back empty, and say that is
what you are doing rather than silently switching.

## Target the post

You need the brand, the video (asset), the platform (post type), and the post
itself. Use whatever the conversation already gives you — if you already know
which brand, video, and platform the user means (because they said so, or your
environment makes the current one available), use it directly and don't make the
user restate it.

If any of those is missing or ambiguous, resolve it before generating: ask the
user which platform/video they mean, or use the read tools/resources to find it.

Don't pick an aspect ratio — a cover is sized automatically from the video/post
(a Short comes out 9:16, not 16:9). Pass `target: { kind: "cover", … }`, which
takes no aspect ratio at all; `{ kind: "standalone" }` is the other form, for a
brand image tied to no video or post. Just make sure the cover form knows which
post (see below).

The **post**, not the video, carries everything viewers see on the platform: the
title, description, tags, the current cover, and previously used covers. If your
instruction depends on any of those — "clickbait text based on the title",
"based on the existing cover" — read the _post_ resource. The video (asset)
resource is the raw content (transcript, analytics); it has no title or cover
and reading it will leave you missing the very thing the user asked you to use.

**Check `editsLockedByPublishing` on that post before you generate anything.**
Some platforms freeze a post once it is live, and a frozen post can never take a
new cover. Generating first and discovering it at the end spends real work — and
the user's time waiting for it — to produce images you have to throw away. Say
the post is final and offer what actually helps: the same treatment on a post
that is still open, or a new post for the video.

## Steps

1. **Decide whether you need a reference image — this is not optional when the
   request edits an existing image.** Read the request:
   - If it edits, updates, tweaks, or builds on an image that already exists —
     the current cover ("add text to _this_ cover", "make this thumbnail
     punchier"), a brand image, or a frame — you **must** include that image as
     a reference. The model cannot modify an image it was never given; skipping
     this is why "add some text to the cover" produces nothing. Call
     `search_images` to find it (for the current cover, filter `isCover: true`
     on the post) and pass it to `generate_images` as a labeled reference. Refer
     to references in the instruction with `{label}` placeholders; every
     reference must be used and every placeholder must match a reference (e.g.
     instruction `"Add the headline across the top of {cover}"` with reference
     `{ label: "cover", objectId }`).
   - If it's a brand-new image from scratch (a graphic or abstract cover with no
     existing image to build on), no reference is needed — go straight to
     generating.
2. **Author the concrete creative content — don't pass the user's phrasing
   through.** A brief like "clickbaity text" or "a punchy headline" is a request
   for you to _write the words_, not a string to forward. Draft the actual
   headline/overlay text and describe any graphic elements (arrows, callouts,
   highlights) concretely. Ground the text in the post's own **title,
   description, and tags** when they're set — read the _post_ resource to get
   them (the asset resource has no title). Then fold your drafted text into the
   instruction and generate.

   **Don't stop to get the wording approved first.** Options commit nothing —
   they sit in the library until the user picks one, and nobody outside the
   brand sees them — so asking spends a round trip to arrive at the same grid,
   and a description of a thumbnail is a worse thing to react to than the
   thumbnail. Their say is the pick in step 5, and that one is not yours to
   make. If the brief is too thin to draft from at all ("make me something
   good", with no title, description, or tags to ground it), that's a genuine
   ambiguity — ask about the missing content, not for permission.

3. Call `generate_images` with the brand ID, your instruction, a count, any
   references, and `target: { kind: "cover", … }` so the cover is sized
   correctly — inside it pass `postId` (preferred; the post resource you read
   gives you one), or `assetId` + `postType`, which targets the asset's post of
   that type — an asset has at most one, so the pair names exactly one post. The
   asset ID is never a valid `postId`. It starts a task. It answers with a task
   handle, whose `taskId` is the task ID, or with a `resource_link`, whose
   `name` is the task ID.

   **The number of options is `count`, never the instruction.** `count` defaults
   to 1, so pass 2 or 3 whenever the user will choose between them. The
   instruction describes ONE image and is reused for each — write "Generate 3
   options" into it and you get back a single picture of a three-panel collage,
   which cannot be set as a cover. That failure is easy to miss, because the
   collage looks like options: you then spend the rest of the turn regenerating
   to fix a problem that came from the wrong parameter.

4. Wait for the task to finish **in this same turn**. In Cue, call `awaitTask`
   with the task ID. In any other client, decide by what `generate_images`
   answered:
   - A task handle: poll `tasks/get` at the interval the task reports.
   - A `resource_link`: poll `get_task_status` with its `name` until the task is
     terminal. Do this even when your client can send `tasks/get`, because
     `tasks/get` never hands such a client the task's result.

   Read `isError` on the finished task's `result` before its links. `completed`
   means the work finished, not that it succeeded. A `result` with
   `isError: true` says why the generation failed and links no image: pass that
   reason on, and do not call `show_images`. In any other `result`, the image
   links, with a `uri` under `data://radical-tempo/images/`, are the images this
   task generated, in order. The last link is the task's job, not an image. Pass
   the image links' `name`s to `show_images` in that order so the user sees them
   (`awaitTask` returns the images as image links already). The user needs to
   _see_ the images before choosing. Never look for them with `search_images` or
   by re-reading the post; the task result is the only thing that ties the
   images to this request. Unless your host tells you otherwise, assume nothing
   will proactively notify you when a task finishes. Never close the turn with a
   promise to follow up. A reply ending "I'll let you know when it's ready"
   strands the task, because nothing comes back to prompt you and the user waits
   for a message that never arrives. (Cue is the exception — it posts a
   `<job-update>` message into the conversation when a task finishes, so a wait
   that times out there is safe to hand off.)

5. Let the user pick — you don't pick for them **unless they handed you the
   pick**. "Generate a few and set the best one as the cover" is a delegation:
   choose, set it, show them what you chose, and say it's easy to swap. Anything
   short of that — including "find one that fits and use it" — leaves the choice
   theirs, because they have not seen the candidates yet. How the choice reaches
   you depends on the client:
   - **Web app (Cue):** each generated image is shown with a **Use as cover**
     button, so you don't need a separate question — the user clicks the one
     they want. Their click arrives as a new user message like "Use image N as
     the cover", where N is that image's position in the grid you just showed.

     **This reading only applies when you actually showed a grid earlier in this
     conversation.** A bare "use number 3" with no grid behind it refers to
     nothing — the user is thinking of something you cannot see, or has
     misremembered. Ask which image they mean. Do NOT search or generate to
     produce a grid and then treat its third entry as their answer: that invents
     the choice and sets a cover they never picked, which is the one outcome
     this whole flow exists to prevent.

   - **Other MCP clients (CLI/desktop):** ask which image they want with an
     appropriate tool, one option per image — labels like "Image 1", "Image 2"
     matching the order shown, each with a short description of that image.
     Single-select. The user can always pick the automatic "Other" to ask for a
     different look (a regeneration) instead.
6. Act on the choice:
   - A chosen image (the web "Use image N…" message, or the selected option) →
     call `update_post` with the current brand ID, the post, and
     `cover: { objectId: "<that image's object ID>" }` — the Nth image link from
     step 4. Identify the post by its `postId` if you have one, otherwise by
     `assetId` + `postType` (this targets the asset's post of that type, of
     which it has at most one) — never pass the asset ID as the post ID. Set a
     cover the user chose, or the one you chose when they delegated the pick in
     step 5 — never auto-apply one they neither picked nor asked you to pick.

     **A pick is not reopened.** Once they have named an image, set it — do not
     run `judgeCover` on it first and do not withhold the write because you
     think it is a weak choice. `judgeCover` exists to catch a broken image
     before you show it; running it on something the user already chose puts
     your taste above their decision, and answering "that one reads poorly, want
     to see others?" leaves the cover unset on a request that was already
     settled. If you genuinely think it hurts the post, set it first and say so
     afterward — they can swap it in one message.

   - "Other" / a free-text reply → treat it as new direction. If the user wants
     different images, generate again (step 3) with their feedback folded into
     the instruction.

## Don't over-iterate — the user chooses

Your own quality check (`judgeCover`, which looks at the pixels) is for catching
a **broken** result before you show it — clipped headline text, a garbled logo,
an obviously wrong crop. If it finds one of those, you may regenerate **once**
on your own to fix it. That is the whole budget.

**`judgeCover` will never come back clean, and it is not supposed to.** Ask it
about any image and it will find something to improve — the contrast could be
stronger, the face could be larger, the text could be tighter. That is a
critique, not a defect, and treating "the judge still has notes" as a reason to
try again is a loop with no exit, because there is no image that ends it. The
question is never "is this the best I can do?" It is "is this broken?" If it
isn't broken, you are done generating.

So when the images are in hand and the result still isn't what you hoped, you
have exactly two moves — and neither of them is generating again:

- **Show what you have** and say plainly what you think is off — "here are two;
  the text on the second is tighter, but neither nails the shocked expression."
  The user may well be happy with one, and they can see the images while you are
  only describing them. If they explicitly handed you the pick ("generate some
  and set the best one as the cover"), finish the job: set the one you'd choose,
  then show them and say it's easy to swap.
- **Ask for direction** if you genuinely don't know which way to take it — a
  different angle, a different headline, a reference image they have in mind.

Never a third generation in one turn without new input from the user. If you are
about to call `generate_images` again and nothing has changed since the last
call except your own opinion, that is the loop — stop and pick one of the two
moves above.

And never let `judgeCover`'s verdict stand in for the user's choice: a critique
is not a selection. The user picks the cover (by clicking in the web app, or by
answering the `presentOptions` question elsewhere); you only apply what they
picked.

This is client-agnostic: generate, show the images, and set the one the user
chose. The only difference is how the choice arrives — a "Use as cover" click in
the web app, or a multiple-choice answer in a CLI/desktop client. Keep your own
text short; the images carry the detail.
