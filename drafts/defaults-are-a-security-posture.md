# Blog draft — Local LLM runtime eval (Phase 1, post 1)

**Status:** skeleton + in-voice preliminary prose. Not final. Review, cut, push back.
**Current location:** `r055le.github.io/drafts/` (outside Hugo's `content/` — won't render even if `draft = true` gets flipped).
**Promotion path:** when ready, move to `content/post/<slug>.md` and flip `draft = false`.
**Companion artifacts:** `agentic-platform-lab/eval/results/ollama.md`, `agentic-platform-lab/eval/results/llama-cpp.md`, `agentic-platform-lab/eval/raw-notes/*.md`.
**Pairs with:** "The Invisible Portfolio" (first post). This one is the first *technical* post — first artifact of the agentic-platform-lab.

---

## Title candidates

Strong to weak, tell me which one survives:

1. **"Defaults Are a Security Posture"**
   — Flat. Thesis-first. The whole point of the post in five words.
2. **"Fast, Friendly, and Leaking Your Prompts: Evaluating Local LLM Runtimes"**
   — Punchier, the leak finding is genuine so it earns the framing.
3. **"Unsafe by Default: Two LLM Runtimes, Ten Footguns"**
   — Clickbait-adjacent. Probably fails the "I can't stomach talking like that" test.
4. **"If You Copy-Paste the Docs, Are You Safe?"**
   — The editorial stance as a question. My favorite, probably.

Leaning toward **4** with **1** as subtitle. Something like:

> # If You Copy-Paste the Docs, Are You Safe?
> ### Defaults are a security posture — evaluating local LLM runtimes for tenant-controlled infrastructure.

---

## Frontmatter (Hugo, matching the existing post format)

```
+++
draft = true
date = 2026-04-17
title = "If You Copy-Paste the Docs, Are You Safe?"
description = "Defaults are a security posture. Evaluating local LLM runtimes for tenant-controlled infrastructure — one thesis, two candidates, ten footguns."
tags = ["devsecops", "llm", "ai", "infrastructure", "containers"]
+++
```

---

## Opening — the premise

**Goal of this section:** set the thesis. Don't lead with the abstraction — lead with the scenario.

Draft prose:

> A competent engineer gets handed a task: stand up a local LLM runtime. The ask is vague, the timeline is tight, and the documentation's quickstart is right there on the project's README. They copy the `docker run` command, they watch the container come up, the API responds, and they move on to the actual work. They do not read the advanced configuration page. They do not grep the source for "auth". They do not run a port scan against their own container. They got the thing running. The ticket is closed.
>
> The question this post cares about is whether that engineer got burned.
>
> "Can it be hardened" is a different question than "is it safe." Every tool worth using can be hardened. The interesting question is whether the *defaults* — the state the tool ships in, the state it lands in when you follow the documented install — are themselves a reasonable security posture. Because nobody reads config the way they should. Nobody reads the advanced hardening guide. The default is the state the tool actually lives in, 95% of the time, on 95% of the servers running it. Shipping unsafe defaults is shipping an attack surface.
>
> I'm spending the next few weeks evaluating local LLM runtimes for a lab I'm calling `agentic-platform-lab` — the idea is to demonstrate an agent workload running on tenant-controlled, security-hardened Kubernetes infrastructure. Before any of the platform work starts, I need to pick a runtime. And I want to pick one that holds up under the question above.
>
> First two candidates are in. Here's what I found.

**Voice gut-check:** probably keep "95% of the servers running it" — specific, slightly editorial. Kill "the question this post cares about" if it feels too meta; could just say "the question I care about."

---

## The test setup (brief — nobody reads methodology sections)

- WSL2 + Docker, i9-12900H, CPU-only for Phase 1. GPU passthrough is Phase 2.
- Same model for both runtimes: **Qwen2.5-3B-Instruct-Q4_K_M** (~1.9 GB GGUF).
- Three probes: latency (p50/p95 across 10 runs), tool calling (5 structured-tool-call cases), concurrency (5 simultaneous requests).
- One editorial probe: **run the documented defaults and see what's reachable without credentials.**
- Scorecard uses A/B/C/F — default-safe weighted above hardenable. "Can be configured securely" is a C, not a B.

**Note:** don't sink time into explaining WSL or Docker. Reader either knows or doesn't care.

---

## Candidate 1: Ollama

**Frame:** easy to like, easy to deploy, default-configured for a threat model that isn't yours.

Draft prose:

> Ollama is legitimately good software. The API is clean, the model management is frictionless, SHA256 verification on model pulls is the right default, and on a 20-core CPU it serves a 3B model fast enough that you can develop against it without hating your life. First-token latency p95 was 0.28 seconds. Total response time p95 under ten seconds. It handled five concurrent requests without so much as a retry. If the question is whether Ollama works, the answer is yes.
>
> Then there's everything else.
>
> The documented quickstart is `docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama`. That `-p 11434:11434` publishes the service on every interface on the host, because that's what Docker's port-mapping shorthand does. The documentation does not use `-p 127.0.0.1:11434:11434`. The container inside binds `0.0.0.0:11434` regardless, so even a careful operator who corrects the host-port mapping still serves the API to every container on the same network. The container runs as UID 0. There is no authentication on any endpoint. Not weak authentication, not optional authentication — none. `/api/tags`, `/api/ps`, `/api/version`, `POST /api/pull`, `DELETE /api/delete`, `POST /api/create` are all reachable from any client that can land a TCP connection.
>
> A GHSA advisory (GHSA-f6mr-38g8-39rg, critical, 2024) describes this exact posture. The advisory's "patched versions" field is empty. The project's position is essentially that auth is not the runtime's job — put a reverse proxy in front. Which is a defensible engineering opinion and an indefensible default, because the vast majority of people running Ollama are running it exactly as documented.
>
> There's one more thing worth a paragraph. Ollama's default context window is 2048 tokens. The model I loaded has a native trained context of 32,768. Unless the caller explicitly sets `num_ctx`, Ollama silently clamps the context to 2k. The 32k model becomes a 2k model and nobody tells you. This is categorized as a footgun, not an attack surface — but it's the same species of problem as the auth default: the tool ships a configuration that's wrong for the common case, and the only way to find out is to read the documentation more carefully than anyone reads documentation.
>
> I'll give Ollama a real win before moving on: it does have Origin-header DNS rebinding protection. A browser attack from `evil.com` cannot read your Ollama instance through a tricked-up DNS resolver. Credit where due — that one's correctly configured.

**Scorecard summary for the post:**

| | Grade |
|---|---|
| Auth (default) | F |
| Container user | F |
| Context length default | F (silent clamp) |
| DNS rebinding | A |
| Performance (3B CPU) | A |
| Model management | A |

**Possible pull-quote for a sidebar / tweet-sized summary:**

> *"Fast to start, fast to serve, and fast to hand an attacker the keys. Ollama's defaults are a security appliance aimed at your foot."*

That's the verdict line in the raw scorecard. It's a little hot — gut-check whether it's too cute. I think it lands because it's specific; the "aimed at your foot" isn't decorative, it describes a real thing. Ruling: keep.

---

## Candidate 2: llama.cpp server

**Frame:** the engine underneath a lot of things, including Ollama. More serious, more configurable, and shipping a live prompt-leak endpoint on by default.

Draft prose:

> Ollama is, under the hood, a friendly wrapper around llama.cpp's inference engine. Running the upstream `llama-server` directly skips the wrapper and gets you closer to the metal, at the cost of losing the product polish. On the same box with the same model, llama.cpp served first-token responses in 0.08 seconds at the median and full responses in about 4.5 seconds — 35% faster end-to-end than Ollama. It got five out of five tool-calling probes right, where Ollama got four out of five (same model, same quant — the difference is that llama.cpp has jinja chat-templating enabled by default, and the Qwen template renders tool-use correctly when jinja's involved). It honors the model's native 32,768 context window instead of silently stepping it down. It has a `/health` endpoint. It has a `--metrics` flag that stands up a Prometheus-compatible endpoint. It has an `--api-key` flag that turns on bearer-token authentication. These are all things Ollama either doesn't have or punts to the reverse proxy. On every axis that matters for "treat the server itself as real infrastructure," llama.cpp wins.
>
> And then it exposes `/slots`.
>
> The `/slots` endpoint returns the live state of every parallel inference slot — including the prompts and completions currently flowing through them. On by default. Unauthenticated. In any multi-tenant deployment, or any shared-network deployment, any reachable client can poll this endpoint and read what other users are asking the model about. The fix is `--no-slots`. The default is **`--slots`**.
>
> It also doesn't check the Origin header, so a malicious webpage on the same LAN can drive your model through a user's browser. That's a posture that Ollama actively blocks and llama.cpp does not.
>
> Net: llama.cpp ships with more hardening *levers* than Ollama and a worse default security *posture* on two specific axes. Both of those are worth the distinction. If I had to grade the "out of the box, default install, competent-but-busy engineer" scenario, llama.cpp is arguably worse than Ollama — because the failure mode isn't just "an attacker can hit your API," it's "an attacker can read your other users' prompts in real time." That's a different kind of leak.
>
> The flip side: the hardening fix is *smaller*. One flag for auth, one flag to close the slots endpoint, one flag to expose metrics. No reverse proxy. No sidecar exporter. That's actually a better story for a lab, because the before/after is legible. You can show the hardening as admission-policy configuration rather than as an architectural band-aid.

**Pull-quote candidate:**

> *"A serious engine wearing a cheap suit — faster than Ollama, more honest about hardening levers, and shipping a live prompt-leak endpoint on by default."*

---

## Head-to-head table (probably the image people screenshot)

| | Ollama 0.21.0 | llama.cpp b8833 |
|---|---|---|
| TTFT p95 | 0.28s | 0.33s |
| Total p95 | 9.77s | 6.02s |
| Tool calling | 4/5 | 5/5 |
| Concurrency speedup (5x) | 3.77x | 4.25x |
| Default context | 2048 (clamped from 32k) | 32k (native) |
| Built-in auth | — | `--api-key` |
| Built-in metrics | — | `--metrics` |
| DNS rebinding block | ✅ | ❌ |
| Live prompt leak surface | — | `/slots` on by default |
| Container user | root | root |
| Image version self-ID | yes | no (Ubuntu label only) |

This table is what lands on the reader. Everything above is context for it.

---

## The through-line — why this matters for tenant infra

**Frame:** tie it back to the lab's thesis. Don't be cute. One paragraph.

Draft:

> The reason any of this matters: I'm building toward a lab that runs an agent workload on tenant-controlled Kubernetes — "tenant-controlled" meaning inside whatever account or cluster the customer actually owns, subject to whatever compliance and admission policy they've already invested in. That context changes the runtime evaluation materially. A runtime that ships "put a reverse proxy in front of me" as its security story is a runtime that makes my lab's Kyverno admission policies have to reach further into the cluster to cover for it. A runtime whose `/metrics` endpoint is a flag flip is a runtime that fits neatly under an existing Prometheus operator. Defaults don't just matter for the engineer typing `docker run` — they matter for every downstream operator who has to compensate for a bad one.
>
> The Trivy incident earlier this year was the mental model I've been carrying through this evaluation. Trivy is — was — a tool every DevSecOps shop ran, because it was the default, and it caught things. When it turned out Trivy itself had a vulnerability that affected every project running it for dev scans, the thing that made it a problem at scale was that it was *everywhere by default*. "Safe by default" and "scaled by default" are the same thing, viewed through opposite lenses. Something unsafe by default, deployed by thousands of engineers who followed the quickstart, is the exact shape of the next supply-chain event.

**Note:** This paragraph is where the -ness dismissal might land naturally if I can find the right fulcrum. Candidates: "quickstart-ness", "productivity-theater-ness". Probably not — feels forced. Let the phrases come up organically in revision.

---

## What the hardening layer becomes in the lab

**Goal:** close the loop. The defaults-matter critique is only legitimate if I'm going to fix it. Here's the fix shape.

Bullet draft:

- Custom container image that drops to a non-root UID, pins the runtime by digest, bakes in the hardening flags (`--api-key-file`, `--no-slots`, `--metrics`, binds to localhost or unix socket).
- Authenticated reverse proxy (the lab's `tool-proxy` does double duty — ServiceAccount token verification at the edge).
- Kyverno admission policies that refuse to admit a pod whose image isn't the approved digest, whose container runs as UID 0, or whose command line doesn't include the required flags. "Enforce the hardening in the cluster" rather than "hope the operator read the docs."
- Prometheus scrape config and OTel trace export so the runtime is an observability citizen, not a black box.

None of this is particularly novel work. What the lab does is stitch it together as one admission-policy-enforced deployment artifact and document *why each piece is there* — with the raw scorecards as the "before" state.

This is where the lab earns its keep. The runtime eval is the problem statement. The admission-policy-enforced hardened deployment is the answer.

---

## What comes next (keep short — this is a post, not a roadmap)

- Third CPU-viable runtime: LocalAI. Finish Phase 1.
- GPU passthrough + Phase 2 rerun with a 7B model, adding vLLM and TGI to the board.
- Then the actual lab build: hardened image, tool proxy, Kyverno policies, end-to-end agent workload.
- Companion posts on the tool-proxy authz model (SA tokens + OPA) and the Kyverno policy set. Those are their own arguments.

---

## Ending — just stop

Candidate ending (don't wrap it up, don't call to action, just land the verdict):

> Both runtimes will go into the lab. Ollama as the "this is what people actually deploy" baseline, llama.cpp as the "this is what happens when you treat the runtime as infrastructure instead of as a product." Both of them are going to be wrapped in enough admission policy that the defaults don't get a vote. That's the part the lab exists to demonstrate. The evaluation was just to earn the right to be opinionated about it.

Or, shorter and probably better:

> Both runtimes will go into the lab. Ollama as the "this is what people actually deploy" baseline, llama.cpp as the "this is what happens when you treat the runtime as infrastructure." Both of them wrapped in enough admission policy that the defaults don't get a vote. That's the lab.

Rule from voice profile: no bow, no takeaway, no "if you're also evaluating runtimes, hit me up." Just stop.

---

## Notes to self — things to decide on revision

- **Length target:** the first post was ~1,500 words. This one has more table/chart material and more raw data; probably lands longer (2,000–2,500). Watch for bloat in the candidate-1 and candidate-2 sections.
- **Pull-quotes:** two strong candidates above. Pick one for the header hero, one for a mid-post callout. Probably the Ollama "aimed at your foot" line up top.
- **Scorecards:** link out to `eval/results/ollama.md` and `eval/results/llama-cpp.md` in the lab repo. Don't dump the full scorecard inline — that's what the repo is for. Post shows the narrative, repo shows the receipts.
- **Screenshots / diagrams:** none strictly needed, but the head-to-head table would benefit from being a real styled table (CSS) rather than markdown. Defer to theme.
- **Slug:** `defaults-are-a-security-posture` or `copy-paste-the-docs`. Leaning toward the former — it's the idea that survives beyond this post.
- **Cross-link:** "The Invisible Portfolio" sets up that the labs are the artifact. This post is the first lab artifact that actually *produces something*. Worth a one-line callback early, not more.
- **Honesty bit to include somewhere:** this lab isn't built yet — the eval is. Don't overstate. The post should read as "here's the first concrete thing from this lab, more coming" not "here's the finished demo."
- **CVE/date receipts:** version numbers and eval date are in the scorecards; don't restate in prose. Link instead.
- **Thing to resist:** the urge to add a "how to evaluate an LLM runtime for your org" generalized framework section. That's the LinkedIn version of this post. The specific evaluation of two specific runtimes *is* the framework, implicitly. If someone wants the framework they can read the rubric in the repo.

---

## Open questions for review pass

1. Is the Trivy reference doing work, or is it a crutch? It's the most concrete "defaults matter" example in the field, but some readers won't know it. Maybe one sentence explaining it vs. just naming it.
2. The "serious engine wearing a cheap suit" line — keeper or cut? Slightly purple. I like it but it's the kind of line I'd circle back on in editing.
3. Should the head-to-head table come earlier, at the top, and then the prose justifies it? Or where it is now (after both candidates are introduced)? Argument for early: readers skim, the table is the artifact they'll screenshot. Argument for late: the table is meaningless without the context, and putting it early makes the post feel like a listicle.
4. Do I want a section on *why Qwen2.5-3B specifically* as the test model? Probably no — it belongs in the rubric, not the post. But flag it as a cut candidate for the revision.
5. The last sentence — "the evaluation was just to earn the right to be opinionated about it" — very me or too cute? I think it works because it's self-deprecating about the evaluator role, which matches the voice. But it's a callback to the "earn the opinion" idea that isn't set up earlier in the post. Might need one sentence planted earlier to make it resonate.
