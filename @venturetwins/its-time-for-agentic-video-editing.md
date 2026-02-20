---
title: "It's Time for Agentic Video Editing"
author: Justine Moore
date: 2026-01-28
url: https://a16z.com/its-time-for-agentic-video-editing/
source: a16z
---

# It's Time for Agentic Video Editing

2025 was the year of video. AI-generated ads went mainstream. launch videos from seed stage startups got millions of views. video podcasts and interviews exploded.

what you didn't see was all the work behind the scenes. cutting 90 minutes of footage into a three-minute short. correcting lighting and audio in post-production because you couldn't nail it in the shoot. searching for the right music and sound effects.

a common rule of thumb in video production is that you'll spend **80% of your time & energy on editing, and 20% on filming** (or now, generating). crafting compelling video is typically a long and tedious process — and few people have the "taste" to do it right. there's a significant barrier to entry.

we now have the technology to hand over some of this work to AI agents, which can help us produce both filmed and generated content. vision models can watch and comprehend massive amounts of video footage. agents can analyze, plan, and use editing tools on your behalf. and we have enough training data to teach models what makes a video great.

video agents will blow out the supply curve for quality video — the kind of content that requires days (or weeks) from professional video editors today. **what Cursor did for coding, these agents will do for video production.**

there's immense demand for agents that give anyone the skills (and taste) of a professional video editor. so why don't these products already exist?

## what's unlocked progress

a few recent developments have made agentic video editing possible:

- **vision models can now process large amounts of video.** you have to understand video before you can edit it. this is a non-trivial challenge — there's a lot of information to process, even in a short clip. we've seen a lot of progress with recent LLMs like Gemini 3, GPT-5.2, Molmo 2, and Vidi2, which are inherently multimodal and have longer context windows. Gemini 3 can now process up to an hour of video — you can upload it as input and ask the model to generate timestamped labels, find a specific moment, or summarize what's happening.

- **models can now use tools.** AI video editors need to be able to take action — not just describe what's happening or suggest changes. we're starting to see meaningful progress around LLMs as real agents that can use tools. one of the best examples is [Claude using Blender](https://x.com/sidahuj/status/1899460492999184534) (a notoriously tricky product that many humans haven't mastered). you can imagine how this evolves as you give agents access to more tools.

- **image and video generation models have improved.** many video production pipelines will be hybrid — a mix of AI and filmed content. imagine filming interviews for a documentary, but generating B-roll or historical footage with AI. or using a motion transfer model to take a reference animation and apply it to a real character. for any of these things to work, models needed to reach a level of quality & consistency to be valuable. now, that's finally happening.

## what agents will be able to tackle

**process** — whether you're filming or generating a video, you'll likely end up with a lot more footage than you need (sometimes by a factor of hundreds — imagine how many "takes" there are for each scene of a movie or TV show). it's often a challenge to sort through all this footage, organize it, and decide what to use. products like [Eddie AI](https://www.heyeddie.ai/) can take hours of uploaded video and do things like identifying A-roll vs. B-roll, processing multiple camera angles, and comparing takes.

**orchestrate** — if we assume that many videos are going to include some element of AI in the future, we're going to need agents that orchestrate all of the models. for example — imagine you want to add an AI animation to an educational video. you'll need an agent that can generate the images, send them to a video model, and stitch the outputs together. products like [Glif](https://glif.app/agents) are launching agents that coordinate between multiple models on a user's behalf.

**polish** — fixing the small details often take a video from good to great. but if you're not a professional video editor, you may be overwhelmed by the flood of little tasks needed to polish a video. for example — you may want to adjust lighting between clips, clean noise out of the audio track, or take out filler words ("ummms" and "uhhhs") during an interview. products like [Descript](http://descript.com/)'s Underlord agent can take a video, make all these changes for you, and deliver the final version.

**adapt** — when you make a good video, it often makes sense to adapt it for more reach. for example, you may want to cut a YouTube podcast into short clips with different aspect ratios to post on your X, Instagram, and TikTok accounts. or even translate a video into other languages (and re-dub the speakers) to reach an international audience. platforms like [Overlap](http://overlap.ai/) allow you to set up node workflows for these adaptation tasks.

**optimize** — the ultimate goal isn't just replacing manual tasks with AI. it's building agents with taste that can make your videos better. there's a reason people hire professional video editors: they make things look good. they spend years learning everything from how to hook viewers to pacing the storyline and using music to build an emotional reaction. there are thousands of micro-decisions there. YouTuber Emma Chamberlain famously said that she used to spend 30-40 hours editing a ~15 minute vlog. what if an AI agent could watch your footage, ask about your objectives, and then craft a few draft versions of a video for you to iterate on? you review and direct — "the opening is too slow." "cut the middle section." "make the ending hit harder" — and the agent executes.

## the conclusion

video has won. it's how we learn, market, and connect. but the editing bottleneck keeps growing. more footage captured, more platforms to publish on, more formats required.

the good news is that technology to solve this exists. vision models, tool-using agents, and massive amounts of training data have all matured in the past year. the pieces are in place.

this means that AI editing agents will dramatically increase the quality of all video we see in the coming months and years, along with the rate at which it's created.

2025 was the year of video. 2026 is the year we let agents edit it.

if you're building anything in the AI video space reach out on X at [@venturetwins](https://x.com/venturetwins) or email at jmoore@a16z.com. i especially want to talk if you're building agents that can help me edit my companion video to this blogpost!
