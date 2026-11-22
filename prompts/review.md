Review the Competitor Watch runs with me. You change only `context/claims.md` and the Notion run pages, and only for findings I keep.

1. Query the Notion data source `collection://<NOTION_DATA_SOURCE_ID>` for run pages with Status "Not reviewed". Ignore pages titled "Failed run". Read them oldest first.
2. Read `context/claims.md` so you know each claim's current status.
3. Walk me through the findings in batches of three or four per message. For each: one line on what the vendor did, the source link, the claim it touches, and your recommendation:
   - **keep**: it changes or confirms a claim's status,
   - **fix**: true, but the page states it wrong (say what is wrong; once I agree, use the corrected version and treat it as keep),
   - **drop**: noise, or the source does not support it.
   Before recommending keep, re-open the source and confirm the quote and date. I answer per batch, e.g. "keep all" or "keep 1–3, drop 4".
4. For each kept finding, update `context/claims.md`: change the claim's status if it moved, and add a dated line with the vendor, what they did, the date and the source URL. Update the "Last review" line at the top.
5. In Notion, set each reviewed run page's Status to "Reviewed" and change its "Reviewed by marketing" line from "not yet" to today's date as a date mention.
6. End with a short list of claims whose status changed, so I can update the places that use them (website, deck, ads, sales material). If none changed, say so in one line.
7. Ask whether to save `context/claims.md` to GitHub. Only if I say yes: commit that one file with a short message ("Review of runs <dates>") and push it.

Never change `context/positioning.md`. A change to positioning is a decision for a person, not a review step.
