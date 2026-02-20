---
title: "State of Generative Media Volume 1"
author: fal.ai (featuring insights from Justine Moore + Olivia Moore, a16z)
date: 2026-01-01
url: https://fal.ai/gen-media-report-volume-1
source: fal.ai
---

# State of Generative Media Volume 1

*note: this is the fal.ai State of Generative Media report (volume 1, covering 2025), which Justine Moore contributed to and promoted via a16z. the a16z URL https://a16z.com/the-state-of-generative-media-report/ returns 404. content sourced directly from https://fal.ai/gen-media-report-volume-1*

---

from e-commerce teams to visual designers, anyone can now generate hundreds of production-ready images in minutes. a few years ago, this volume would have required thousands of photographers, studios, and production staff. the cost structure that has governed e-commerce and other digital verticals has shifted. traditional barriers around content production are evaporating, due to generative media infrastructure.

the primary impact of breakthroughs in generative technology is the expansion of creative potential for users and builders alike. entertainment applications initiated adoption of generative media, but in 2025 production applications (e.g. e-commerce, advertising, creative studios) drove scale and by year's end 88% of organizations deployed AI in at least one business function.

as Jeffrey Katzenberg articulated:

> "it's the democratization of storytelling at a level that has never happened in the existence of humankind."

this report examines how generative technology and trends accelerated in 2025. these insights draw from survey data collected across a diverse range of organizations and individual users, industry leaders who spoke at the Generative Media Conference in October, and the most impactful market research covering the changing landscapes in generative technology.

## technical advances in 2025

in 2025, video generation models delivered outputs passing visual turing tests for untrained observers. technical capabilities advanced greatly across image, video, and audio generation in 2025, with different modalities reaching similar stages of evolution.

while individual launches defined key inflection points, the broader story of 2025 was scale. model releases were no longer isolated breakthroughs — they became continuous across modalities:

| modality | new models on fal platform |
|---|---|
| video | 450 |
| image | 406 |
| audio | 59 |
| 3D | 35 |
| speech | 35 |
| **total** | **985** |

### image generation performance

image generation transformed experimental workflows into production pipelines in 2025. key milestones:

| timeline | model | company | impact |
|---|---|---|---|
| aug 2024 | Flux.1 Dev | Black Forest Labs | shattered performance ceiling, superior prompt adherence |
| mar 2025 | GPT Image 1 | OpenAI | true multimodal image generation, defining cultural moment |
| aug 2025 | Qwen Image Edit | Alibaba | open-source image editing with LoRA |
| aug 2025 | Nano Banana v1 | Google DeepMind | consumer accessibility without technical proficiency |

[Flux Kontext](https://fal.ai/models/fal-ai/flux-kontext) was the first dedicated image editing model achieving character consistency, style transfer, and localized editing at near-real-time speeds. [Qwen Image Edit](https://fal.ai/models/fal-ai/qwen-image-edit) arrived as one of the first open-source image editing models with LoRA capability, democratizing fine-tuning for developers without enterprise compute budgets.

Google DeepMind's [Nano Banana (v1)](https://fal.ai/models/fal-ai/nano-banana) proved that users without technical proficiency could generate production-quality content through natural language. ByteDance's [Seedream 4.0](https://fal.ai/models/fal-ai/bytedance/seedream/v4/text-to-image) offered faster generation at lower computational costs while maintaining comparable output quality.

### video generation capabilities

eight major video generation releases in ten months produced rapid competitive iteration. performance leadership changed hands multiple times as companies shipped at speeds uncommon in enterprise software.

| timeline | model | company | key innovation |
|---|---|---|---|
| dec 2024 | Veo 2 | Google DeepMind | physically accurate video, quality benchmark |
| apr 2025 | Kling 2.0 | Kuaishou | first-frame-last-frame narrative control |
| may 2025 | Veo 3 | Google DeepMind | native audio generation with video output |
| jul 2025 | MirageLSD | Decart | live-stream diffusion, real-time generation |
| sep 2025 | Sora 2 | OpenAI | scene-aware multi-modal generation |

[Veo 3](https://fal.ai/models/fal-ai/veo3/image-to-video) in May 2025 enabled rapid-turnaround workflows for social media and content channels. the model's combination of speed and quality with native audio generation let content creators generate, iterate, and publish within hours rather than days.

MirageLSD from Decart in July 2025 generated video frame-by-frame in real-time through live-stream diffusion. the approach opened applications in live streaming and interactive entertainment that batch-processing models could not address.

[Sora 2](https://fal.ai/models/fal-ai/sora-2/image-to-video/pro) in September 2025 combined native audio with excellent multi-shot generation in a single output, enabling coherent scene transitions without manual editing.

major releases arrived every 4-6 weeks in 2025, with performance improvements expanding viable use cases across entertainment, marketing, and education.

### voice, music and audio synthesis

audio became one of the most production-ready generative media categories in 2025. [ElevenLabs Turbo v2.5](https://fal.ai/models/fal-ai/elevenlabs/tts/turbo-v2.5) is among the most widely used low-latency text-to-speech systems (~250-300ms), while [MiniMax Speech-02](https://fal.ai/models/fal-ai/minimax/speech-02-turbo) (May 2025) achieved 99% human voice similarity across 32 languages. as one generative voice user noted: "sub-300ms is table stakes for voice AI. above that, the experience breaks."

Eleven Music by ElevenLabs (August 2025) was the first major AI music model trained entirely on licensed data, establishing opt-in participation and 50/50 royalty splits for artists. Suno drove rapid consumer adoption with high-quality, prompt-based song generation.

### 3D modeling and vision language

3D generation matured from experimental outputs to production assets in 2025, compressing modeling timelines from weeks to minutes. Tencent released Hunyuan 3D 2.0 in January 2025. Deemos launched [HyperRodin](https://fal.ai/models/fal-ai/hyper3d/rodin) Gen 1.5 in April 2025 with 4 billion parameters. Meshy launched version 5 in July 2025 and v6 preview in October, recognized by Andreessen Horowitz in their game developer survey.

Tripo 3.0 in September 2025 served over 3 million creators and 700+ enterprises. Microsoft's TRELLIS 2, released in December, can generate high-resolution assets in under 3 seconds, creating opportunities for real-time applications.

### world models

world models simultaneously generate and simulate interactive 3D environments where all modalities converge. DeepMind announced Genie 2 in December 2024, generating playable 3D environments from single image prompts. users and AI agents navigate using keyboard and mouse, with the model simulating action consequences within physically consistent spaces.

Fei-Fei Li's World Labs launched Marble in November 2025, the first commercial world model product. Marble generates persistent and downloadable 3D environments from text, images, videos, or panoramas. the platform outputs environments as Gaussian splats, meshes, or videos, integrating into Unity, Unreal Engine, and VR headsets.

## enterprise adoption and ROI

enterprise generative AI adoption accelerated through 2025, with adoption rates varying by industry vertical and use case.

- generative media adoption: **89% personal** vs. **57% organizational**
- image generation: **44%** in production workflows vs. 39% for video
- video generation: **62% personal** adoption vs. **32% organizational**

| industry vertical | adoption rate | primary use cases |
|---|---|---|
| advertising | 56% | rapid creation of campaign visuals, banner ads, social graphics at scale |
| entertainment, media & creative | 43% | storyboarding, pre-visualization, special effects, short-form promotional clips |
| creative software or tools | 31% | design platform, creative software, video/image editing tools |
| educational and training content | 30% | interactive learning videos, animated explainers |
| retail & e-commerce | 19% | automated product photography, catalog images, virtual try-on mockups |
| architecture & real estate | 8% | 3D renders, staging visuals, and concept imagery for developments |

### enterprise ROI

return on generative media investment materialized faster than expected:

| ROI status | percentage of organizations |
|---|---|
| already profitable | 34% |
| expecting returns within 12 months | 31% |
| **total achieving ROI ≤ 12 months** | **65%** |

74% of companies report their initiatives meet or exceed ROI expectations.

organizations achieving generative scale made structural changes beyond deploying new technology. 43% redesigned workflows and production pipelines, 33% invested in staff training and upskilling, and 30% allocated dedicated budget for media generation infrastructure.

### infrastructure selection

decision criteria for organizations prioritizing:

| decision criterion | organizations prioritizing |
|---|---|
| cost optimization | 58% |
| model availability | 49% |
| generation speed | 41% |
| reliability and uptime | 37% |
| data security and compliance | 34% |

infrastructure provider adoption across image and video generation workloads:

| provider | image generation APIs | video generation APIs |
|---|---|---|
| fal.ai | 50% | 44% |
| Google AI Studio | 33% | 56% |
| OpenAI | 39% | — |
| Replicate | 15% | 22% |

### model orchestration and management

enterprise production deployments use a median of **14 different models**. the belief that single "omni models" would handle all generative tasks proved incorrect. production deployments revealed that task-specific optimization consistently outperformed general-purpose approaches for specialized applications.

## what's next

the trajectory of generative media development through 2026+ is clear. three major themes will dominate:

1. **multimodal advancements** (e.g. world models)
2. **infrastructure optimization**
3. **democratization of creative tools**

expertise will become orchestration rather than execution. taste becomes scarce, while capability becomes abundant. the durable competitive moats will belong to teams that understand how to best deploy generative media, now that generating professional media is easier than ever.
