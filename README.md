<p align="center">
  <img src="assets/cronus-hero.png" width="220" alt="Cronus — the Titan carrying time">
</p>

<h1 align="center">Cronus</h1>

<p align="center"><b>EXECUTION &bull; SCHEDULING &bull; RECOVERY</b></p>

<p align="center"><b>Durable execution and scheduling engine for the Titans ecosystem.</b><br>
Jobs, DAG workflows, schedules, watchers, leases and workers that survive restarts and crashes — on your machine.</p>

<p align="center">
  <a href="https://github.com/titanslmachado/Atlas/releases/tag/cronus-0.1.0"><img alt="Release" src="https://img.shields.io/badge/release-0.1.0-1f6feb"></a>
  <img alt="Platforms" src="https://img.shields.io/badge/platforms-Windows%20x64%20%7C%20Linux%20x64-3fb950">
  <img alt="Install" src="https://img.shields.io/badge/install-one%20line-3fb950">
  <img alt="Signed" src="https://img.shields.io/badge/releases-Ed25519%20signed-8b5cf6">
  <a href="./LICENSE.md"><img alt="License" src="https://img.shields.io/badge/license-EULA-lightgrey"></a>
  <img alt="Free tier" src="https://img.shields.io/badge/free%20tier-yes-3fb950">
</p>

---

## Install

One line. No Git, no Rust, no admin rights. The installer pins the release
public key and the SHA-256 of the bootstrap; the bootstrap verifies the signed
catalog and every archive (hash + Ed25519) before anything runs.

**Windows x86_64** — PowerShell:

```powershell
irm https://github.com/titanslmachado/Atlas/releases/latest/download/Install-Cronus.ps1 | iex
```

**Linux x86_64**:

```bash
curl -fsSL https://github.com/titanslmachado/Atlas/releases/latest/download/install-cronus.sh | sh
```

Already have any Titan? Then it is just:

```bash
titans install cronus
```

Prefer a manual download? Signed archives per platform are on the
[cronus-0.1.0 release](https://github.com/titanslmachado/Atlas/releases/tag/cronus-0.1.0)
(`bin/cronus` inside; SHA-256 and signature in `signed-distribution-catalog.json`
on the [latest release](https://github.com/titanslmachado/Atlas/releases/latest)).
Official Titans releases are hosted in the Atlas repository. macOS and ARM64 are
not part of this release.

## What Cronus is

Cronus is the *execution* Titan. Long or heavy work — extraction, validation,
model downloads, tool installs, renders — is submitted to Cronus instead of
blocking an agent inline. Jobs run in lanes with **durable at-least-once
execution, idempotent claim and completion, checkpoints, lease recovery,
dead-letter handling and schedules**, and leave a durable trail. Results are
archived to Atlas through a durable outbox.

Cronus makes asynchronous and autonomous workloads durable: work survives
failures, processes can restart, and agents stay continuously operational —
without turning the execution layer into a knowledge store (that is Atlas).
It runs as a real background service (Windows Service / user daemon) and is
operated from a built-in **Workbench** in your browser: what is queued,
running, stuck, dead-lettered, scheduled, and who is doing the work.

**Core capabilities:** job queues &middot; DAG workflows &middot; workers &middot; scheduling &middot; watchers &middot; supervised daemons &middot; retries &middot; leases &middot; backpressure &middot; checkpoints &middot; failure recovery

## What you can do with it

| Capability | Tool | Result |
|---|---|---|
| Submit durable work | `cronus_job_submit {definition:{kind, owner, payload_ref, tags}}` | kinds: knowledge_extraction, maintain, quality_evaluation, tool_install, model_download, document_render, video/audio/image, … |
| Track it | `cronus_job_get` · `cronus_job_list` · `cronus_job_progress` · `cronus_job_artifacts` | status, checkpoints, progress %, output refs |
| Control it | `cronus_job_cancel`, `cronus_job_require_approval` · `cronus_job_resolve_approval` | governed cancel + human/agent approval gates |
| Schedule recurring work | `cronus_schedule_create` · `cronus_schedule_list` · `cronus_schedule_pause` | `@every` / cron-like presets |
| Workflows | `cronus_workflow_*` | multi-step runs with durable state |
| Recover | `cronus_recover`, `cronus_dead_letters` · `cronus_dead_letter_replay` | a killed worker's job is recovered, never double-claimed |
| Lanes & fairness | queue tools | dedicated heavy/simulation lane, backpressure, priority, DLQ |

**Reliability guarantees:** durable at-least-once execution, atomic/idempotent
claim and completion, stale-claim fencing, per-task panic isolation (one job
crashing never takes down the worker), lease recovery, durable archival
delivery through the outbox. Exactly-once external effects require a connector
that persists the Cronus idempotency key.

## The first 60 seconds

```bash
cronus status
```

```bash
cronus call cronus cronus_job_submit '{"definition":{"kind":"maintain","owner":"me","payload_ref":"atlas://acme/alpha","tags":["demo"]}}'
```

```bash
cronus call cronus cronus_job_list '{}'
```

Open the Workbench: `http://127.0.0.1:7743/workbench`.

## For AI agents

- Cronus speaks **MCP over stdio**; the installer wires it into the shared
  `titans` MCP server of the AI clients present on the machine.
- Payloads travel as refs (`coeus://`, `atlas://`), never inline bytes.
- Submit-and-poll: submit a job, then poll `cronus_job_get`; don't block.
- REST (`127.0.0.1:7743`) and gRPC are loopback-only by design.

## Capacity contract

Cronus publishes an operational floor, not an unbounded throughput claim: the
release gate requires ≥ 25 submits/s (p95 ≤ 100 ms) and ≥ 10 terminal
transitions/s (p95 ≤ 200 ms) over 1,000 durable jobs. Lane and worker
concurrency are explicit, configurable backpressure controls; exceeding a
configured capacity queues work instead of weakening claim fencing.

## Updates

Two independently signed channels — a public discovery catalog and a
distribution catalog — drive updates. Nothing changes without explicit
confirmation, and a version can be pinned.

```bash
titans versions check
```

## Free and paid

This release is **free**. Capabilities and versions that become paid are
published in the signed public catalog; rights you acquire for a version stay
with that version (see the [EULA](./LICENSE.md), §1.3).

## Security

Report vulnerabilities privately — see [SECURITY.md](./SECURITY.md).

## License

Proprietary. Use is governed by the [Titans End User License Agreement](./LICENSE.md).
Third-party components are listed in the SBOM inside every release archive.
This repository hosts the official product page; the source code is not
published.
