---
name: radical-tempo-youtube-studio-import
domain: product
description: >-
  Runbook for importing a brand's published YouTube videos, or only their
  YouTube Studio metrics, from YouTube Studio with a browser tool: which import
  to run, the checks before any download, one tool batch per video, a plan for
  runs of more than 10 videos, the upload script, and recovery from a dropped
  download or an expired upload URL. Load before the first call to
  `import_youtube_studio_videos` or `import_youtube_studio_metrics`. Triggers:
  import from YouTube, import my YouTube videos, YouTube Studio, bring in my
  channel, back catalog, import my latest videos, Studio metrics, refresh
  YouTube metrics, stayed to watch, shown in feed, retention, audience
  retention, Content tab export.
---

# YouTube Studio Import

Each tool description is the full contract for its calls. This skill adds what
the descriptions leave out: the order to work in, how to keep a long run small,
and what to do when a step fails.

## Which import to run

- **Videos with metrics** (`import_youtube_studio_videos`): the user wants
  videos from their channel in Radical Tempo, e.g. "import my latest videos",
  "bring in my back catalog". Each video becomes an asset with its published
  YouTube post, its audience retention, and the channel's Studio metrics.
  Processing costs credits, as an upload does.
- **Metrics only** (`import_youtube_studio_metrics`): the user wants fresh
  Studio numbers for posts the brand already has, e.g. "refresh my YouTube
  metrics", "pull Stayed to watch". One Content-tab export covers every post of
  the channel. No credits.
- If your tool list has no `import_youtube_studio_metrics`, Studio metrics
  arrive only with a video import. Say so. Never start a video import only for
  the metrics.
- If you have no browser tool, give the user the Studio steps from the tool's
  description, one video at a time, and ask for each saved file's path. The rest
  of this skill still applies.

## Before any download

1. Resolve the brand with `list_brands`.
2. Run call 1 of the import. It refuses a brand with no connected YouTube
   channel, and it names the connected channel by title and ID.
3. Open YouTube Studio and compare the channel it shows with the channel call 1
   named. If they differ, stop and tell the user. Never download from another
   channel.
4. Check that every video the user named is on that channel. A video on any
   other channel cannot be imported, even one the brand appears in, e.g. a talk
   an event's parent channel published. Tell the user which videos are on
   another channel before you download the rest.
5. For a video import, read the "already has" list call 1 answers. Download only
   the rest.
6. A video the user uploaded by hand, whose post was never published through
   Radical Tempo, is not on that list. The import adds a second asset for it.
   Say so before you download. The user can archive the hand-uploaded asset
   afterward.

## Download each video in one tool batch

Follow the Studio steps in `import_youtube_studio_videos`'s description. Put
each video's steps in one browser tool batch.

- Open the video's edit page. Click the Options menu at the top right. Check
  that the menu opened. Click Download.
- Wait until the file is on disk before the page navigates. A navigation right
  after an Export or Download click drops the download.
- Open the retention URL. Click Export. Check that the menu opened. Click
  `.csv`. Wait until the zip is on disk.
- Studio names each download by the video's title. In your shell, move the
  newest file in the download folder into the video's folder, renamed as the
  description says, before the next download.
- Write `metadata.json`, then `_complete.json` last.
- Never click Download from a page script. A scripted click reports success and
  starts no download.
- Never use the Options menu of a row in the Content list. A click there can
  miss the menu and land in another video's details page.

A retention export with only a header row is a valid export: Studio has no
retention data for a video with few views. Keep it.

## Runs of more than 10 videos

- Do call 1 once, for every video, yourself.
- Split the videos to download into batches of 10. Give each batch to one
  subagent, one batch after another, never 2 at once: they share the browser and
  the download folder.
- Give each subagent the run folder path, its 10 video IDs, the `metadata.json`
  template from call 1, and the Studio steps. Ask it to report each video as
  done or failed, with the reason.
- Download the Content report once, yourself, after the last batch.
- Write `run.json` yourself, with every failed video in `failed`.
- At most 50 videos go in one import. For more, run another import.

## Upload and finish

- Make call 2 only once every download is done.
- Run the script as soon as call 2 answers. Its upload URLs end with your
  sign-in to Radical Tempo, which can be about 15 minutes, sooner than the hour
  its text gives.
- Pass the one line the script prints, unchanged, as call 3's arguments.
- Wait for the task in the same turn. Report each video as imported, skipped
  because the brand already has it, or failed, with the reason the result gives.
- If call 2 says the brand's credits are short, tell the user before call 3,
  with the `process_asset` step it names.

## When a step fails

- **A download did not land:** open the same page again and repeat that one
  step. Never move on with a folder that lacks `_complete.json`.
- **Studio shows a sign-in page or another channel:** stop and ask the user to
  sign in to the connected channel.
- **The script fails with `403`:** the upload URLs expired. Make call 2 again
  with the same `importId` and `files`, then run the new script.
- **The script stops at another failed file:** run it again, or make call 2
  again when its text says to. A file sent twice replaces the first copy.
- **Call 3 says nothing was uploaded:** the script did not reach its end. Run it
  to the end, then make call 3 again.
- **The session ended mid-run:** start again from call 1. The brand's
  already-imported videos are skipped, so a second run is safe.

## Metrics only

- Call 1 answers an upload command and names the channel.
- Open the Content tab's Advanced mode from the Content tab, never from a
  shortened URL, which renders a blank page.
- Set the period to Lifetime. Add "Stayed to watch" and "Shown in feed". Export
  the `.csv`.
- Upload the zip unchanged with the command. Never unzip, edit, or recompress
  it.
- Make call 2 with the `uploadId`. Wait for the task in the same turn. Report
  how many posts got metrics and how many rows of another channel were skipped.
