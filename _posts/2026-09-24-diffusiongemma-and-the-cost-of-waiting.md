---
layout: post
title: "What happens when AI gets faster?"
date: 2026-09-24
read_time: 3
---

Hey, when you ask an AI a question, what do you do while it thinks? You probably wait a few seconds, open another tab, check a message, and come back. By then, you've lost a little of the flow. Now imagine doing that every time you need help with something.

That's what got me interested in DiffusionGemma. We talk a lot about how smart AI is getting. I'm equally interested in what happens when it gets fast enough to keep up with us.

Most familiar LLMs write an answer one token at a time. A token is a small piece of text. Before you see anything, the model may be processing your question, generating internal reasoning, or waiting for a search or another tool. Once the answer starts appearing, its generation speed determines how quickly the rest arrives. So the whole wait includes getting started, producing the answer, and whatever tools or network calls the task needs.

DiffusionGemma works differently. It refines blocks of 256 tokens in parallel, then moves to the next block. Google reports over 1,100 tokens per second on an H100 GPU using FP8 precision with small batches of requests. That makes me wonder how many product experiences we've designed around waiting could change.

![Generation speeds: DiffusionGemma over 1,100 tokens per second in Google's H100 test; GPT-6 Sol max 99, Claude Opus 5.5 high with fallback 88, and GPT-6 Astra high 52 in hosted API measurements.](/images/diffusiongemma/generation-speed.svg)

*A September 2026 snapshot: the gray bars are hosted API measurements from Artificial Analysis; the green bar is Google's H100 result. The setups differ, so these numbers show the range of speeds rather than how the models perform on the same job.*

Take a 600-token answer. At 60 tokens per second, generating it takes about ten seconds. At 600, it takes about one. You still have the other delays, but that difference can change where AI fits into someone's day.

A customer support rep could get a useful reply while the customer is still explaining the problem. A shopping assistant could help someone compare two products before they leave the page. A developer could get a small replacement function while they're still focused on the code. These are moments where an answer arriving sooner could mean someone actually uses it.

The same idea gets interesting with agents. A coding agent writes a fix, runs a test, sees an error, and tries again. Faster generation leaves more time for another attempt. Even if several agents work in parallel, some steps still depend on earlier ones finishing. Research like Self-Refine has shown that feedback and revision can improve answers. Speed gives us more room to try that within the time someone is willing to wait.

Voice is another obvious opportunity. Shorter pauses could make a tutor, shopping assistant, or customer service bot feel easier to talk to. Detecting when someone has finished speaking and generating audio also take time, so faster text is one part of making that conversation feel natural.

DiffusionGemma still trails its Gemma 4 counterpart on several quality measures. I'd still want a stronger model for a difficult problem, and faster generation won't make a slow database or test suite disappear. But I see real potential in the everyday tasks where the answer is good enough and timing matters. Help that arrives while you're doing something could become a much bigger part of how we work.

[Download the one-page PDF](/images/diffusiongemma/diffusiongemma-and-the-cost-of-waiting.pdf)
