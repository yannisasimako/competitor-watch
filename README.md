# Competitor Watch

Twice a week, an AI checks what your competitors launched, how they describe themselves now, and which customers they announced. Then it tells you whether the reasons you give buyers to pick you (your "claims", e.g. "the only tool that does X") are still true. Each check becomes one Notion page. You review it; nothing changes without you.

Runs on [Claude Code](https://code.claude.com), Anthropic's AI assistant. Every step is a plain text file you can read and edit.

## How it works

1. Reads your three files (below).
2. Skips anything earlier checks already reported.
3. Visits each competitor's own website, docs and announcements.
4. Keeps only findings that touch one of your claims, and says whether each claim is still yours, now shared with a competitor, or no longer usable.
5. Reopens every link and deletes anything it can't confirm.
6. Writes a short Notion page anyone in the company can read.

You review the page, and what you keep updates your claim list for the next check.

## Your three files (in `context/`)

- **`positioning.md`**: your story. What you sell, to whom, what they use instead, why you win. Changes rarely.
- **`doctrine.md`**: your claim checklist. Each claim, whether it still holds, since when, and the proof. Changes after each review.
- **`competitors.md`**: who to check and where they post news.

Two files for claims on purpose: positioning is what you say, the checklist is whether it's still true. A competitor's launch updates the checklist, never your story.

Tip: fill in `positioning.md`, then ask Claude to draft `doctrine.md` from it and your website.

## Setup

1. **Copy this project** with the **Fork** button (private is fine), fill in the three files and save them to GitHub.
2. **In Notion, create a database** called **Competitor Watch** with these columns:

   | Column | Type |
   |---|---|
   | Run | Title |
   | Run date | Date |
   | Vendors with changes | Multi-select |
   | Findings | Number |
   | Claims at risk | Text |
   | Status | Select: `Not reviewed`, `Reviewed`, `Nothing new` |

3. **Connect Notion to Claude** at [claude.ai/customize/connectors](https://claude.ai/customize/connectors). Then ask Claude: *"give me the data source ID of my Competitor Watch database"*, and paste it into both files in `prompts/` where it says `<NOTION_DATA_SOURCE_ID>`.
4. **Schedule it** at [claude.ai/code/routines](https://claude.ai/code/routines) > **New task** (needs a paid Claude plan; it asks to connect GitHub the first time):
   - **Prompt:** `Read prompts/competitor-watch.md in this repo and follow it exactly.`
   - **Schedule:** Custom, `0 4 * * 2,5` (Tue and Fri, 4:00 UTC).
   - **Repository:** your copy.
   - **Model:** the most capable one.
   - **Connectors:** keep Notion (and Slack if you want alerts), remove the rest.
   - Click **Create**, then **Run now**.

The first check looks back 90 days. Click through a few findings to their sources before trusting the rest.

## Getting alerted

Want a Slack message when a check finds something? Connect Slack to Claude, then edit the last line of `prompts/competitor-watch.md` to e.g. `Notify: Slack, channel #competitor-watch`. Otherwise, turn on Notion notifications for the database.

## Reviewing a check

In Claude Code on your computer, open your copy of this project and type *"follow prompts/review.md"*. Claude shows the findings three or four at a time with a suggestion (keep, fix or drop), you answer for the group ("keep all", "drop 4"), and it updates `doctrine.md`, marks the page reviewed and offers to save the change to GitHub.

Or by hand: read the page, edit `doctrine.md` on GitHub, set the page to `Reviewed`.

## If something goes wrong

Open the run in the task's **History** to see what Claude did. Most failures are a Notion column name that doesn't match exactly, a wrong database ID, or Notion missing from the task's connectors.

MIT license.
