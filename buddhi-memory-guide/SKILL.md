---
name: buddhi-memory-guide
description: Guides an AI assistant in using Buddhi's connectors to read company memory and to add good memory to it. Use when the user asks what the company knows or decided, asks to remember, save or record something for the team, asks how to connect an assistant to Buddhi, or when substantial work has produced a decision, fix, finding or customer request that a colleague would need later.
---

# Working with company memory through Buddhi's connectors

Buddhi is the company's shared memory. An AI assistant reaches it only through a **connector**. There is no other way in: if no connector is available in this session, you cannot read or add company memory, and you must say so instead of pretending.

What an assistant adds is only as good as what it writes. A vague or bundled statement wastes a reviewer's time and, if approved, misleads everyone who recalls it later. Most of this guide is about writing memory that is worth keeping.

## 1. Find out which connector you have

Check in this order and use the first that is present.

| Connector | How to recognise it | What you can do |
|---|---|---|
| **Buddhi tools** (the MCP connection) | Tools named `recall`, `get_fact`, `submit_memory_candidate`, `list_memory_candidates`, `list_memories`, `remember` | Read and propose. This is the full connector; prefer it |
| **`buddhi` command line** | `buddhi whoami` runs and prints a workspace | Propose a file with `buddhi push`. It cannot read |
| **Product API** | You are code running inside a product that holds a Buddhi key | Read and propose over HTTPS at `/api/v1` |
| **None** | None of the above | Nothing. Tell the user, and see [connectors.md](connectors.md) for how they can connect this assistant |

The Buddhi tools reach you the same way whichever app you run in: the Claude Code plugin, a Claude connector, or an entry in the MCP settings of Claude Desktop, Cursor, Windsurf or VS Code. Setup for each is in [connectors.md](connectors.md). Read it only when the user asks how to connect, or when no connector is present.

Every connector acts as one person, with that person's access. You see what they may see and nothing more. A denial is a real answer, so report it; do not look for a way around it.

## 2. Read before you answer

Before answering a question about the company's decisions, products, customers, people or processes:

- call `recall` with a plain-language question. It returns the memories this person may see, each with its source, author and date
- call `get_fact` for a single authoritative value such as a metric, when you know its key
- say where the answer came from. If memory has nothing, say that plainly and do not fill the gap with a guess
- if two memories disagree, show both and their dates. Do not pick one silently

## 3. Decide what is worth adding

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

If the user named something specific to keep, propose that. Otherwise choose the few items that matter. If nothing qualifies, say so and propose nothing.

## 4. Check it is not already known

Call `recall` for each item first. Skip what memory already says. If your item corrects or updates an existing memory, say so in the statement ("replaces the earlier 30-day limit") so the reviewer sees the change.

## 5. Write each one to stand alone

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

## 6. Submit through your connector

You **propose**; a person approves. A proposal cannot be recalled by anyone until it is approved.

**Buddhi tools.** Call `submit_memory_candidate` once per item:

| Field | What to send |
|---|---|
| `text` | The standalone statement |
| `title` | A short label, a few words |
| `category` | `decision`, `fact`, `customer_feedback`, `process`, `task` or `lesson` |
| `confidence` | 0 to 1. Something the user stated outright is high; something you inferred is lower. The reviewer relies on it, so be honest |
| `evidence` | Who said it, or where and when it was observed |
| `links` | Source addresses, if any |
| `source_memory_ids` | Ids of existing memories it builds on or replaces, from `recall` |
| `scope` | Only when it is obvious where it belongs. The reviewer decides. Never `publishable` |
| `run_id` | The same value for every proposal in this conversation |

Use `submit_memory_candidate` in preference to `remember`. `remember` takes only text, title, scope and a source link, so the reviewer gets no category, confidence or evidence. For most keys it also only proposes.

**`buddhi` command line.** Write the statement to a file and run:

```
buddhi push ./statement.md --title "Short label" --scope engineering
```

**Product API.** `POST /api/v1/memory-candidates` with `content`, and optionally `title`, `category`, `scope`, `confidence`, `evidence` (`note`, `links`, `memoryIds`) and `runId`. A `202` with `pending_review` means it is waiting for a person.

**No connector.** Tell the user Buddhi is not connected in this session and show the statements you would have proposed, so they can add them in Buddhi themselves.

## 7. Report honestly

Tell the user, briefly:

- what you proposed, as a short list of titles
- that each is **waiting for review** in Buddhi under Governance → Agent Memory. Never say "saved" or "remembered"
- anything you chose not to propose and why, if they might have expected it

To check on earlier proposals, call `list_memory_candidates`, optionally with `status` set to `pending`, `approved` or `rejected`.

## Limits to respect

- A handful of proposals per conversation is right. Twenty is the most one conversation can have waiting, and one key can have a hundred.
- If a submission says too many proposals are waiting, stop and tell the user the inbox needs reviewing first.
- Proposing the same text twice returns the first proposal; it is not an error.
- You cannot approve your own proposals. Do not look for a way to.
