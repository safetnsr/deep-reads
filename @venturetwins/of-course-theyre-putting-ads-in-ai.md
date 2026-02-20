# of course they're putting ads in AI

**author:** justine moore (@venturetwins) & bryan kim (@kirbyman01)
**source:** a16z.com
**date:** february 2026
**url:** https://a16z.com/putting-ads-in-ai/

---

the internet is a miracle of universal access to opportunity, inquiry and connection. and ads pay for that miracle. as marc has long argued, "if you take a principle stand against ads, you're also taking a stand against broad access." ads are why we have nice things.

so, the announcement last month that openai plans to launch ads for free users is probably the biggest piece of news-that-isn't-actually-news of 2026 (so far). because of course, if you've been paying attention, the signs that this would happen have been everywhere. fidji simo joined openai in 2025 as CEO of applications, which many people interpreted to mean "implement ads, just like she did at facebook and instacart." sam altman had been teasing the rollout of ads on business podcasts. and tech analysts like ben thompson have been predicting ads pretty much since chatgpt launched.

but the main reason that ads aren't a surprise is because they're the best way to bring a service on the internet to the largest possible number of consumers.

## the luxury beliefs problem

"luxury beliefs", a term that came into vogue a few years ago, are stances taken not really for principled reasons, but for optical reasons. tech has plenty of these, especially when it comes to advertising. for all the moralistic hand-wringing over "selling data!" or "tracking!" or "attention harvesting" and other bingo words, the internet has always run on ads and most people like it that way. internet advertising has created one of the greatest "public goods" in history, for the negligible price of occasionally having to look at ads for cat snuggies or hydroponic living room gardens. people who pretend this is a bad thing usually are trying to prove something to you.

## the platform precedent

any internet history buff knows that ads are a core part of how platforms eventually monetize: google, facebook, instagram, and tiktok all started free, and then figured out monetization with targeted ads. ads can also be a way to supplement the ARPU of a lower-value subscriber, as in the case of netflix's newer $8/month option, which introduced ads to the platform. ads have done a very good job of training people to expect most things on the internet to be free or extremely low-cost.

this pattern can now be seen across frontier labs, as well as specialized model companies, and smaller consumer AI companies. from our survey of consumer AI subscription companies, we can see that converting subscription users can be a real challenge for all of them.

so what's the solution? as we all know from past consumer success stories, ads are often the best way to scale your service to billions of users.

## why most people don't pay

to understand why most people don't pay for AI subscriptions, it helps to understand what people use AI for. last year, openai published data on exactly this.

in short, most people use AI for personal productivity: things like writing emails, searching for information, and tutoring or advice. meanwhile, higher value pursuits, like programming, make up a very small percentage of overall queries. anecdotally, we know that programmers are some of the most committed users of LLMs, with some even calibrating their sleep schedules to optimize for daily usage limits. for these users, a $20 or $200/month subscription doesn't feel exorbitant, because the value that they're getting (the equivalent of a swarm of highly productive SWE interns) is likely orders of magnitudes greater.

but for the users who are using LLMs for general queries, advice, or even writing help, the onus of actually paying is too great. why would they pay for an answer to questions like "why is the sky blue" or "what are the causes for the peloponnesian war" when previously a google search would direct you to a good-enough answer for free. even in the case of writing help (which some people are using for email jobs and rote work), it often doesn't do enough of a person's job to justify an individual paying for a subscription. additionally, advanced models and features often aren't needed by the majority of people: you don't need the best reasoning model to write emails or suggest recipes.

## the scale argument

let's take a step back and acknowledge something for a moment. the absolute number of people paying for a product like chatgpt is still enormous: 5-10% of 800M WAUs. 5-10% of 800M is 40-80M people! on top of that, the price point for Pro at $200 is ten times what we thought the ceiling was for consumer software subscriptions. but, if you want to get chatgpt to a billion people (and beyond) for free you need to introduce a product other than subscriptions.

the good news is that people actually do like ads! ask the average instagram user, and they'll probably tell you that the ads they get are ridiculously useful: they get served products they actually want and need, and make purchases that actually make their lives better. framing ads as exploitative or intrusive is regressive: maybe we feel that way about TV ads, but targeted ads are actually pretty great content most of the time.

i'm using openai as an example here (since they have been one of the most forthcoming labs when it comes to comprehensive disclosures around usage trends). but this logic applies to all frontier labs: they will all need to introduce some form of advertising eventually if they want to scale to billions of users. the consumer monetization model is still unsolved in AI.

my general rule of thumb in consumer app development is that you need a minimum of 10M WAUs before introducing ads. many AI labs are already at this threshold.

## the ad models

we already know ad units are coming to chatgpt. what might they look like, and what other ad and monetization models are viable for LLMs?

### higher value search and intent-based advertising

openai has confirmed that these kinds of advertisements (ingredients for recipes, hotel recommendations for trips, etc.) are coming to the platform for free and low cost-tier users. these ads will stand apart from answers in chatgpt, and will be clearly labeled as sponsored. over time, the ads may feel more like prompting: you'll prompt an intent to purchase something, and the agent will fulfill your request end-to-end, from a list of sponsored and unsponsored content.

in a lot of ways, these ads are reminiscent of the earliest ad units in the 90's and 2000's, and what google has perfected with their sponsored SEO ad units (for what it's worth, google still derives the vast majority of revenue from its ad business, and only ventured into subscriptions 15+ years into their history).

### context-based ads in the style of instagram

ben thompson has made the point that openai should've introduced ads much earlier to chatgpt responses. first, it would have acclimated non-paying users to ads much earlier (back when they had a true head start on gemini's capabilities). second, it would have given them a lead on building a truly great ad product, which anticipates what you want rather than opportunistically feeding ads based on intent-based queries.

instagram and tiktok can deliver an amazing ad experience that shows you products you never knew you wanted, but absolutely need to buy immediately and many people find the ads to be useful rather than obtrusive. given the amount of personal information and memory openai has, there is plenty of opportunity to build a similar ad product for chatgpt. of course, there are differences between the experience of using these apps: can you transpose a more "lean-back" ad experience on instagram or tiktok into the more engagement-heavy model of using chatgpt? it's a much harder problem, and a way more lucrative one to get right.

### affiliate commerce

last year, openai announced an instant checkout function in collaboration with marketplace platforms and individual retailers, that would allow users to make purchases from directly within their chat. you can imagine this being built out into its own dedicated shopping vertical, in which an agent proactively sources clothing, home goods, or rare items you're tracking because of their limited availability, and the model provider getting a cut of revenue from the marketplaces featured in this service.

### games

games are often forgotten or glossed over as being their own kind of ad unit, and we don't know for sure how they play into chatgpt's ad strategy, but they're worth mentioning here. app install ads (many of whom were for mobile games) were a huge percentage of facebook's ad growth for years on end, and games are so inherently profitable that it's not hard to imagine big ad budgets getting spun up here.

### goal-based bidding

this is a bit of a fun one, for the fans of auction algorithms (or for former blockchain gas-fee optimizers who want to pivot into LLMs). what if you could set a bounty for a specific query (e.g., $10 for noe valley real estate alerts) and have a model throw an outsize amount of compute at a particular outcome? you'd get perfect price discrimination based on the determined "value" of a question, and could also get better guaranteed chain-of-thought reasoning for searches that are particularly important to you.

poke is one of the best examples of something like this: people had to explicitly negotiate with the chatbot for subscription to the service (of course this didn't map to compute costs, but it's still a fun illustration of what it could look like).

in some ways, this is already how some models function: both cursor and chatgpt have routers that select models for you, based on the interpreted complexity of the query. but even if you're the one selecting models from a dropdown, you don't get to choose the underlying amount of compute a model throws at a problem. for highly motivated users, being able to specify an example of how much a problem is worth to them in dollar amounts could be appealing.

### subscriptions for AI entertainment and companions

there are two main use-cases that users of AI have demonstrated a willingness to pay for: coding and companionship. characterAI has one of the highest WAU counts of any non-lab AI company. they can also get away with charging a $9.99 subscription fee for their service because what they offer is a hybrid of companionship and entertainment. but even though people do pay for companion apps, we have yet to see companion products cross the threshold where they can reliably monetize via ads.

### pricing by token usage

in the AI creative tooling and coding space, pricing by token usage is also a common monetization model. this has become an attractive pricing mechanism for companies with power users, which allows them to differentiate and charge more in accordance with usage.

## conclusion

monetization is still an unsolved problem in AI, with the majority of users still enjoying the free tier of their preferred LLM. but this will only be temporary: the history of the internet has taught us that ads find a way.
