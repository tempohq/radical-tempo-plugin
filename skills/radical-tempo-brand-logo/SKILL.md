---
name: radical-tempo-brand-logo
domain: product
description: >
  How to set a brand's logo from an image the user gives you. Load when the user
  attaches or links an image and asks to use it as their brand's logo, or asks
  to change, replace, or update the logo for a brand. Covers picking the target
  brand, getting the image, and applying it.
---

# Brand Logo

Set a brand's logo to an image the user supplied. This works the same whether
you are Cue in the web app or a Claude client on the MCP server — it is driven
entirely by the `logo` field of the `update_brand` MCP tool.

## When to use

The user wants a brand's logo set or changed and has an image in mind — they
attached one to the conversation ("use this as my logo"), linked one ("grab the
logo from https://…"), or point at an image already in the brand ("use that
graphic we made as the logo").

## Target the brand

You need the brand. Use whatever the conversation already gives you — if you
already know which brand the user means (because they said so, or your
environment makes the current one available), use it directly and don't make the
user restate it. Only ask when it's genuinely ambiguous, e.g. the user has
several brands and named none.

## Getting the image

`update_brand` takes the logo one of two ways, and which one you use depends
only on where the image is:

- **`{ objectId }`** — an image that already exists in Radical Tempo. This is
  the path for an image the user **attached to the conversation**: an attachment
  arrives as an image link, and its object id is what goes here. It is also the
  path for an image already in the brand — use `search_images` on the brand to
  find one.
- **`{ image }`** — an image that lives outside Radical Tempo: an HTTP/HTTPS
  URL, or a `data:<mimeType>;base64,<data>` URI. Supported types are JPEG, PNG,
  WebP, and BMP.

If the user attached an image, don't re-fetch or re-encode it — pass the object
id you were given. Re-uploading the same bytes just creates a second copy of the
image.

## Steps

1. Resolve the brand (above) and the image (above).
2. **Confirm before you apply, unless the user was already explicit.** Setting a
   logo replaces the one the brand has now, and it shows up everywhere the brand
   does. "Use this as my logo" with an attached image is explicit — just do it.
   Anything you had to infer — which of several attached images, which brand,
   which of the images `search_images` returned — needs a short confirmation
   first (`presentOptions` when there's a set to choose from, otherwise a plain
   question). Never pick a logo on your own.
3. Call `update_brand` with the brand id and `logo`. A call that changes only
   the logo needs no `expectedUpdatedAt`. It returns a link to the logo image
   plus a URI for the updated brand.
4. Tell the user it's set, and show the logo image so they can see what landed.

## Don't

- **Don't generate a logo unless the user asked you to.** This skill is for an
  image the user already has. If they want you to _make_ one, that's image
  generation (`generate_images`) first — and then they still choose before you
  apply it.
- **Don't touch anything else about the brand.** A logo request is not license
  to rename the brand or edit its notes.
- **Don't set a logo the user didn't pick.** If several images are in play, ask
  which one.
