---
name: buddhi-remember
description: Proposes durable knowledge from the current conversation to the company's Buddhi memory, where a person reviews it before it becomes company memory. Use when the user asks to remember, save or record something for the team, says a decision, fix or finding should not be lost, or at the end of substantial work that produced knowledge a colleague would need later.
---

# Proposing memories to Buddhi

Buddhi is the company's shared memory. This skill covers one thing: getting knowledge from a conversation **into** it, well.

You do not write company memory. You **propose**, and a person approves. A proposal cannot be recalled by anyone until it is approved, so never tell the user something was "saved" or "remembered" — say it was proposed for review.

## 1. Decide what qualifies

Propose knowledge that someone else at the company would need later:

- a **decision** and the reason for it
- a **fact** that was established or corrected
- **customer feedback**: what a customer asked for or complained about
- a **process** or fix that worked ("when X happens, do Y")
- a **lesson** from a bug, an incident or an approach that failed
- a **task** only when it is a standing commitment others must know about

Do not propose:

- the conversation itself, a summary of what you did, or your own reasoning and speculation
- anything already in memory
- secrets, credentials, API keys, or personal information about individuals
- temporary state: what is being worked on now, to-dos, things true only today
- code or configuration that already lives in a repository

If the user named something specific to keep, propose that. Otherwise review the conversation and choose the few that matter. If nothing qualifies, say so and propose nothing. Every proposal costs a person's attention to review.

## 2. Check it is not already known

If the `recall` tool is available, search for each item first. Skip what memory already says. If your item corrects or updates an existing memory, say so in the statement ("replaces the earlier 30-day limit").

## 3. Write each one to stand alone

A colleague will read this months later with no access to this conversation.

- One proposal per piece of knowledge. Do not bundle several into one.
- Name the people, systems, customers and dates. "We" and "it" mean nothing later.
- Say **why**, not only what. The reason is usually the valuable part.
- Use absolute dates ("12 October 2026"), never "yesterday" or "next sprint".
- State it as the company's knowledge, not as a story about the chat.

| Weak | Strong |
|---|---|
| "We decided to go with the second option." | "On 12 Oct 2026 the platform team chose Razorpay over Stripe for India payments, because Stripe's UPI support needs a separate entity." |
| "Fixed the import bug." | "When the Orion importer times out on accounts over 10k rows, raise the batch size from 50 to 500. The default is too small for large accounts." |
| "Customer wants exports." | "Halden Logistics asked twice in October 2026 for CSV export of invoices; they reconcile in Excel monthly." |

## 4. Submit

**With the Buddhi tools connected** (preferred), call `submit_memory_candidate` once per item:

| Field | What to send |
|---|---|
| `text` | The standalone statement |
| `title` | A short label, a few words |
| `category` | `decision`, `fact`, `customer_feedback`, `process`, `task` or `lesson` |
| `confidence` | 0 to 1. Something the user stated outright is high; something you inferred is lower. Be honest: the reviewer relies on it |
| `evidence` | Who said it, or where and when it was observed |
| `links` | Source addresses, if any |
| `scope` | Only when it is obvious where it belongs. The reviewer decides. Never `publishable` |
| `run_id` | The same value for every proposal in this conversation |

**Without the tools, if the `buddhi` command line is installed**, write the statement to a file and run:

```
buddhi push ./statement.md --title "Short label" --scope engineering
```

It reports that the file was sent for review.

**With neither available**, do not pretend. Tell the user Buddhi is not connected in this session and show them the statements you would have proposed, so they can add them in Buddhi themselves.

## 5. Report back

Tell the user, briefly:

- what you proposed, as a short list of titles
- that each is **waiting for review** in Buddhi under Governance → Agent Memory
- anything you chose not to propose and why, if they might have expected it

If a submission returns an error saying too many proposals are waiting, stop and tell the user the inbox needs reviewing first.

## Limits to respect

- A handful of proposals per conversation is right. Twenty is the most a single conversation can have waiting.
- Proposing the same text twice returns the first proposal; it is not an error.
- You cannot approve your own proposals. Do not look for a way to.
