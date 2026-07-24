---
layout: post
title: "What the Jacobian Lens Taught Me About How a Model Thinks"
date: 2026-07-23
read_time: 14
---

I started this journey with a simple question. How do neurons work. I ended it staring at an Anthropic paper that asks something much stranger. Does a language model have something like a private, reportable train of thought, sitting underneath everything it says out loud.

This post is my attempt to walk through that whole arc in one pass, the way I wish I could hand it to myself from ten years in the future and have it still make sense.

## Part 1: Where it started, a single neuron

A single biological neuron is not very impressive on its own. It basically has one move, fire or don't fire, and it tells the next neuron how urgent that signal is by how fast it repeats the firing, not by sending a different kind of signal each time. One neuron alone can barely say more than "a little" or "a lot."

Real information, the kind that lets you recognize your grandmother's face, never lives in one neuron. It lives in the pattern across thousands of neurons firing together, at particular rates, in particular combinations. A single drumbeat tells you nothing. A full orchestra, playing together, tells you everything.

![A chain of neurons compared to a transformer's residual stream](/images/jacobian-lens/diagram1_neuron_vs_residual.svg)

That idea, that meaning lives in a population, in a pattern, not in one unit, turns out to be the right frame of mind to carry into the second half of this story. Because a transformer has its own version of a signal being passed along, and it faces its own version of the question, where does meaning actually live inside all these numbers.

## Part 2: The residual stream, the transformer's version of a nerve

A transformer processes text through a stack of layers. At every layer, for every token position, it keeps a running vector called the residual stream. Think of it as a notebook page that gets passed up through the layers. Each layer reads the current page, does some computation, and writes its result back onto the same page, adding to what was already there instead of replacing it.

Early in the stack, that page holds barely more than "which word is this." By the final layer, it holds enough for the model to predict the next word directly. Everything in between is where the model's actual thinking happens.

If we label the state of that page after each layer as I1, I2, I3, I4, and I5 (the final layer), we get a clean way to talk about a snapshot at any point in the process. Each of those snapshots is called an activation.

![How a transformer layer writes to the residual stream without erasing what came before](/images/jacobian-lens/diagram6_residual_write_pattern.svg)

## Part 3: The question a group of Anthropic researchers asked

In July 2026, a team at Anthropic (Wes Gurnee, Nicholas Sofroniew, Jack Lindsey, and collaborators) published a paper called "Verbalizable Representations Form a Global Workspace in Language Models." Their question borrows directly from neuroscience.

In your brain, only a small slice of everything happening is ever available to you consciously, the part you could describe if someone asked. The rest, your visual system parsing a face, your motor system holding your posture, runs automatically, invisibly. Cognitive scientists call the accessible slice the global workspace, a shared space that many parts of the brain can read from and write to, that supports deliberate reasoning, that you can hold a thought in, and that is limited in size.

The researchers asked whether something similar has quietly emerged inside language models. Is there a small, privileged set of internal representations, sitting on top of a much larger mass of automatic processing, that a model can report on, hold in mind, and reason with.

![The global workspace analogy: a small reportable slice inside a much larger mass of automatic processing](/images/jacobian-lens/diagram7_global_workspace_analogy.svg)

To go looking for it, they needed a way to peek inside the residual stream at a middle layer and ask, in a principled way, what is this activation currently disposed to make the model eventually say.

## Part 4: The Jacobian lens, reading the middle of the notebook

Here is the actual mechanism, in the same I1 through I5 language.

A derivative answers a simple question. If I nudge this input a tiny bit, how much does the output move, and in which direction. A Jacobian is just the multi number version of that same question, since here both the input (an activation) and the output (the final layer's activation) are vectors with thousands of numbers rather than one.

So for layer 2, the Jacobian J2 asks, if I nudge I2 a tiny bit, how much does I5 move, per unit of nudge. That relationship is called the Jacobian matrix.

Computed on a single sentence, that measurement would be noisy, mixing "what does this direction generally mean" with "what happened to be going on in this one sentence." So the researchers computed it across 1,000 different prompts, and across many token positions inside each prompt, then averaged all of those measurements together into a single clean matrix per layer.

Once you have that averaged matrix, here is what you do with it. Take a real activation, say I2, multiply it by J2. That gives you a stand in for what the final layer would roughly look like, given this middle layer snapshot. Normalize it, then run it through the model's own final step, the unembedding matrix, the exact same operation the model always uses at the true end of processing. Out comes a score for every word in the model's vocabulary. Sort those scores, and the top of the list is the answer, the words this middle layer activation is currently leaning toward saying.

![The Jacobian lens pipeline, from a middle layer activation to a ranked word list](/images/jacobian-lens/diagram2_jacobian_lens_pipeline.svg)

This is a refinement of an older, simpler tool called the logit lens, which just skips straight to the final step without correcting for the fact that early layers use a different internal "coordinate system" than late layers. That shortcut works fine near the end of the network and produces garbage near the beginning. The Jacobian lens fixes exactly that gap.

The formula itself is genuinely simple, a derivative and an average. What is not simple is knowing which derivative to take, averaging it in exactly the right way to isolate general disposition from one sentence noise, and then proving, through a long chain of experiments, that this simple measurement actually picks out something functionally special rather than being one more arbitrary probe among thousands you could have run instead.

## Part 5: Proving it is not just a nice looking correlation

A ranked list of words sitting at some middle layer would not mean much if it were just a coincidence, a shadow that happens to line up with the model's real output without actually driving it. The paper spends real effort ruling that out.

The first test is a straightforward correlation. Ask the model to think of a sport and name it in one word. Right before it answers, check the Jacobian lens readout at that exact position. In one example, Soccer sits at the top of the readout, and the model then says "Soccer."

The second test is where it gets interesting, because correlation is cheap and causation is the real prize. The researchers took the model's internal Soccer direction and swapped it for a Rugby direction, leaving everything else about the activation untouched, then let the model keep processing. The model then said "Rugby" instead.

![Swapping the J-space content changes what the model says out loud](/images/jacobian-lens/diagram4_swap_experiment.svg)

Scaled up across many categories and many trials, this swap reliably pulls the target word toward the top of the model's output, even for words that were not in its top ten most likely answers beforehand. That is the difference between "these two things happen to move together" and "this is the thing actually driving the outcome."

They pushed even further, and split a concept's full internal representation into two pieces, a small slice aligned with this newly found workspace (about 6 to 7 percent of the concept's total variance) and a much larger remaining slice (roughly 93 percent). When they repeated the swap experiment using only the small workspace slice, it worked nearly as well as using the whole thing. Using only the large remaining slice, it barely worked at all. A small sliver of a concept's representation is doing almost all the work of making that concept reportable, while the much larger remainder is presumably busy with something else entirely.

## Part 6: A model can hold a thought while saying something else

Humans can deliberately hold a concept in mind while doing something unrelated. Think about lemons while you read this sentence about a rainstorm, and you'll find you can do both at once. The paper tests whether a language model can do the same thing.

They instructed Sonnet 4.5 to concentrate on citrus fruits while writing a totally unrelated sentence about an old painting. The output sentence never mentions fruit. But checking the Jacobian lens readout partway through that sentence, the word orange sits at the top.

![Holding "orange" in mind while writing about a painting](/images/jacobian-lens/diagram5_directed_modulation.svg)

An even sharper version of this experiment asked the model to silently solve 3 squared minus 2 while copying that same unrelated sentence. Checking the same middle position, the readout doesn't just show "math," it walks through the actual computation, arithmetic, then the intermediate value nine, then the final answer seven, all of it silent, none of it appearing anywhere in the visible output.

They also found this control is imperfect in a way that will feel familiar to anyone who has ever tried not to think of something. Telling the model to ignore a concept does suppress it relative to actively focusing on it, but it does not suppress it all the way down to the baseline you'd get with no instruction at all. That's the same "white bear" effect studied in humans, where being told not to think about something makes you think about it a little more than if nobody had mentioned it.

## Part 7: The shape of the workspace itself

Beyond just existing, this workspace has real structure. It only behaves like a workspace across a band of middle layers, not the whole network. Early layers are mostly parsing the raw input, and the final few layers shift into what the researchers call a motor regime, locking in on the specific next word rather than holding abstract concepts anymore.

![The J-space sits in a middle band of layers, not the whole network](/images/jacobian-lens/diagram3_jspace_structure.svg)

Within that middle band, the workspace has limited capacity, a small number of concepts active at once, occupying only a minority of the total activity happening at that layer. And it behaves like a genuine broadcast format, meaning the directions that make it up connect, both upstream and downstream, to a wider set of the model's internal circuitry than most other representations do. That is the mechanistic fingerprint of something meant to be written once and read by many different parts of the system, exactly the role a global workspace is supposed to play.

## Part 8: Why this matters beyond curiosity

This is not just a neat parlor trick for peeking inside a model's head. Because the Jacobian lens surfaces things a model is not currently saying but could report if asked, it becomes a genuine safety tool. The same technique the paper builds toward later lets researchers catch a model's strategic deliberations, notice when a model has silently recognized something like a prompt injection without ever mentioning it out loud, and detect misaligned objectives a model might be pursuing quietly, underneath an output that looks completely normal.

That is the throughline connecting everything in this post. A biological brain hides most of its processing from you and surfaces only a privileged slice for conscious report. It now looks like language models have developed something structurally similar, on their own, without anyone explicitly training them to have it, and we finally have a tool simple enough to actually go looking for it, and rigorous enough to trust what it finds.

## A practical note, for future me

If you ever try to run something like this locally, remember that the "1000 prompts" step in the formula is not a metaphor, it's a real computation your machine has to redo. Loading model weights is fast, since it is mostly reading bytes off disk. Fitting or loading a Jacobian lens against a real prompt corpus, and moving all of that onto a GPU, is genuinely heavy compute, and on Apple hardware using MPS, the very first run is especially slow because of one time kernel compilation. A finished HTML file sitting on your disk from a previous run will not save you from any of this, since a fresh process has no memory of anything that came before it. That part, at least, is a little bit like the model itself, plenty of processing that never carries forward unless something deliberately writes it down.

---

*Source: Gurnee, Sofroniew, Lindsey, et al., "Verbalizable Representations Form a Global Workspace in Language Models," Anthropic, July 2026.*
