+++
draft = false
date = 2026-04-12
title = "The Invisible Portfolio"
description = "DevOps work is invisible by design. So how do you build a portfolio for it?"
tags = ["devops", "career", "portfolio"]
categories = ["meta"]
+++

DevOps is invisible work. Not invisible like "people don't appreciate it" — invisible by design. If everyone's noticing what you do, something is probably on fire.

The best day in operations is the one where nothing happens. Your alerting stays quiet. Your deployments roll out without anyone pinging you. Your infrastructure handles the traffic spike because you sized it right six months ago. Nobody sends you a thank-you Slack message for the incident that didn't happen.

This creates a problem when someone asks what you've been doing for the last decade.

## The Portfolio Problem

Frontend engineers can show you a website. Designers can show you a Figma file. Mobile developers can hand you their phone. When a DevOps engineer interviews, we get "tell me about a time you..." and hope the interviewer can follow along.

The work itself is scattered across private repos, internal wikis, classified environments, and infrastructure that's been decommissioned. The most interesting things I've built are behind NDAs. The second most interesting things were in environments so locked down that screenshots weren't an option.

And even if you could show it — what are you showing? A Terraform module? A CI pipeline? YAML? None of it means anything without the context of *why* those decisions were made.

## What Would a DevOps Portfolio Even Look Like?

I sat with this question for a while. The obvious answer — public repos with infrastructure code — felt hollow. Anyone can write a Helm chart. The hard part of this work isn't the syntax. It's knowing *when* to use a StatefulSet vs. a Deployment. It's knowing that threshold alerts are noise and burn-rate alerts catch real incidents. It's knowing which failure modes matter and which ones are theoretical.

So I started building labs. Not tutorials — labs. Each one demonstrates a specific set of production patterns, but the code is almost secondary. The real content is:

- **Decision documentation.** Why burn-rate alerting instead of thresholds? Why distroless containers instead of Alpine? Why this specific Alertmanager routing tree? The answers aren't in the YAML.

- **Alert math that's actually tested.** The [SRE Observability Lab](https://github.com/R055LE/sre-observability-lab) has `promtool test rules` running in CI. Synthetic time series prove the burn-rate math works before it ever touches a cluster. Most teams don't test their alerts at all.

- **Chaos scenarios with expected outcomes.** Five fault injection scripts, each paired with a document describing exactly which dashboards change and which alerts fire. The scripts are trivial. The expected-behavior docs are the point — they prove you understand the system's failure modes, not just how to break it.

- **Runbooks linked from alerts.** Every alert annotation includes a `runbook_url` pointing to an actionable document. CI validates that those links aren't broken. This is how on-call actually works — the alert tells you where to look.

## The Secondary Benefit

Building these labs forced me to articulate decisions I'd been making on instinct for years. I know how to set up burn-rate alerting. I've done it. But explaining *why* the fast-burn window is 5 minutes and the slow-burn window is 30 minutes — writing that down in a way that someone else can follow — is a different skill.

Operations engineers tend to accumulate knowledge through experience and then deploy it through muscle memory. That's fine when you're the one on-call. It's not fine when you're trying to explain your value to someone who's never been paged at 3 AM.

This is as much about reclaiming my own ability to communicate this work as it is about having something to show. The concepts are there. The experience is there. The muscle of talking about it clearly — that needed exercise.

## What's Next

More labs, more writing. The next posts will dig into specific technical decisions — burn-rate alerting math, chaos engineering methodology, what "production-grade" means for a container. Less meta, more substance.

If you're an infrastructure engineer staring at the same portfolio problem, the approach is simple: build the thing you know how to build, then write down why you built it that way. The "why" is the portfolio. The code is just evidence.
