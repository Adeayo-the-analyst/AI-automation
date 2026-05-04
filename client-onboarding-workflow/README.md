# Client Onboarding Workflow

## What this is

A Make.com automation that handles the complete client onboarding sequence from a single form submission. When a prospect submits the onboarding form, the system creates a named client folder in Google Drive, sends a personalized welcome email, creates a follow-up task in ClickUp with a 24-hour deadline, and delivers an internal Slack notification confirming all three actions completed — in seconds, without manual intervention.

---

## Why it was built this way

Most onboarding automations are built for the happy path. This one was built for what goes wrong.

Every submission passes through a security and validation filter before any action is taken. A normalization layer converts the raw form payload into ten clean variables — the only data source for every downstream module. All action modules carry Break error handlers that route failures to Incomplete Executions rather than letting them disappear. A fallback route fires an immediate #errors Slack notification on every blocked submission, with enough detail to investigate and reprocess.

The welcome email was designed to feel human, not automated. First name only. A specific reference to the client's selected project type. A conversational sign-off. Tested against one question: would a real person send this exact message?

---

## Architecture

```
Tally Form → Router
             ├── Route 1: security_check filter → Normalizer → Google Drive
             │                                              → Gmail
             │                                              → ClickUp
             │                                              → Slack #onboarding
             └── Route 2: Fallback → Slack #errors
```

---

## Tools

| Tool | Role |
|---|---|
| Make.com | Workflow orchestration |
| Tally | Form submission and instant webhook trigger |
| Google Drive | Automated client folder creation |
| Gmail | Personalized welcome email |
| ClickUp | Task creation with assignee and 24-hour deadline |
| Slack | Internal notification (#onboarding) and error alerting (#errors) |

---

## Defensive Architecture Decisions

**Normalization layer** — Raw form data never touches downstream modules directly. One place to fix if anything changes upstream.

**Source variable** — Every submission is tagged with its origin. Adding a second trigger source requires no downstream changes.

**Fallback route** — Blocked submissions surface in #errors immediately. Nothing fails silently.

**Tally duplicate prevention** — Platform-native email deduplication, more reliable than a filter.

**Break handlers on all action modules** — Failures route to Incomplete Executions for manual review and reprocessing.

**Human-first email design** — Automation that feels automated creates distance before the relationship begins.

---

## Failure Modes Documented

17 failure modes across three categories — security and validation, action module failures, and operational and platform failures — each with severity rating and resolution steps. Full registry in Document 2.

---

## Documents Included

| Document | Contents |
|---|---|
| Document 1 — System Architecture | What the system does, architecture decisions, business outcomes, platform constraints |
| Document 2 — Failure Mode Registry | 17 failure modes with severity ratings and resolution steps |
| Document 3 — Setup and Configuration Record | Full module configurations, security setup, pre-handover checklist |
| Document 4 — Operating Guide | Healthy and broken signals, weekly checks, things never to do, emergency reference |
| Portfolio Evidence Pack | Annotated screenshots evidencing live build and operation |

---

## Note on Credentials

No API keys, webhook URLs, OAuth credentials, or client data are included in this repository. All sensitive configuration is held in Make.com and Google Cloud Platform.

---

Built as Project 2 of a two-project AI automation portfolio.  
Adeayo Adewale
