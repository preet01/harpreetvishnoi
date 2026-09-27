---
layout: post
title: "I compared GPU rental with a managed API. Renting costs 71% more in this example."
date: 2026-09-27
read_time: 4
---

I was researching the cost of running open models, so I did the maths for GLM-5.3-Flash: what does it cost to run the model on rented GPUs versus use the same model through a managed API?

Using published GPU rental prices, API prices, and a performance benchmark, renting the GPUs costs approximately 71% more for the same estimated amount of work. That is before adding setup, supporting infrastructure, or engineering time.

## What I compared

A managed API lets an application send requests to a provider that runs the model. You pay for the input tokens it reads and the output tokens it generates. Tokens are the small pieces of text the model processes.

With rented GPUs, your company rents the hardware, loads the model, and operates the software that handles requests. You pay for the hardware while it is allocated to you, whether it is busy or idle.

For the rented option, I used one copy of GLM-5.3-Flash running across four NVIDIA B200 GPUs, matching a published Lambda benchmark.

Why four GPUs? The model is too large for one GPU in this configuration. Its deployment recipe lists approximately 386 GB of GPU memory as a planning minimum. The model is spread across connected GPUs, which work together to answer requests. This is one model running across four GPUs, not four separate models. [Deployment recipe](https://github.com/vllm-project/recipes/blob/main/models/zai-org/GLM-5.3-Flash.yaml)

## The cost of one hour

I used [Lambda's published benchmark](https://lambda.ai/inference-models/zai-org/glm-5.3-flash) to estimate how much work the four-GPU setup could complete in an hour.

If it sustained the benchmark's highest measured processing rate for the full hour, it would process approximately 74.62 million input tokens and generate 9.33 million output tokens. Processing those same input and output volumes costs $15.86 through the managed API, compared with $27.16 to rent the four GPUs for that hour.

| Complete the same amount of work | Cost |
|---|---:|
| Managed GLM-5.3-Flash API: 74.62 million input tokens and 9.33 million output tokens | $15.86 |
| GLM-5.3-Flash on four rented B200 GPUs for one hour | $27.16 |

The calculation uses the September 2026 prices from my research:

- GPU rental: $6.79 per B200 per hour, from [RunPod](https://www.runpod.io/pricing).
- Managed API: $0.15 per million input tokens and $0.50 per million output tokens, listed for several providers on [OpenRouter](https://openrouter.ai/z-ai/glm-5.3-flash).

GPU rental costs $11.30 more per hour, approximately 71% more, before adding the cost of operating the system.

## Why keeping the GPUs busy does not close the gap

I initially expected renting to become cheaper once usage was high enough. But this comparison already assumes the GPUs sustain the benchmark's highest measured processing rate.

Keeping GPUs busy spreads the rental bill across more work. Once they reach that rate, however, handling more work in the same hour requires more capacity or a faster setup.

In this comparison, each four-GPU setup costs $27.16 per hour to process 74.62 million input tokens and generate 9.33 million output tokens. Buying that same amount of processing through the API costs $15.86. Renting another identical setup doubles both the capacity and the rental bill. It does not make each token cheaper.

That is why having lots of AI usage is not enough, by itself, to make renting economical. The rented hardware also has to complete that work at a lower cost than the API.

## What the comparison tells me

The maths combines Lambda's measured performance with RunPod's advertised rental price. It assumes the rented setup delivers comparable performance. The input and output volumes belong to that particular benchmark workload, not a universal maximum for every kind of request.

The benchmark also uses many requests running at once, with longer waiting times. It tells me how much work the setup can process, not how quickly an individual user will receive an answer. The API figure prices the same token volume; it is not a measurement of the API's response time or a guarantee of its hourly capacity.

Different hardware, lower rental prices, or better serving software could change the result. Privacy and control may also justify self-hosting even when it costs more.

But for this setup, the maths favors the managed API even with the rented GPUs kept busy. Using an open model and running its infrastructure are separate decisions. In this comparison, paying a provider to run the model is cheaper.
