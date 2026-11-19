# Competitor Watch

An AI assistant that checks your competitors twice a week. It looks at what they released, how they now describe themselves, and which customers they announced. Then it checks each finding against what you say about your own product, and tells you which of your claims still hold. It writes one page per check to Notion. It never changes anything by itself: you review each page and decide what to keep.

It runs on Claude Code, Anthropic's AI assistant for working with files and the web. The instructions are plain text files, so you can read and change every step.

---

## How it works

Each check follows the same six steps:

1. **Reads what you say about your product.** Three files you fill in once: your story (`positioning.md`), the list of claims you make and whether each still holds (`doctrine.md`), and your competitor list (`competitors.md`).
2. **Reads what earlier checks found**, so it only reports what's new since the last one. The first check, and any competitor you add later, looks back 90 days.
3. **Looks at each competitor** for four things: new releases, new messaging on their website, new customers in your market, and pricing changes. It only cites the competitor's own website, docs and announcements, never articles written about them.
4. **Checks each finding against your claims.** For each one: which of your claims does it touch, and is that claim still yours, now something competitors can say too, or no longer usable? Anything that touches none of your claims is dropped.
5. **Double-checks before writing.** It reopens every link, confirms the quote is really there and the date is right, and deletes anything it can't confirm. If it can't reach more than half the competitors' websites, it writes a short "Failed run" page instead of a half-finished one.
6. **Writes one Notion page** anyone can read without background: a short summary, one section per finding (what happened, the link, why it matters to you, and what the source does *not* prove), and which competitors had nothing new.

Then you review the page, and the findings you keep update `doctrine.md`. The next check uses the updated file. If nobody reviews, the claims go stale and the check slowly turns into a news feed.

```
competitors.md ─┐
positioning.md ─┼─> look ─> check against claims ─> double-check ─> Notion page ─> your review ─> doctrine.md
doctrine.md ────┘                                                                                    │
      ▲──────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## The three files you fill in

Templates are in the `context/` folder. The assistant is only as good as what you put in them.

- **`positioning.md`: your story.** What you sell, who buys it, what they use instead of you, and why they should pick you. You change it rarely, on purpose. The assistant only reads it.
- **`doctrine.md`: your claim checklist.** Each claim you make, marked as still yours, now shared with competitors, or retired, with a date and a link as proof. It also names the one thing a competitor would have to show to beat your main claim. Every finding is checked against this file. It changes after each review.
- **`competitors.md`: who to check.** Each competitor, the products to watch, and where they post news. You change it when a competitor appears, renames itself or stops mattering.

**Why two files for your claims?** `positioning.md` is what you say. `doctrine.md` is whether it's still true. Your positioning might say "we're the only tool that does X." Your doctrine then records "X: Competitor B can say this too since March 4, here's the link. The part still only ours is Y." Keeping them apart means one competitor's launch never quietly rewrites your story. You change your story when you decide to; the checklist changes when the evidence does.

---

## Setup

### 1. Copy this project and fill in the three files
Make your own copy of this project on GitHub (the **Fork** button at the top of this page). Make it private if your files will hold anything you don't want public.

Fill in the three files in `context/`. Replace every `Competitor A`, `Competitor B` and every `<...>`. The original setup checks 25 competitors per run without trouble.

`doctrine.md` is the hardest to write from scratch. A shortcut: fill in `positioning.md` first, then ask Claude to draft `doctrine.md` from it and your website, marking every claim as "Ours" with today's date. Your first few reviews will correct it.

Save your changes to your GitHub copy. The scheduled check reads the files from GitHub, not from your computer.

### 2. Create the Notion database
In Notion, create a full-page database called **Competitor Watch** with these columns. The names must match exactly.

| Column | Type | What goes in it |
|---|---|---|
| Run | Title | `Run 2026-01-06` (the date), or `Failed run 2026-01-06` |
| Run date | Date | The date of the check |
| Vendors with changes | Multi-select | Competitors that had findings (filled in automatically) |
| Findings | Number | How many findings |
| Claims at risk | Text | One short line |
| Status | Select | Three options: `Not reviewed`, `Reviewed`, `Nothing new` |

Then find the database's ID, so the assistant knows where to write. Ask Claude (with Notion connected, see step 3): *"Find my Competitor Watch database in Notion and give me its data source ID."* You get something like `collection://1a2b3c4d-...`. Copy the part after `collection://` and paste it into `prompts/competitor-watch.md` and `prompts/review.md`, wherever you see `<NOTION_DATA_SOURCE_ID>`.

Optional: in `prompts/competitor-watch.md`, replace `<OWNER>` with the name of the person readers should ask about the page.

### 3. Schedule it
Claude can run this on a schedule in Anthropic's cloud, so your computer can be off. Anthropic calls this a **routine** or **scheduled task**.

**You need:**
- A paid Claude plan (Pro, Max, Team or Enterprise). On a company plan, your admin may need to switch it on.
- Your GitHub copy of this project, and Claude allowed to read it. The first time you open [claude.ai/code](https://claude.ai/code), it asks to connect your GitHub account. Say yes.
- Notion connected to Claude: go to [claude.ai/customize/connectors](https://claude.ai/customize/connectors), add Notion and sign in with a Notion account that can edit the Competitor Watch database. (A "connector" is Claude's link to another app, here Notion.)

**Set it up:**
1. Go to [claude.ai/code/routines](https://claude.ai/code/routines) and click **New task** (some accounts show **New routine**).
2. **Name:** `Competitor Watch`.
3. **Prompt** (the instruction it gets each time): paste this one line: `Read prompts/competitor-watch.md in this repo and follow it exactly.` That way, when you improve the instructions file later, the next check uses the new version. If you pasted the whole file here instead, you'd have to paste it again after every change.
4. **Schedule:** choose **Custom** and enter `0 4 * * 2,5`. That means 4:00 every Tuesday and Friday, in UTC (London winter time). To use your own time zone, write it in front, e.g. `CRON_TZ=America/New_York 0 7 * * 2,5` for 7:00 New York time.
5. **Advanced settings > General > Repository:** pick your GitHub copy of this project.
6. **Cloud environment:** leave it on `Default`.
7. **Model:** pick the most capable Claude model in the list. Deciding whether a finding touches your claims is the hard part, and a weaker model lets more noise through.
8. **Connectors tab:** keep Notion (plus Slack or the app you chose for notifications, see [Reviewing a check](#reviewing-a-check)) and remove all the others. Claude adds all your connected apps by default and acts as you in each one, so only leave what it needs.
9. Click **Create**, then **Run now** to test it straight away.

**After each check:** the task's **History** list shows every run. Click one to see step by step what Claude read and did. The Notion page is the result; the history is for when something goes wrong.

**Good to know**
- The instructions tell Claude to only read the web, write to Notion and, if you set it up, send one notification. The setup screen has no switch to enforce that, so removing the other connectors (step 8) is your safety net.
- Each check uses some of your Claude plan's allowance. Two checks a week is light.
- Anthropic still labels the feature a preview, so button names may change. Their guide: [Scheduled tasks](https://code.claude.com/docs/en/routines).

**Prefer to run it by hand?** Open Claude Code in your copy of the project and type *"run prompts/competitor-watch.md"*.

### 4. Check the first page
The first check looks back 90 days, so it takes longest. Open the Notion page and click through three or four findings to their sources before trusting the rest.

---

## Reviewing a check

The review is the part you do yourself, ideally within a few days of each check. It's what keeps your claims current.

**How you know there's something to review.** Nothing alerts you by default. Pick one:
- **A message where you already work (recommended).** The check can post a short note in Slack (or any other messaging app you can connect to Claude, such as Gmail) each time it finds something: how many findings, which competitors, and the link to the page. Connect the app at [claude.ai/customize/connectors](https://claude.ai/customize/connectors), keep it in the routine's Connectors tab, and fill in the `Notify:` line at the end of `prompts/competitor-watch.md`, e.g. `Notify: Slack, channel #competitor-watch`.
- **Notion's own notifications** for new pages in the Competitor Watch database.
- **A Notion view** of the database filtered to Status = `Not reviewed`, which you check after each scheduled day.

**You need, once:**
- Claude Code on your computer ([install guide](https://code.claude.com/docs/en/setup)), signed in with the same Claude account you connected Notion to.
- Your GitHub copy of this project downloaded to your computer. In a terminal: `git clone <your copy's URL>`, then open Claude Code in that folder.

**Each review:**
1. Type *"follow prompts/review.md"*.
2. Claude reads every Notion page marked `Not reviewed` and shows you the findings three or four at a time, each with a suggestion:
   - **keep**: it changes or confirms one of your claims.
   - **fix**: true, but described wrong. Claude says what's wrong, corrects it, then keeps it.
   - **drop**: noise, or the source doesn't back it up.
3. You answer for the whole group, e.g. "keep all" or "keep 1–3, drop 4". You can also ask about any finding before deciding.
4. Claude adds the kept findings to `doctrine.md` with the date and link, and marks the Notion pages `Reviewed`.
5. It lists any claim that changed, so you know what to update on your website, slides or ads.
6. It asks whether to save `doctrine.md` to your GitHub copy. Say yes: the next check reads GitHub, so an unsaved change is invisible to it. Before each review, type *"get the latest version from GitHub"* so you start from the newest files.

**Prefer to review by hand?** Read the Notion page, open `doctrine.md` on GitHub (the pencil icon edits it in the browser), update each claim's status with the date and link, save, and set the page's Status to `Reviewed`. Same result, no Claude Code needed.

---

## Why it's built this way

- **Checked against your claims, not just listed.** A list of competitor features is easy to make and nobody reads it. Every finding has to touch something you say.
- **Three labels for each claim:** still yours, shared with competitors, retired. Enough to act on, few enough to stay consistent.
- **The winning bar, written down.** `doctrine.md` names the one thing a competitor would need to show to beat your main claim. Every page says plainly whether anyone did, so "nothing changed" is a real answer.
- **Every link reopened.** AI assistants can invent releases and quotes that sound right. Rechecking each source and deleting what can't be confirmed is the main protection.
- **"What this doesn't prove" on every finding.** A launch post with no named customer is weaker than it looks. Saying so every time stops the page from overstating things.
- **A failed check says it failed.** A half-finished page that looks complete is worse than none.
- **Counts, not rankings.** "Competitor B showed up in 7 of 8 checks" shows who keeps shipping into your space, without pretending to measure importance.
- **No to-do lists on the page.** The page reports. You decide, in the review.
- **Plain words.** Anyone in the company should be able to read a page without asking what it means.

## When something goes wrong

- **No Notion page appeared.** Open the run in the task's History. Usually: Notion wasn't kept in the Connectors tab, the database ID in the instructions is wrong, or the Notion account can't edit the database.
- **The page landed outside the database, or its columns are empty.** A column name doesn't match the table in step 2 exactly. Fix the name in Notion and click **Run now**.
- **Most competitors show up as "Not covered", or every check is a "Failed run".** Claude couldn't open their websites. My best guess, unconfirmed: check the cloud environment's internet access setting, then click **Run now**.
- **The review can't see Notion.** Run it in Claude Code signed in with the same Claude account you connected Notion to.
- **Findings feel generic or off-topic.** Your claims in `doctrine.md` are too vague to check against. Make your main claim and the winning bar specific.

## Limits

- Pricing is often not public. The page says so rather than guessing.
- It only sees what competitors publish. Private roadmaps, sales talk and unannounced deals stay invisible.
- Some websites block automated visits. Those competitors show up as "Not covered" and are tried again next time.
- It's only as current as `doctrine.md`. Review the pages.

## License

MIT: free to use, copy and change. See `LICENSE`.
