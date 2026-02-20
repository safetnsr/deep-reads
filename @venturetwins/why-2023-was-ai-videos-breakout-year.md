---
title: "Why 2023 Was AI Video's Breakout Year, and What to Expect in 2024"
author: Justine Moore
date: 2024-01-01
url: https://a16z.com/why-2023-was-ai-videos-breakout-year-and-what-to-expect-in-2024/
source: a16z
adapted_from: https://twitter.com/venturetwins/status/1741147864498397328
---

# Why 2023 Was AI Video's Breakout Year, and What to Expect in 2024

*Adapted from a thread on X by Justine Moore.*

2023 was a breakout year for AI video. At the start of the year, no public text-to-video models existed. Just 12 months later, dozens of video generation products are in active use, with millions of users worldwide creating short clips from text or image prompts.

These products are still relatively limited — most generate 3 to 4 second videos, the outputs tend to be mixed in quality, and things like character consistency have yet to be cracked. We're still far from being able to create a Pixar-level short film with a single text prompt.

However, the progress we've seen in video generation over the past year suggests that we're in the early innings of a massive transformation — similar to what we saw in image generation.

## Where can you generate AI video today?

### Products

We have been tracking 21 public products so far this year. While you've likely heard of Runway, Pika, Genmo, and Stable Video Diffusion, there's a long list of others to discover.

Most of these products come from startups — many of which begin with Discord bots, which have a few advantages:

- You don't need to build your own consumer-facing interface, and can focus exclusively on model quality
- You can tap into Discord's base of 150 million monthly active users for distribution
- Public channels provide an easy way for new users to get inspiration on what to create

However, there's a large percentage of the population who don't use Discord and may find the interface confusing. We're starting to see more video products build their own websites or even mobile apps, especially as they mature.

### Research and Big Tech

Google, Meta, and others are conspicuously missing from the list of public products — though they've published papers on Meta's [Emu Video](https://ai.meta.com/blog/emu-text-to-video-generation-image-editing-research/), Google's [VideoPoet](https://sites.research.google/videopoet/) and Lumiere, and ByteDance's MagicVideo.

Thus far, the big tech companies — with the exception of Alibaba — have chosen to not publicly release their video gen products. Legal, safety, and copyright concerns often make it difficult to convert research into products — giving newcomers the chance to gain a first-mover advantage.

## What's next for AI video?

Most companies in this space are focused on tackling a few core unsolved issues:

- **Control** — can you control both what happens in a scene and how the "camera" moves? Runway's motion brush is a good example of giving users more control pre-generation.

- **Temporal coherence** — how can you make characters, objects, and backgrounds stay consistent between frames? This is a very common problem across all publicly available models.

- **Length** — can you make clips beyond a couple of seconds? This is highly related to temporal coherence. Many companies limit the length of the videos you can generate because they can't ensure any kind of consistency after a few seconds.

## Outstanding questions

It feels like AI video is at GPT-2 level. We've made big strides in the last year, but there's still a way to go before everyday consumers are using these products on a daily basis.

- **Does the current diffusion architecture work for video?** Today's video models are diffusion-based: they essentially generate frames and try to create temporally consistent animations between them. They have no intrinsic understanding of 3D space. Some believe models don't fundamentally need an understanding of 3D space — if trained on enough quality data, they can learn relationships between objects. Others are convinced these models will need a 3D engine.

- **Where will the quality training data come from?** Video data is harder to come by. While there is no shortage of publicly accessible video on YouTube and TikTok, it's not labeled. The "holy grail" of video data would likely come from studios or production companies, which have long-form videos shot from multiple angles accompanied by scripts and directions.

- **How will use cases segment between platforms / models?** What we've seen across almost every content modality is that one model doesn't "win" for all use cases. We expect that video will have a similar dynamic — products will further diverge in terms of workflow and serve different end markets.

- **Who will own the workflow?** Beyond pure video generation, making a good clip typically requires editing. It's not uncommon to see a video that started as a Midjourney image, got animated on Runway or Pika, and upscaled in Topaz. We expect to see video generation platforms start to add some of these features themselves. We're also bullish on an AI-native editing platform that makes it easy to generate content across modalities in one place.
