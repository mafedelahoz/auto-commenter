# TripAdvisor Forum Commenter Skill

> TripAdvisor Forum Reply Automation - From Thread Discovery to Posting and Tracking
> Adapted from reddit-commenter. See CONTRIBUTING.md "Adding New Platforms" for the pattern this follows.

---

## ⚠️ Read This Before Running Anything On This Skill

**TripAdvisor is a direct competitor to CuddlyNest.** TripAdvisor sells its
own hotel/stay bookings and takes commission the same way CuddlyNest does.
Their forum moderation is specifically tuned to catch accounts that
repeatedly steer readers toward outside booking sites — this is not a
generic anti-spam filter, it's the exact pattern this skill produces. The
account-ban outcome seen on r/Shoestring after a couple of CuddlyNest
mentions is the **floor**, not the ceiling, of what to expect here; treat
TripAdvisor as higher-risk than any Reddit target and default to *not*
mentioning the product at all unless the user has explicitly confirmed they
want to accept that risk for this platform specifically, separate from the
Reddit decision.

TripAdvisor's actual written forum rules (verify current wording at
https://www.tripadvisor.com/pages/forums_posting_guidelines.html before
running this live) additionally require:
- Disclosing any commercial affiliation with a property/business you mention
- No repeated posting of the same website/business across threads
- No "written for marketing purposes" content

---

## Required Tool: Playwright MCP

Same as reddit-commenter: `browser_navigate`, `browser_snapshot`,
`browser_click`, `browser_type`, `browser_wait_for`. No screenshots, minimize
tokens per call, navigate directly to URLs rather than clicking through.

---

## How TripAdvisor Differs From Reddit

| Reddit | TripAdvisor Forums |
|---|---|
| Subreddit (topic-based) | Forum (destination-based — e.g. "Italy Forums > Rome Forum", "Asia Forums > Thailand Forum > Bangkok Forum") |
| Post + comments | Thread + Replies |
| Anonymous-ish handle culture | Real-name-leaning culture; many long-tenured "Destination Expert" users who answer fast and are protective of thread quality |
| Self-promo generally banned, enforced inconsistently | Self-promo **explicitly and strictly banned**, enforced by both community flagging and TripAdvisor staff — see warning above |
| New/rising sort | Threads sorted by recent activity within each destination forum; no sitewide "new" feed — you must pick a destination forum first |
| subreddits.md quota per subreddit | resources/forums.md quota per **destination forum**, deliberately low |

---

## Execution Workflow

### Step 1: Check Activity Status and Select a Destination Forum

```
1. Check today's date file in tracking/tripadvisor/ folder
   → File name: YYYY-MM-DD.md, create from template.md if missing

2. Check today's reply count by destination forum

3. Select next destination forum from resources/forums.md, prioritizing
   ones with no activity today
```

### Step 2: Find Threads

```
1. Navigate directly to the destination forum
   → browser_navigate("https://www.tripadvisor.com/ShowForum-g{location-id}-i{forum-id}-...")
   → or use TripAdvisor's search for the destination name + "forum"

2. browser_snapshot()

3. Criteria for selecting a thread:
   • A real question with enough detail to answer specifically (dates,
     budget, party size, area of the city) — TripAdvisor's own community
     norms downvote/ignore vague threads harder than Reddit does
   • ⚠️ Thread you haven't replied to today — check tracking log
   • Prefer threads with 0-3 replies over ones a Destination Expert has
     already answered thoroughly
```

### Step 3: Deep Analysis

```
Same discipline as SKILL.md Step 3 for Reddit:
1. Read the full thread, not just the first post
2. Understand what's actually being asked (specific area recommendation?
   itinerary sanity check? price expectation check?)
3. Read existing replies — Destination Experts often give hyper-local,
   highly specific answers; don't repeat what's already been said well
4. Decide if you have a genuinely specific, locally-grounded answer to add.
   If you don't have real knowledge of the specific neighborhood/property
   being asked about, don't fabricate it — general "compare a few
   platforms" advice reads as an obvious non-answer to a locals-only thread
   and draws exactly the scrutiny described in the warning above.
5. Site verification: if a specific property/link is mentioned and
   feedback is requested, visit it before commenting.
```

### Step 4: Write the Reply

```
1. Draft based on Step 3.
2. Given the competitor risk, default posture is: answer with genuine,
   locally-specific travel advice and no product mention. Only include a
   CuddlyNest mention if the user has separately confirmed they accept the
   elevated risk for TripAdvisor specifically (see warning above) — do not
   assume the Reddit-flow "organic mention OK" decision carries over here.
```

### Step 5: Personalization Review

```
See resources/personalization_tripadvisor.md — tone here skews slightly
more formal/complete-sentence than Reddit or Quora; TripAdvisor's forum
culture is older-skewing and less tolerant of internet-casual shorthand.
```

### Step 6: Post the Reply

```
1. browser_snapshot() to find the reply box at the bottom of the thread
2. browser_click into it, browser_type the reviewed reply
3. Submit
4. Secure the reply's permalink (TripAdvisor threads have stable anchor URLs per post)
```

### Step 7: Judge Potential Customer (Optional)

```
Same criteria as leads/reddit.md, logged to leads/tripadvisor.md.
```

### Step 8: Update Tracking

```
Update tracking/tripadvisor/[today's-date].md the same way as the Reddit
skill — activity table, activity log entry, leads file if applicable.
```

---

## Cautions

1. **Read the top warning again before the first real run.** This is the
   one platform in this repo where the default should be "don't mention the
   product" rather than "mention it when organic."
2. **Login required**, verify session active before starting.
3. **Never fabricate local specificity** (a neighborhood, a property name)
   you don't actually know — TripAdvisor forum regulars catch this fast and
   call it out publicly, which is worse for the account than staying silent.
4. **Disclose, don't dodge**: if you ever do mention CuddlyNest, do it
   exactly as a personal-experience mention (see personalization file),
   never phrased to imply neutrality you don't have.
5. **Site verification required** whenever a specific property/listing is
   linked and a review is requested.
