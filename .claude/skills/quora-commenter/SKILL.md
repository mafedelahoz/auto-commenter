# Quora Commenter Skill

> Quora Answer Automation - From Question Discovery to Posting and Tracking
> Adapted from reddit-commenter. See CONTRIBUTING.md "Adding New Platforms" for the pattern this follows.

---

## Required Tool: Playwright MCP

This skill uses **Playwright MCP** to interact with Quora.

### Main MCP Tools
| MCP Tool | Purpose |
|----------|---------|
| `browser_navigate` | Navigate to Quora pages/search |
| `browser_snapshot` | Capture page structure (accessibility tree) |
| `browser_click` | Click elements (answer box, Submit, etc.) |
| `browser_type` | Input text (answer content) |
| `browser_wait_for` | Wait for page loading |

### ⚠️ Important Notes When Using Playwright MCP
- **Minimize tokens**: summarize only the essential info needed per action.
- **Direct navigation**: use `browser_navigate` with a direct URL/search query rather than clicking through Quora's feed when possible.
- **No screenshots**: use only `browser_snapshot`, never `browser_take_screenshot`.

---

## How Quora Differs From Reddit (read before running batch)

| Reddit | Quora |
|---|---|
| Subreddit (community) | Space (topic) — looser, a question can belong to zero or many Spaces |
| Post + comments | Question + Answers (you write an **Answer**, not a comment) |
| Short comments are normal | Quora's own algorithm and readers favor **substantive, multi-paragraph answers** — a one-liner reads as low-effort and gets collapsed |
| Sort: new/rising/hot | Sort a question's feed by "Recommended" or browse a Space's "Unanswered" tab; search Quora directly for open questions |
| Karma / upvotes | Upvotes + "Most Viewed Writer" type badges; Quora also lets readers request you answer a question ("Answer request") |
| Daily per-subreddit quota (subreddits.md) | No community-owned quota — pace by **overall daily answer count** instead, see `resources/spaces.md` |
| Self-promo generally banned outright | Quora explicitly **permits** recommending a product/service when it's relevant and clearly your own experience — but answers that read as an ad get "Needs Improvement" collapsed or removed. The bar is answer quality, not the mention itself. |

**Net effect on the workflow below**: Steps 1-8 mirror SKILL.md for Reddit, but Step 4 (write) and Step 5 (review) are tuned for longer-form answers, and there is no subreddit-style hard rule-check step — instead check the target Space's "Space guidelines" (few Spaces have any) and Quora's sitewide [BNBR policy](https://www.quora.com/about/be-nice-be-respectful) once, not per-question.

---

## Execution Workflow

### Step 1: Check Activity Status and Select a Question

```
1. Check today's date file in tracking/quora/ folder
   → File name: YYYY-MM-DD.md, create from template.md if missing

2. Check today's answer count and topic spread:
   - How many answers written today, across which Spaces/topics
   - Check last answer time (minimum 10-15 minute intervals — Quora's spam
     detection is sensitive to rapid-fire posting more than Reddit's is)

3. Select next question:
   - Prioritize Spaces/topics with no activity today (see resources/spaces.md)
   - Prioritize genuinely unanswered or thinly-answered questions over
     already-saturated ones (Quora surfaces "Answer" prominently even on
     heavily-answered questions, but a fresh unanswered one is a better target)
```

### Step 2: Find Questions

```
1. Search directly instead of browsing the generic feed:
   → browser_navigate("https://www.quora.com/search?q={topic keywords}&type=question")
   → or browser_navigate a specific Space's URL, e.g. https://www.quora.com/topic/{Space-name}
     then check its question list

2. Page snapshot
   → browser_snapshot()

3. Criteria for selecting a question to answer:
   • You can share genuine, specific insight — not just restate the question
   • ⚠️ CRITICAL: Question you haven't answered today
     - Check tracking/quora/today's-date.md activity log for question URLs
     - Verify the selected question URL is NOT in that list
   • Relevance to "Good Topics to Answer" in resources/spaces.md
   • Prefer questions with few or no existing quality answers — Quora ranks
     answer order partly by recency+quality, so a fresh good answer on a
     lightly-answered question gets seen; adding a 15th answer to an
     already-exhaustive thread adds little value

4. Secure the question URL
```

### Step 3: Deep Analysis of the Question and Existing Answers

```
⚠️ CRITICAL: Must perform this step before writing an answer

0. Navigate directly to the question
   → browser_navigate(question URL)
   → browser_snapshot()

1. Read the question precisely, including its "question details" box if present
   (Quora questions often have 1-3 extra sentences of context below the title —
   don't answer off the title alone)

2. Understand the asker's actual intent:
   - Seeking a specific recommendation/decision? (e.g. "Which app for X?")
   - Seeking a how-to / process?
   - Seeking opinion/debate? (e.g. "Is X worth it?")
   - Information-sharing / trivia-style question?

3. Read existing top answers (Quora sorts by relevance, not always chronological):
   - What's already been said well — don't repeat it
   - What angle or gap is missing
   - What tone dominates (personal-story-led vs. listicle vs. terse fact)

4. Decide answer angle:
   - Is this question worth answering? (see skip conditions in BATCH.md)
   - What's the one genuinely useful thing you can add?

5. ⚠️ CRITICAL: Site verification — if the question links to a product/site and
   asks for a review/opinion on it specifically, visit the site with
   browser_navigate and browser_snapshot before writing anything about it.
   Never describe a site you haven't actually opened.

⚠️ Proceed to Step 4 only after clearly identifying the gap you're filling
```

### Step 4: Write the Answer

```
1. Draft based on Step 3:
   - Lead with the actual answer, not a restated question or "Great question!"
   - Quora rewards depth: aim for 2-5 short paragraphs on questions that
     merit it (how-to, comparison, planning), 1 paragraph is fine for
     simple factual or personal-experience questions
   - Structure: a clear direct answer first, then the reasoning/context —
     never bury the answer at the end
   - One genuinely useful point done well beats five shallow ones
```

### Step 5: Personalization Review (Loop)

```
1. Check resources/personalization_quora.md
   → Run the Quora-specific checklist (adapted from Reddit's 16-point list,
     see that file — the biggest deltas are length/depth and no em-dash-heavy
     "answer voice" Quora's own power users are known for)

2. Check style patterns (see resources/personalization_quora.md)

3. Review process:
   • All items PASS → Step 6
   • Any violation → revise, re-review from Step 5
```

**Detailed personalization guide**: See `resources/personalization_quora.md`

### Step 6: Post the Answer

```
1. Click "Answer" on the question
   → browser_snapshot() to find the "Answer" button/composer
   → browser_click(Answer button)

2. Input answer content
   → browser_type(reviewed answer)

3. Submit
   → browser_click(Submit/Post button)

4. Secure the answer's permalink
```

### Step 7: Judge Potential Customer (Optional)

```
⚠️ CRITICAL: Judge accurately by referring to Step 3 analysis again

→ Refer to "Lead Selection Criteria" in leads/quora.md
→ Classify as lead only askers with an actual planned trip/need
  (not a hypothetical "what's the best country to visit" question)

When lead discovered, update leads/quora.md with the same fields used for
Reddit leads (username if visible — Quora allows anonymous askers, note that).
```

### Step 8: Update Tracking

```
Update tracking/quora/[today's-date].md:

1. Activity table: increment today's answer count, note the Space/topic,
   update last-answer time

2. Activity log entry:
   ### [HH:MM] {Space/topic}
   - **Question**: [Title](URL)
   - **Topic Summary**: one-line summary
   - **Answer Link**: [Answer URL]
   - **Answer Content**: full text written

3. Update leads/quora.md if a lead was found
```

---

## File Reference Rules (Token Savings)

| File | Reference Timing |
|------|------------------|
| `resources/spaces.md` | Step 1 (topic/Space selection) |
| `resources/personalization_quora.md` | Step 5 (review) |
| `resources/product.md` | Step 7 (lead judgment) |
| `leads/quora.md` | Step 7 (lead criteria check) |

---

## Cautions

1. **Login required**: verify Quora session is active before starting.
2. **Pace, don't quota**: no per-topic hard cap like subreddits, but keep
   10-15 min between answers minimum — Quora's spam system watches posting
   velocity closely.
3. **Answer quality over answer count**: a short, generic answer does more
   reputational damage on Quora than skipping the question entirely.
4. **Never fabricate a claimed identity or experience** you don't know is
   true for the account owner (same rule as Reddit — see feedback memory on
   false-experience claims).
5. **Mention the product only when it's the actual answer to what's asked**,
   never appended as an afterthought — see `resources/personalization_quora.md`
   "Natural Promotion" section.
6. **Site verification required** whenever a question links to a specific
   product/site and asks for a review of it.
