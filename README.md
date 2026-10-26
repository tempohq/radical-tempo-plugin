# Radical Tempo

Radical Tempo turns long-form video into clips, covers, and posts, and reads
back how they perform. This plugin connects Claude to your Radical Tempo
account, so you can work on a brand's video library from a chat.

## What you can do

- Bring a video in from an upload, Google Drive, Dropbox, or AWS S3, and follow
  its processing.
- Find a video or a clip by what is in it, its title, or its metadata.
- Generate clips from a video, with hook text and descriptions written in the
  brand's voice, in a vertical, square, or widescreen shape.
- Reframe a clip to another aspect ratio, or trim it.
- Create a cover image for a post.
- Publish a post now or on a schedule to YouTube, TikTok, Instagram, Facebook,
  LinkedIn, or X. Publishing always proposes first and asks for your approval.
- Review performance: views, watch time, retention, and which clips won or lost
  against similar ones.
- Save a memory about a brand, so later work keeps its context.

Try asking:

- "Which of my posts did best over the last 30 days, and why?"
- "Find the part of my videos where we talk about procrastination."
- "Make vertical clips from my latest video."

## Before you start

- You need a Radical Tempo account. Sign up at
  [radicaltempo.com](https://radicaltempo.com).
- The first time Claude uses a Radical Tempo tool, it opens Radical Tempo's
  sign-in page in your browser. Sign in and approve access. You never paste an
  API key.
- Claude can do what your account can do in the app, on the brands your account
  can reach.

## What the plugin contains and connects to

- **One MCP server**, at `https://mcp.radicaltempo.com/mcp`. It's the only place
  the plugin sends data. Each request carries the sign-in token from your
  approval, and Radical Tempo reads and writes only the brands your account can
  reach.
- **Skills**: text files that explain Radical Tempo's features, its terms, and
  how to carry out multi-step tasks such as setting a post's cover.
- **An agent**: a text file that describes how to work with a creator's content.

The plugin runs no code on your machine. It has no hooks, scripts, or local
servers.

Analytics come from YouTube, TikTok, Instagram, and Facebook. LinkedIn and X
don't report analytics.

## Support

- Setup guide:
  [Connect an AI tool](https://app.radicaltempo.com/docs/connect-an-ai-tool)
- Email: [support@radicaltempo.com](mailto:support@radicaltempo.com)
- [Privacy policy](https://radicaltempo.com/privacy-policy)
- [Terms of use](https://radicaltempo.com/terms-of-use)
