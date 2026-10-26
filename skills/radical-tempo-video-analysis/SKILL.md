---
name: radical-tempo-video-analysis
description: >
  How to evaluate video content quality across 6 dimensions — pacing, opening
  frame, headline text, format, duration, and edit quality. Includes scoring
  rubrics and platform-specific targets.
---

# Video Analysis

## Prefer the stored content score

If the video's `asset` resource carries a `content_score`, that is Tempo's own
quality verdict — report its `overall` (0–100) as _the_ score and build your
analysis around it instead of presenting a competing overall number of your own.
Its `pillars` (`watch` / `stay` / `reach`) tell you where the clip is strong or
weak; use the six dimensions below to explain _why_ and what to change, leading
with the weakest pillar. A `content_score` whose `overall` is `null` means the
clip was scored but lacks enough signal for a number yet — say that plainly
rather than reporting a zero. Only when there is no `content_score` at all
should you produce a standalone assessment from the dimensions below.

## Review Dimensions

Evaluate content across 6 dimensions. For each, produce a score (0-10),
reasoning with specific data references, and concrete suggestions.

### 1. Opening Frame (targets: watch rate)

The first 1-3 frames determine scroll-stop rate. Evaluate:

- **Motion:** Is there movement in the first 0.5s? Static frames lose.
- **Framing:** Close-up or mid-shot outperforms wide/establishing shots 3:1.
- **Text overlay:** Bold, high-contrast overlay text improves hook rate ~40%.
- **Face presence:** Faces in the opening frame increase engagement.
- **Visual intrigue:** Unexpected visuals, bright colors, or pattern interrupts.

To assess: look at the visual event log for the first 3 seconds. Note the
scene_type and energy level. If transcript is available, check if there's speech
starting within 1.5s.

| Score | Meaning                                                  |
| ----- | -------------------------------------------------------- |
| 9-10  | Strong scroll-stopper: motion + face + text + intrigue   |
| 7-8   | Good hook: has 2-3 elements, minor improvements possible |
| 5-6   | Adequate: functional but won't stand out in a feed       |
| 3-4   | Weak: static wide shot, no text, delayed start           |
| 1-2   | Poor: blank/black frame, off-topic opening, confusing    |

### 2. Headline Text (targets: watch rate)

The hook — first spoken words and/or text overlay in the first 5 seconds.

- **Strong hooks:** Questions, bold claims, specific numbers, pattern
  interrupts, "Here's why..." / "The secret to..."
- **Weak hooks:** "Hey guys", "So today I want to...", generic greetings, no
  speech for 3+ seconds
- **Text overlays:** Should reinforce or preview the value proposition

To assess: check the transcript for the first 3-5 seconds of speech. Check
composition/metadata for text overlays.

| Score | Meaning                                                |
| ----- | ------------------------------------------------------ |
| 9-10  | Compelling: creates curiosity, promises specific value |
| 7-8   | Good: functional hook with some specificity            |
| 5-6   | Generic: standard intro, doesn't differentiate         |
| 3-4   | Weak: slow start, greeting-based, no value proposition |
| 1-2   | No hook: silence, off-topic, or confusing opening      |

### 3. Format (targets: watch rate)

Aspect ratio and orientation for the target platform.

| Platform          | Ideal Format       | Notes              |
| ----------------- | ------------------ | ------------------ |
| TikTok            | 9:16 (1080x1920)   | Vertical mandatory |
| Instagram Reels   | 9:16               | Vertical mandatory |
| YouTube Shorts    | 9:16               | Vertical mandatory |
| YouTube long-form | 16:9 (1920x1080)   | Landscape standard |
| LinkedIn          | 16:9 or 1:1        | Square acceptable  |
| Facebook          | 16:9, 9:16, or 1:1 | Platform-flexible  |

To assess: check asset metadata for resolution/dimensions. Compare against
target platform requirements.

### 4. Duration (targets: average view duration)

Length appropriateness for platform and content type.

| Platform          | Ideal Duration | Virality Sweet Spot |
| ----------------- | -------------- | ------------------- |
| TikTok            | 15-60s         | 21-34s              |
| Instagram Reels   | 15-30s         | 15-22s              |
| YouTube Shorts    | 30-58s         | 40-55s              |
| YouTube long-form | 8-15 min       | Depends on niche    |
| LinkedIn          | 30-90s         | 45-75s              |
| Facebook Reels    | 15-30s         | 15-22s              |

To assess: compare asset duration against platform targets. Also compare against
the brand's top-performing content durations from benchmarks.

### 5. Edit Quality (targets: average view duration)

Pacing consistency and dead spot analysis.

- **Energy dips:** Extended low-energy sections (>5s) predict viewer drop-off
- **Silence gaps:** Gaps >1.5s feel like dead air on short-form, >3s on
  long-form
- **Awkward transitions:** Abrupt cuts without narrative purpose
- **Dead spots:** Sections with no visual change AND no speech

To assess: analyze the visual event log for energy levels over time. Check
transcript for silence gaps (gaps between segments). Identify sections where
both visual energy is "low" and there's no speech.

| Score | Meaning                                                              |
| ----- | -------------------------------------------------------------------- |
| 9-10  | Tight edit: consistent energy, no dead spots, purposeful transitions |
| 7-8   | Good: minor pacing issues, 1-2 brief dead spots                      |
| 5-6   | Average: noticeable dead spots, 2-3 silence gaps                     |
| 3-4   | Below average: frequent dead spots, energy valleys                   |
| 1-2   | Poor: multiple dead spots, awkward pacing throughout                 |

### 6. Pacing (targets: average view duration)

Speech rate and visual rhythm.

| Platform          | Optimal WPM | Style                                       |
| ----------------- | ----------- | ------------------------------------------- |
| TikTok / Reels    | 160-200     | Fast, energetic, minimal pauses             |
| YouTube long-form | 130-170     | Conversational, brief pauses OK             |
| LinkedIn          | 110-140     | Professional, measured, pauses add gravitas |

To compute WPM: count words in transcript, divide by duration in minutes. For
windowed analysis, compute WPM per 10-second window to find pacing variation.

Also evaluate:

- **Cut frequency:** How often do visual scenes change? Higher for short-form.
- **Energy curve:** Does energy build or stay flat? Best content has peaks and
  valleys.
- **First-word timing:** How quickly does speech start? <1s is ideal for
  short-form.

## Scoring Guidelines

- **Always reference specific data.** "Your WPM is 142, which is within
  LinkedIn's ideal range" not "Your pacing is good."
- **Compare to benchmarks.** "Your 47s duration is 19s longer than your brand's
  top TikTok performers (avg 28s)."
- **Prioritize by impact.** Lead suggestions with the dimension that would
  improve performance most.
- **Be actionable.** "Trim the 7-second low-energy section at 0:25-0:32" not
  "Consider tightening the edit."
- **Handle missing data.** If transcript is unavailable, skip pacing/headline
  dimensions and note why. Don't guess.
