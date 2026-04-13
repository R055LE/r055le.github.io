+++
draft = false
date = 2026-04-12
title = "The Invisible Portfolio"
description = "DevOps work is invisible by design. So how do you build a portfolio for it?"
tags = ["devops", "career", "portfolio"]
+++

DevOps is invisible work. Not "underappreciated" invisible — invisible by design. The ideal day in operations is the one where nothing happens. Your alerting stays quiet, your deployments roll out, your infrastructure handles the spike because you sized it right six months ago. Nobody thanks you for the incident that didn't happen. And the day you finally leave it a well-oiled machine? That's invisible too.

This creates a problem when someone asks what you've been doing for the last 13 years.

I do DevOps, which means I handle server infrastructure automation. That's the version I give at parties — I whittled it down over time because the full answer takes longer than most people's interest in the question. If they lean in, I'll go deeper. Most don't.

## The Portfolio Problem

Frontend engineers can show you a website. Designers hand you a Figma file. When a DevOps engineer interviews, we get "tell me about a time you..." and hope the interviewer follows along.

The work itself is scattered across private repos, internal wikis, and environments so locked down that screenshots aren't an option. The most interesting things I've built are behind NDAs. And even if I could show them — what am I showing? Terraform modules? CI pipelines? YAML? None of it means anything without the context of why those decisions were made. To take in the value, you'd need to already be deeply ingrained in this kind of work, and then take the time to walk the code without guidance.

So I never built a portfolio. I couldn't figure out what that would even look like.

## Why Now

I'm on layoff number four in three years. The industry's doing what it's doing — costs, overstaffing corrections, the AI of it all — and I'm not here to dissect that. Something needed to change on my end.

Being wholly capable of handling the job and being able to communicate that are two very different things. I'm significantly better at the former. That gap needed to close.

## What I Did About It

I started building labs. Not tutorials — the distinction matters. A tutorial is guided: do X, get Y. These were built for me, not for a viewer. I needed to recreate work I've done before in a form that could actually exist in public. Figuring out what to do with it came later.

The code is almost secondary. What matters is the reasoning that doesn't live in the files — why burn-rate alerting instead of thresholds, why multi-window instead of single-window, what actually happens to your error budget when Redis goes down. You can read the YAML and see *what* was configured. You can't see *why* unless someone writes it down.

That's what this blog is for. The labs are the evidence. The writing is the context that makes the evidence legible.

## Not Everyone Has a React App

Not everybody has a frontend portfolio with a React app and a live demo. Not everybody has ex-Meta or ex-Google in their headline, and honestly, not everybody should aspire to it. There's a whole layer of this industry that keeps things running, and the people doing that work don't have a great answer for "show me what you've built."

Invisible work wears on you. It doesn't help with imposter syndrome when your biggest accomplishments are things that didn't break. You can't exactly share a link to the outage that never happened.

This is my attempt at something shareable — without having to set everything on fire or make up metrics for how I saved someone some fabricated amount of dollars I was definitely not tracking.

## What's Next

There's more to write. The next posts will get into specifics — the alert math, the chaos engineering methodology, what "production-grade" actually means when nobody's watching. Less about me, more about the work.
