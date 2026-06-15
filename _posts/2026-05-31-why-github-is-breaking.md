---
layout: post
title: "Why GitHub is Breaking"
date: 2026-05-31
read_time: 3
---

If you've been a developer at any point in the last year, you've probably had one of those days where you try to push code, open a pull request, or kick off a build and nothing works. GitHub is down. Again.

GitHub has had 257 incidents between May 2025 and April 2026, with 48 of them classified as major outages — roughly one serious disruption every single week.

That made me go down a rabbit hole. Why is it going down? What system does agent-led commits break? Why can't GitHub just add more servers, since they have infinite capacity with Azure?

## Scale

GitHub processed **1 billion total commits in all of 2025**. By early 2026, it was handling **275 million commits every single week**. That's a 14x jump in months, not years.

## Why Not Just Add More Servers?

A single pull request touches: Git storage, mergeability checks, branch protection rules, GitHub Actions, search, notifications, permissions, webhooks, APIs, background jobs, caches, and databases. All at once.

This is called **architectural coupling** — all the parts are stitched together so tightly that when one breaks, everything else gets dragged down too. Adding more servers to a system this tightly connected just means more things competing for the same shared bottlenecks. It's like adding more cars to a road where there's only one bridge. The bridge is still the problem.

## So, How Can This Be Fixed?

1. **Separate the critical stuff:** Git operations (saving code) and GitHub Actions (running tests) are being moved to their own dedicated infrastructure. If search breaks, it breaks alone — your code-saving keeps working.
2. **Separate human and robot traffic lanes:** AI agents get their own lane. When it floods, human developers are protected. Agent traffic shouldn't be able to starve a person trying to open a pull request.
3. **Rewrite the slowest parts:** GitHub is built on Ruby on Rails — a framework designed for human-paced web traffic. The highest-traffic code is being rewritten in Go, a language built for machine-speed, high-volume operations.
