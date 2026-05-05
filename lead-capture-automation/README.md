# B2B Lead Capture Pipeline
**Automated lead capture with validation, duplicate detection, security token authentication, and full operational documentation.**

---

## What This System Does

This pipeline automatically captures inbound leads from any web form, validates them against five conditions, checks for duplicates, writes clean records to a CRM database, sends the lead a personalized confirmation email, and notifies the sales team on Slack — without any manual intervention required.

From submission to team notification takes less than five seconds.

---

## Business Outcomes

- **No leads fall through the cracks** — captures every submission automatically, 24/7
- **CRM stays clean without manual effort** — incomplete, malformed, and duplicate submissions never reach the database
- **Eliminates manual data entry** — no data entry clerk required; every record arrives validated and formatted
- **Unauthorized access blocked** — secret token authentication prevents bot and spam submissions
- **Sales team responds faster** — Slack notification arrives within five seconds of every valid submission
- **Failures are visible, not silent** — error handlers store failed executions for manual review; nothing is lost

---

## Pipeline Architecture

```
Webhook
    └── security_check (token authentication)
            └── intake_validation (data quality — 5 conditions)
                    └── Airtable Search Records (duplicate check)
                            └── check_record (zero records gate)
                                    └── Airtable Create Record
                                            └── Gmail (confirmation email)
                                                    └── Slack (team notification)

Error paths:
Gmail failure → Break → Incomplete Executions
Slack failure → Break → Incomplete Executions
```

---

## Defensive Architecture

Every submission passes through six gates before writing anything to the database.

| Gate | What It Checks | Blocks |
|---|---|---|
| security_check | Token matches expected value | Unauthorized submissions — bots, bad actors |
| intake_validation — email present | Email field not empty | Submissions with no email address |
| intake_validation — name present | Name field not empty | Submissions with no name |
| intake_validation — company present | Company field not empty | Submissions with no company — invalid in B2B context |
| intake_validation — email format | Email matches `.+@.+\..+` pattern | Malformed email addresses |
| check_record | Zero matching records in Airtable | Duplicate submissions from same email |

---

## Tools

| Tool | Role |
|---|---|
| Make.com | Automation platform — runs the entire scenario |
| Airtable | CRM database — stores validated lead records |
| Gmail | Sends personalized confirmation emails to leads |
| Slack | Notifies sales team instantly on every new lead |
| Hoppscotch | Webhook testing tool used during build and QA |

---

## Failure Modes Documented

11 failure modes identified, tested, and documented during build. Full details in the Failure Mode Registry.

| # | Failure Mode | Severity | Auto-Handled |
|---|---|---|---|
| 1 | Missing submission fields | Low | Yes |
| 2 | Malformed email address | Low | Yes |
| 3 | Duplicate submission | Medium | Yes |
| 4 | Unauthorized webhook access | High | Yes |
| 5 | Gmail confirmation failure | Medium | Partial |
| 6 | Slack notification failure | Medium | Partial |
| 7 | Gmail connection expiration | Medium | No |
| 8 | Scenario deactivated accidentally | High | No |
| 9 | Operation limit reached | High | No |
| 10 | Airtable schema drift | High | No |
| 11 | Batch submission attempt | Medium | No |

Three high-severity failures cause complete system shutdown with no visible symptoms. All three are documented with monitoring protocols in the Failure Mode Registry.

---

## Documentation Delivered

Every project includes four documents:

| Document | Contents |
|---|---|
| System Architecture | What the system does, business outcomes, plain-English description of every component |
| Failure Mode Registry | 11 failure modes — triggers, system response, severity, human action required |
| Setup and Configuration Record | Every module configuration, credential types, pre-handover checklist with delivery confirmation |
| Operating Guide | What healthy looks like, symptom-response pairs, weekly and monthly checks, emergency quick reference |

---

## Build Notes

This system was built incrementally — one module confirmed before the next was added. Every failure mode in the registry was encountered during the build, not anticipated theoretically.

Notable real failures encountered and resolved:

- Slack private channel requiring manual bot invitation — `/invite @Make` required before notifications fire
- Duplicate records accumulating during failed test runs — resolved with search-before-create architecture
- Filter operator type mismatch — numeric vs text on `check_record` produces unpredictable results
- Queued bundles from failed runs causing infinite loop behavior — resolved by clearing execution history before resuming
- Token field mapping — black background (mapped variable) vs plain text (literal string) behave differently in filter conditions
- Company filter operator inverted — Equal to instead of Not equal to blocked valid submissions silently

These failures are documented because they represent the gap between tutorial-path automation and production-grade automation. A system hardened against failures its builder has actually encountered behaves differently under live conditions.

---

## Methodology

Every engagement begins with an operational intake process — a structured conversation designed to surface how the client's process actually works, not just how it's documented. The gap between those two things is where most automations fail.

Deliverables reflect defensive architecture philosophy:

> *I build automations that run without you watching them — because I build for what goes wrong, not just what goes right.*

---

## Portfolio

This project is part of a B2B automation portfolio targeting SaaS and service business operations — lead management, client onboarding, reporting, and sales workflow automation.

- **Upwork:** https://www.upwork.com/freelancers/~01d4a8ff7d9f3973e5?mp_source=share
- **LinkedIn:** linkedin.com/in/adeayo-adewale-30a472171
- **GitHub:** github.com/Adeayo-the-analyst

---

*May 2026*
