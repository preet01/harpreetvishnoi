---
layout: post
title: "Product Critique: Zillow Mobile App"
date: 2023-05-08
read_time: 4
---

Zillow is the dominant real estate search app in the US. The iOS experience has meaningful UX gaps that make searching for homes harder than it needs to be.

## Map vs. List View Fragmentation

The app splits into two distinct modes with different capabilities. Map view lets you search by location but has no sorting options — you scroll manually through pins. List view offers sorting by price and other factors but can't filter by geographic proximity. To do a complete search, you have to constantly switch between modes. These should be unified.

## Navigation Crowding

Four clickable elements on the top bar — list toggle, search, location filter, and menu — make the interface feel bulky. On smaller screens this creates accidental taps. Consolidate or move secondary actions.

## Inconsistent Save Search

The save-search feature appears in three different visual formats across the app. Same function, different UI every time you encounter it. Consistency is basic trust.

## Missing Per-Room Filter

Users looking for group housing can't filter apartments by average cost per room. If three people are splitting rent, the only meaningful number is the per-person cost — not the total. That filter doesn't exist.

## Premature Notification Request

Permission to send notifications appears during signup, before the user has context for why they'd want them. Ask for notification permission the first time a user saves a search — when the value proposition is obvious.

## App Crashes

The app crashed approximately every 20 minutes during my testing. A potential memory leak. This is the most critical issue — everything else is polish. A crashing app loses users before any UX improvement matters.
