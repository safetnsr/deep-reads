---
title: "Why 2023 Was AI Video's Breakout Year, and What to Expect in 2024"
author: Justine Moore
date: 2024-01-02
url: https://a16z.com/why-2023-was-ai-videos-breakout-year-and-what-to-expect-in-2024/
source: a16z
adapted_from: https://twitter.com/venturetwins/status/1741147864498397328
---

# Why 2023 Was AI Video's Breakout Year, and What to Expect in 2024

*adapted from [a thread from a16z partner Justine Moore](https://twitter.com/venturetwins/status/1741147864498397328) on X.*

2023 was a breakout year for AI video. at the start of the year, no public text-to-video models existed. just 12 months later, dozens of video generation products are in active use, with millions of users worldwide creating short clips from text or image prompts.

these products are still relatively limited — most generate 3 to 4 second videos, the outputs tend to be mixed in quality, and things like character consistency have yet to be cracked. we're still far from being able to create a Pixar-level short film with a single text prompt (or even multiple prompts!).

however, the progress we've seen in video generation over the past year suggests that we're in the early innings of a massive transformation — similar to what we saw in image generation. we're seeing continued improvements in text-to-video models, as well as offshoots like image-to-video and video-to-video gaining steam.

to help understand this explosion in innovation, we've tracked the biggest developments so far, companies to keep an eye on, and the remaining underlying questions in this space.

## where can you generate AI video today?

### products

we have been tracking 21 public products so far this year. while you've likely heard of Runway, Pika, Genmo, and Stable Video Diffusion, there's a long list of others to discover.

most of these products come from startups — many of which begin with Discord bots, which have a few advantages:

- you don't need to build your own consumer-facing interface, and can focus exclusively on model quality
- you can tap into Discord's base of 150 million monthly active users for distribution — especially if you [get featured](https://discord.com/servers) on the platform's "Discover" page
- public channels provide an easy way for new users to get inspiration on what to create (by seeing what others generate) and provide social proof for the product

however, we're starting to see more video products build their own websites or even mobile apps, especially as they mature. while Discord provides a good launchpad, it's limited in terms of the workflow you can add on top of pure generation, and teams have very little control over the consumer experience. it's also worth noting that there's a large percentage of the population who don't use Discord and may find the interface confusing or don't return to it frequently.

### research and big tech

where are Google, Meta, and others? they're conspicuously missing from the list of public products — though you may have seen their splashy posts announcing models like Meta's [Emu Video](https://ai.meta.com/blog/emu-text-to-video-generation-image-editing-research/), Google's [VideoPoet](https://sites.research.google/videopoet/) and [Lumiere](https://lumiere-video.github.io), and ByteDance's [MagicVideo](https://magicvideov2.github.io/).

thus far, the big tech companies — with the exception of Alibaba — have chosen to not publicly release their video gen products. instead, [they're publishing papers](https://docs.google.com/spreadsheets/d/1FZqG6ESvUzfkZB7AOW5YE9pOYOUfjzw7h6XLREvOJo8/edit?pli=1#gid=514547156) on various forms of video generation and dropping demo videos without announcing if or when their models will ever be public.

these companies all have massive distribution advantages, with billions of users on their products. why not drop their video models, when their demos look strong and they could have the opportunity to capture meaningful market share in this emerging category?

it's important to remember that these companies move slowly. most still haven't released text-to-image products, though Instagram [shipped](https://www.engadget.com/instagram-now-offers-ai-generated-backgrounds-on-stories-115054259.html) an AI background generator for Stories late last year, and TikTok has been quietly launching AI filters. legal, safety, and copyright concerns often make it difficult to convert research into products at these companies and delay rollout — giving newcomers the chance to gain a first-mover advantage.

## what's next for AI video?

if you've ever used one of these products, you know that there's much to be improved before AI video is ready for primetime. getting a "magic moment" where a model generates a beautiful clip that matches your prompt is possible, but relatively rare. it's more common that you need to hit re-generate a few times and crop or edit the output to get professional-grade clips.

most of the companies in this space are focused on tackling a few core (and as of yet unsolved) issues:

- **control** — can you control both what happens in a scene (e.g., if you prompt "man walking forward," is the movement as described?) and how the "camera" moves? on the latter point, a number of products have added features that allow you to zoom or pan the camera or even add special effects.

  the former point — is the movement as described? — has been more difficult to tackle. it's an underlying model quality issue (does the model understand and can it execute on your prompt), though some companies are trying to give users more control pre-generation. Runway's motion brush is a good example of this, as it allows you to highlight specific areas of an image and determine how they move.

- **temporal coherence** — how can you make characters, objects, and backgrounds stay consistent between frames and not morph into something else or warp? this is a very common problem across all of the publicly available models. if you see a temporally coherent video today that's longer than a few seconds, it's likely video-to-video, taking a video and transforming the style with something like [AnimateDiff prompt travel](https://stable-diffusion-art.com/animatediff-prompt-travel-video2video/).

- **length** — can you make clips beyond a couple of seconds? this is highly related to temporal coherence. many companies limit the length of the videos you can generate because they can't ensure any kind of consistency after a few seconds. if you see a long-form AI video, you'll notice it's comprised of a bunch of short clips and required dozens, if not hundreds, of prompts!

## outstanding questions

it feels like AI video is at GPT-2 level. we've made big strides in the last year, but there's still a way to go before everyday consumers are using these products on a daily basis. when will the "ChatGPT moment" arrive for video? there's no broad consensus among researchers and founders in the field, and a few questions still to be answered:

- **does the current diffusion architecture work for video?** today's video models are diffusion-based: they essentially generate frames and try to create temporally consistent animations between them. they have no intrinsic understanding of 3D space and how objects should interact, which explains the warping / morphing. for example, it's not uncommon to see a person walking down a street in the first half of a clip and then melting into the ground in the second — the model has no concept of a "hard" surface. it's also difficult (if not impossible) to generate the same clip from a different angle due to the lack of 3D conceptualization of a scene.

  some people believe that video models don't fundamentally need an understanding of 3D space. if they're trained on enough quality data, they will be able to learn the relationships between objects and how to represent scenes from different angles. others are convinced that these models will need a 3D engine to generate temporally coherent content, particularly beyond a few seconds.

- **where will the quality training data come from?** it's harder to train video models than other content modalities, largely due to the fact that there's not as much quality, labeled training data for these models to learn from. language models are often trained on public datasets like [Common Crawl](https://commoncrawl.org/), while image models are trained on labeled datasets (text-image pairs) like [LAION](https://laion.ai/blog/laion-5b/) and [ImageNet](https://image-net.org/).

  video data is harder to come by. while there is no shortage of publicly accessible video on platforms like YouTube and TikTok, it's not labeled and may not be diverse enough. the "holy grail" of video data would likely come from studios or production companies, which have long-form videos shot from multiple angles that are accompanied by scripts and directions. however, it's TBD whether they'll be willing to license this data for training.

- **how will these use cases segment between platforms / models?** what we've seen across almost every content modality is that one model doesn't "win" for all use cases. for example, Midjourney, Ideogram, and DALL-E all have distinct styles and excel at generating different types of images.

  we expect that video will have a similar dynamic. if you test today's text-to-video and image-to-video models, you'll notice that they're good at different styles, types of movement, and scene composition. the products built around these models will likely further diverge in terms of workflow and serve different end markets. and this doesn't even include adjacent products that aren't doing pure text-to-video, but are tackling things like animated human avatars (e.g., [HeyGen](https://app.heygen.com/)), VFX (e.g., [Wonder Dynamics](https://wonderdynamics.com/)), and video-to-video (e.g., [DomoAI](https://domoai.app/)).

- **who will own the workflow?** beyond pure video generation, making a good clip or film typically requires editing, especially in the current paradigm, where many creators are using video models to animate photos created on another platform. it's not uncommon to see a video that started as a Midjourney image, got animated on Runway or Pika, and upscaled in Topaz. creators will then take the video to an editing platform like Capcut or Kapwing and add a soundtrack and voiceover (often generated on other products like Suno and ElevenLabs).

  it doesn't make sense to go back-and-forth between so many products. we expect to see the video generation platforms start to add some of these features themselves. however, we're also bullish on an AI-native editing platform that makes it easy to generate content across modalities from different models in one place and piece these things together.

if you're working on something here, reach out! we'd love to hear from you. you can find me on X at [@venturetwins](https://twitter.com/venturetwins), or email me at jmoore@a16z.com
