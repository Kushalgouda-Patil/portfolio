---
title: "Streakwall"
description: "Turns your GitHub contribution history into an iOS lock-screen wallpaper that updates daily via Shortcuts."
publishDate: "2026-07-09"
tags: ["nextjs", "typescript", "vercel-og", "github-api"]
coverImage:
  src: "./cover.png"
  alt: "iOS lock screen wallpaper showing a 15-column dot grid of GitHub contributions for the year, colored by intensity, with a contributions and percent-of-year readout at the bottom"
repoUrl: "https://github.com/Kushalgouda-Patil/github-contribution-wallpaper"
liveUrl: "https://github-contribution-wallpaper.vercel.app"
pinned: true
---

A full-year GitHub contribution graph, rendered as a 15-column dot grid and turned into an iOS lock-screen wallpaper — each dot is a day, colored by contribution intensity, with today highlighted and a "contributions · % of year" readout at the bottom.

No app to install: paste a wallpaper URL (`/api?user=<github-username>`) into iOS Shortcuts as a daily automation, and the lock screen updates itself every morning with the latest streak.

Built on Next.js's App Router, with the image rendered on the edge via [`@vercel/og`](https://vercel.com/docs/functions/og-image-generation) and contribution data pulled live from GitHub's GraphQL API (cached for an hour, no database).
