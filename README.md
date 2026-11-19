# 🔭 Competitor Watch

**Catch every move your competitors make, and see whether your pitch still holds up.**

It looks at what competitors released, how they describe themselves now, and which customers they announced, and checks each finding against your "claims": the reasons you give buyers to pick you, e.g. "the only tool that does X". Each check becomes one Notion page. You review it; nothing changes without you.

Runs on [Claude Code](https://code.claude.com), Anthropic's AI assistant. Every step is a plain text file you can read and edit.

## Start here

| You want to | Go to |
|---|---|
| **See what a check looks like** | [What you get](#-what-you-get): an example page. |
| **Set it up** | [Setup](#-setup): three steps in GitHub, Notion and Claude. |
| **Review a check** | [Reviewing a check](#-reviewing-a-check): keep, fix or drop each finding. |
| **Know if it's for you** | [Questions people ask](#-questions-people-ask) |

[How it works](#%EF%B8%8F-how-it-works) · [What you get](#-what-you-get) · [Your three files](#-your-three-files) · [Setup](#-setup) · [Alerts](#-getting-alerted) · [Reviewing](#-reviewing-a-check) · [Questions](#-questions-people-ask) · [Troubleshooting](#%EF%B8%8F-if-something-goes-wrong)

```mermaid
flowchart LR
    A[Your 3 files] --> B[AI checks competitors]
    B --> C[Notion page]
    C --> D[You review]
    D -->|kept findings update<br>your claim checklist| A
```

## ⚙️ How it works

| Step | What happens |
|---|---|
| **Read** | Your three files: your story, your claims, your competitor list. |
| **Skip** | Anything earlier checks already reported. |
| **Look** | Each competitor's own website, docs and announcements. Never articles about them. |
| **Judge** | Keeps only findings that touch one of your claims: still yours, now shared with a competitor, or no longer usable. |
| **Verify** | Reopens every link, confirms every quote and date, deletes what it can't confirm. |
| **Write** | One short Notion page anyone in the company can read. |

**Read-only. Every link rechecked. Nothing changes without your review.**

## 👀 What you get

A Notion database that fills up with one page per check. This example uses a made-up expense tool and made-up competitors:

![A Notion table of six checks: run date, competitors with changes, number of findings, the claim at risk and whether marketing reviewed it](docs/example-table.png)
*Every check in one table: what changed, which claim is at risk, what's still waiting for review*

Open a check and you get one page:

| | |
|---|---|
| ![Top of an example check page: properties, the period covered and a two-paragraph summary saying the core claim still holds, with one part now shared by Competitor B](docs/example-summary.png) | ![One finding: Competitor B now blocks out-of-policy payments in a beta, how often it has appeared, why it matters and what the source does not show](docs/example-finding.png) |
| *The short version: does your main claim still hold?* | *One finding: what happened, why it matters, what it doesn't prove* |

## 📁 Your three files

All in `context/`:

| File | What it is | Changes |
|---|---|---|
| `positioning.md` | Your story: what you sell, to whom, what they use instead, why you win. | Rarely |
| `doctrine.md` | Your claim checklist: each claim, whether it still holds, since when, and the proof. E.g. *"Set up in a day": no longer only ours, Competitor D matched it on Oct 30 (link).* | After each review |
| `competitors.md` | Who to check and where they post news. | When the list changes |

Two files for claims on purpose: positioning is what you say, the checklist is whether it's still true. A competitor's launch updates the checklist, never your story.

> 💡 **Tip:** fill in `positioning.md`, then ask Claude to draft `doctrine.md` from it and your website.

## 🚀 Setup

| Where | What to do |
|---|---|
| **1. GitHub** | **Fork** this project (private is fine), fill in the three files in `context/` and save them. |
| **2. Notion** | Create a database called **Competitor Watch** with the six columns below. |
| **3. Claude** | Connect Notion at [claude.ai/customize/connectors](https://claude.ai/customize/connectors), add the database ID to the instructions, then schedule the check. |

<details>
<summary><b>The Notion columns</b></summary>

<br>

Names must match exactly.

| Column | Type |
|---|---|
| Run | Title |
| Run date | Date |
| Vendors with changes | Multi-select |
| Findings | Number |
| Claims at risk | Text |
| Status | Select: `Not reviewed`, `Reviewed`, `Nothing new` |

</details>

<details>
<summary><b>Adding the database ID</b></summary>

<br>

Ask Claude: *"give me the data source ID of my Competitor Watch database"*. Paste it into both files in `prompts/` where it says `<NOTION_DATA_SOURCE_ID>`, and save them to GitHub.

</details>

<details>
<summary><b>Scheduling the check</b></summary>

<br>

At [claude.ai/code/routines](https://claude.ai/code/routines) > **New task**. Needs a paid Claude plan; it asks to connect GitHub the first time.

| Field | Enter |
|---|---|
| Prompt | `Read prompts/competitor-watch.md in this repo and follow it exactly.` |
| Schedule | Custom: `0 4 * * 2,5` (Tue and Fri, 4:00 UTC) |
| Repository | Your copy |
| Model | The most capable one |
| Connectors | Keep Notion (and Slack for alerts), remove the rest |

Click **Create**, then **Run now**.

</details>

> ⚠️ The first check looks back 90 days. Click through a few findings to their sources before trusting the rest.

## 🔔 Getting alerted

Want a Slack message when a check finds something? Connect Slack to Claude, then edit the last line of `prompts/competitor-watch.md` to e.g. `Notify: Slack, channel #competitor-watch`. Otherwise, turn on Notion notifications for the database.

## ✅ Reviewing a check

In Claude Code on your computer, open your copy of this project and type *"follow prompts/review.md"*. Claude:

1. Shows the findings three or four at a time, each marked **keep**, **fix** or **drop**.
2. Takes your answer for the group ("keep all", "drop 4").
3. Updates `doctrine.md`, marks the page reviewed and offers to save the change to GitHub.

**Or by hand:** read the page, edit `doctrine.md` on GitHub, set the page to `Reviewed`.

## ❓ Questions people ask

<details>
<summary><b>What does it cost?</b></summary>

No subscription of its own. Scheduled checks need a paid Claude plan, and each check uses part of that plan's usage. The pages go to your existing Notion workspace.
</details>

<details>
<summary><b>Does it work for my market?</b></summary>

If your competitors publish news, docs or customer stories online, yes. It knows nothing about your market until you fill in the three files, and it's only as sharp as your claim checklist.
</details>

<details>
<summary><b>Can I use something other than Notion?</b></summary>

Yes. Notion is what it's set up for, but the instructions are plain text. Connect the tool you use to Claude (e.g. Google Docs), then ask Claude to rewrite the "write the page" step in `prompts/competitor-watch.md` and the matching steps in `prompts/review.md` for it.
</details>

<details>
<summary><b>Will it change my files or my website?</b></summary>

No. A check only reads the web and writes one page. Your claim checklist changes only when you review, and only with your yes.
</details>

<details>
<summary><b>Can it make things up?</b></summary>

AI can. That's why every check reopens each link, confirms each quote and date, and deletes what it can't confirm. Still, click through the first few findings before you trust it.
</details>

<details>
<summary><b>What can't it see?</b></summary>

Anything competitors don't publish: roadmaps, sales conversations, gated content, and often pricing. Some sites block automated visits; those competitors show up as "not covered" and are tried again next time.
</details>

<details>
<summary><b>How many competitors, how often?</b></summary>

The original setup checks 25 competitors twice a week. Add or remove competitors in `competitors.md` and change the schedule in the task's settings. Each check only reports what's new since the last one, so more often means shorter pages, not more noise.
</details>

<details>
<summary><b>Can my team read the pages?</b></summary>

Yes, that's who they're written for: plain words, no internal shorthand. Share the Notion database with sales, product or anyone who gets asked about competitors.
</details>

## 🛠️ If something goes wrong

Open the run in the task's **History** to see what Claude did. Most failures are a Notion column name that doesn't match exactly, a wrong database ID, or Notion missing from the task's connectors.

---

MIT license
