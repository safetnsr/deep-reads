# Kling 2.6 Motion Control

**Author:** @venturetwins  
**Date:** 2026-01-17T17:52:51.000Z  
**URL:** https://x.com/venturetwins/article/2012583915215880584  
**Images:** 2  

---

Want to make one of the viral AI character swaps that are blowing up right now? It's easier than you might think! You no longer need to run this kind of video-to-video model on your local machine - there are cheap tools to do it in the browser.

I've spent hours figuring out the perfect workflow for these videos. In this post, I'll walk through your options for models to use, how to prompt them, and things to keep in mind to get a successful result.

Let's get started with the most popular model 👇

## Kling 2.6 Motion Control

[![](https://pbs.twimg.com/amplify_video_thumb/2012384669245825024/img/lfncK795CjQFo7vw.jpg)](blob:https://x.com/59800184-5d54-40b6-ab10-f11069503b36)

0:01 / 0:26

A few examples of the types of character swaps you can do with Kling Motion Control.

Kling (

[@Kling\_ai](https://x.com/@Kling_ai)

) is the 👑 right now for AI character swaps. You can upload a reference video of up to 30 seconds, and it handles some pretty complex motions! I'd recommend the below workflow:

1) Find the reference video. You want a video with a clear shot of a SINGLE character, and their entire upper body (or full body) should be visible. You can record your own or pull one from the Internet (there are reference animation videos on YouTube, and plenty of clips on places like TikTok).

2) Transform the first frame. Screenshot or extract the first frame and take it to an image edit model like Nano Banana Pro or Flux 2 (I use both on 

[Krea](https://krea.ai)

 - 

[@krea\_ai](https://x.com/@krea_ai)

). Prompt your desired changes. You can change both the character and the background, but make sure you keep the character's position the same. E.g. "replace the man in image 1 with the woman in image 2 in exactly the same pose."

[![Image](https://pbs.twimg.com/media/G-4Z0NJWIAIYXN5?format=jpg&name=small)](/venturetwins/article/2012583915215880584/media/2012574468153942018)

A few examples of the types of transformations that can work.

3) Take the video and new first frame to Kling. You can run the model on 

[Kling](https://kling.ai)

's product, or somewhere that hosts the model (like 

[Krea](https://www.krea.ai/motion-transfer)

 or 

[Fal](https://fal.ai/models/fal-ai/kling-video/v2.6/standard/motion-control)

). I don't put anything in the prompt box, the reference video + starting image covers it. If you run the model on Kling, make sure you check the "character orientation matches video" box.

4) Change the voice (if needed) with ElevenLabs. The model will keep the audio that you upload in the reference video - which doesn't work if you're changing the age, ethnicity, or gender of the character. You can upload your video (or just the audio) to ElevenLabs' 

[voice changer](https://elevenlabs.io/voice-changer)

. It will keep the delivery and timing but change the voice itself!

Pro tip - most people don't yet realize that you can get more complex full-body movement with Kling Motion Control. But it can be really good. Something to explore further if you want to stand out.

[![](https://pbs.twimg.com/profile_images/1987694712883691520/gK8yLQrE_normal.jpg)](/venturetwins)

Justine Moore

@venturetwins

·

[Dec 21, 2025](/venturetwins/status/2002541792932344032)

We may have cracked motion control in AI video.
I've been testing the new 

[@Kling\_ai](/Kling_ai)

 tool, and it's shockingly good.
It can handle complex movements I've never gotten another video-to-video model to do - like this gymnastics clip.
It can also do lip sync and camera movement ![👇](https://abs-0.twimg.com/emoji/v2/svg/1f447.svg)

[![](https://pbs.twimg.com/amplify_video_thumb/2002541583804088320/img/Oa3SAhS97eH6AN-3.jpg)](blob:https://x.com/8fbfb4b5-588f-470d-8b74-e77777526102)

0:01 / 0:13

47

112

899

[350K](/venturetwins/status/2002541792932344032/analytics)

If you're interested in testing alternative models or workflows, keep reading!

## Wan 2.2 Animate

[![](https://pbs.twimg.com/amplify_video_thumb/2012375918124412928/img/T6M3ZPtdw2hN1IXI.jpg)](blob:https://x.com/dfd50564-0c5d-478c-98e8-ae0bdf3517b0)

0:01 / 0:22

Wan 2.2 Animate is a slightly older model but you can still get strong character swap results. With this model, I've found it even more important that the new start frame is "clean" (no extra people, and the proportions match the reference video exactly).

The workflow is the same as what I described above, and you can also run it on either 

[Krea](https://www.krea.ai/motion-transfer)

 or 

[Fal](https://fal.ai)

. But it's important to know that there are two models here, so pick the right one for your use case:

1. [Move](https://fal.ai/models/fal-ai/wan/v2.2-14b/animate/move)

    - applies the character motion from a video into a new scene (you'd use this if you want to change the character AND the background / environment).
2. [Replace](https://fal.ai/models/fal-ai/wan/v2.2-14b/animate/replace)

    - swaps out only the character in a video while keeping the rest of the scene consistent.

## Other options

If you're interested in video editing more broadly, two other models to check out are 

[Luma Ray 3](https://lumalabs.ai)

 (select "Modify") and 

[Runway Gen-4](https://runwayml.com/)

 (select "Aleph").

The clip lengths are a bit more limited - 10 seconds for Luma, and 5 seconds for Runway. And I've found that for close-up shots of a person talking, the lip sync typically isn't as good as Kling and Wan.

However - these models are definitely worth a look if you want to do anything beyond a character swap or if you don't need much speech in the video. The clip below has some great examples of what Luma Ray 3 Modify can do.

[![](https://pbs.twimg.com/profile_images/1735758549836537859/3M7OxS2k_normal.jpg)](/mrjonfinger)

Jon Finger

![](https://pbs.twimg.com/profile_images/1861041306258288640/V5yJEmjK_bigger.jpg)

@mrjonfinger

·

[Jan 8](/mrjonfinger/status/2009381297555161374)

One of the amazing things about Ai is the possibility of just exploring a world you imagine for no other reason than your own play and joy.
Made with 

[@LumaLabsAI](/LumaLabsAI)

 Ray3 Modify.

[@DreamLabLA](/DreamLabLA)

[![](https://pbs.twimg.com/amplify_video_thumb/2009381170883252225/img/tJ4Lr0SeT1SYYi0f.jpg)](blob:https://x.com/2dfa449f-4e49-4bcb-b8b0-c43afb69023c)

0:05 / 1:24

154

865

5.4K

[333K](/mrjonfinger/status/2009381297555161374/analytics)

## Conclusion

Thanks for reading! As you can probably tell, I'm really bullish on AI character swaps and motion transfer as a more controllable way to generate video. Today, we have people turning themselves into influencers - in the future, it's hard to see how this doesn't transform all types of filmed content (e.g. ads, TV, and movies).

If you use this tutorial or have an alternative workflow, please share your results ⬇️! I'd love to see what others are making.

And tagging the companies mentioned in this post in case you want to check them out further: 

[@Kling\_ai](https://x.com/@Kling_ai)

, 

[@krea\_ai](https://x.com/@krea_ai)

, 

[@fal](https://x.com/@fal)

, 

[@Alibaba\_Wan](https://x.com/@Alibaba_Wan)

, 

[@LumaLabsAI](https://x.com/@LumaLabsAI)

, 

[@runwayml](https://x.com/@runwayml)

---

## Images

![Image](https://pbs.twimg.com/media/G-4gwIaW4AAljto?format=jpg&name=900x900)

![Image](https://pbs.twimg.com/media/G-4Z0NJWIAIYXN5?format=jpg&name=small)

