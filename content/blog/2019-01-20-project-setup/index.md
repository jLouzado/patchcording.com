---
title: 5 Tips When Starting A Client Project
date: '2019-01-20T15:00:00.314Z'
---

![Freelancer At A Typewriter](./rawpixel-604746-unsplash.jpg)

This is the advice I wish I'd had when I was starting out as a freelance developer.
Once you've gotten a client and sealed the deal, where do you begin?

## 1. Write Down The Requirements!

Seriously, write them down. Share them with the client, get approval.

- There's always the possibility that you've misunderstood something
- If something isn't clear, now is your chance to get it cleared up.
  - It's cheaper to find a mistake in a Doc than in Code.

> Requirements _will_ change. Plan accordingly.

However, don't try to refine Requirements **perfectly**.

- Firstly, perfection is a myth and any plan that depends on it will fail.
- Secondly, circumstances change, so it's only necessary to have the shape of the app in mind when beginning.

Which brings us to our first point:

## 2. Get Feedback

Whatever you do, don't disappear for 3 months with a plan to be "done" by then.
Software projects are fundamentally [complex](https://en.wikipedia.org/wiki/Cynefin_framework) so get feedback as frequently as possible.

If possible, invest some time in learning the basics of [Scrum](https://www.scrumguides.org/index.html) and use that as a feedback framework.
The terminology can get a little confusing, but the basic idea is simple:

- define a fixed time-period to show the product to the client and get feedback.
  - say 1 to 2 weeks, but not longer than a month.
- every week, deliver something of **business value**
- get feedback, re-prioritize, and start another cycle.

### 2.5 Sidenote about Scrum

- There are definitely other Agile Methodologies out there,
  - use whatever you like, but
  - make sure it's solving the client-feedback problem
- The beauty (I think) of Scrum is that it makes _management_ (managers and clients) more agile and data-driven.
- There's a lot of hype around Agile, use whatever gets the job done.

### 2.5 (b) Regular Payment Schedules

- Having a regular feedback cycle also naturally lends itself to getting paid regularly.
- Maybe not every cycle, maybe every couple of cycles
  - figure out what works best for you.
- Definitely **DO NOT** disappear for three months and then present a bill. Dear God, please. k?

## 3. Deploy to Production ASAP

When I first started I definitely underestimated the devOps side of things. In my mind the thing worked,
there was no need to mess around with deployment till we were ready for customers.

What ended up happening was that I became a bottleneck and that, coupled with the fact that I didn't setup regular feedback sessions,
meant that the Product was out of the market for way longer than it needed to be.

That doesn't have to be you, deploy to Production in Sprint 1 itself. This has two benefits:

1. It lowers the cost of feedback
   - Someone interested in the Project doesn't need to setup a dev-environment just to look at it
   - they can just load a URL and take a look
   - Especially in today's day and age with services like Netlify and Heroku there's really no reason not to.
1. You'll get customers much sooner because they'll be able to use it as well
   - Since you're not a bottleneck, marketing and sales can start chipping in with feedback much more quickly.

I've become somewhat obsessed with this after being burned by not following this advice. Especially if it's a web-app,
there's no excuse to not have the app hosted somewhere as soon as humanly possible.

## 4. Write Tests

Not all the tests, not 100% coverage, but definitely for critical paths and TDD as much as possible.
It's easy in the beginning, and the cost of introducing it gets harder and harder as time passes.

Write tests, future you will thank you for it.

> For Details refer:
> [Write Tests, Not Too Many, Mostly Integration](https://blog.kentcdodds.com/write-tests-not-too-many-mostly-integration-5e8c7fff591c)

## 5. Finish Your Work First

This is one I still struggle with even to this day. If you're even a little imaginative it's easy to get carried away.
You think you know everything, and that you'd be able to fix everything if only you had the reins. If that's true, you should just
start your own thing, freelancing might not be for you.

However, if you're in a team, and you're working with professionals then they're relying on you; so you should rely on them and
let them do their jobs.

Be practical, finish your stuff first; it'll probably take you longer than you think. Use your judgement.

## Final Thoughts

This subject is vast, and each situation is different but hopefully these basics help set up a self-adjusting, adaptive structure for your
app to live in.

For more specific recommendations I'd highly recommend the [12-Factor App](https://12factor.net/) as a starting point.

Good luck out there. :)

> Cover Photo by [rawpixel](https://unsplash.com/photos/faUzKz3TvpM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
