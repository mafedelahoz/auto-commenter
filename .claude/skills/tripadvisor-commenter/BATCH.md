# TripAdvisor Forum Commenter - Batch Mode

> Executed when "Fill today's TripAdvisor quota" or "Run TripAdvisor batch" command given
> See SKILL.md for the single-reply workflow and the competitor-risk warning at the top of it — read that before running this.

---

## Batch Execution Trigger

- "Fill today's TripAdvisor quota"
- "Run TripAdvisor batch mode"

---

## Pre-Start Check

```
1. Check tracking/tripadvisor/today's-date.md (create from template.md if missing)
2. Check current reply count by destination forum
3. Calculate remaining quota:
   - Total daily target: 5 replies across the forums in resources/forums.md
     (lowest of the three platforms — highest moderation risk, smallest
     realistic daily footprint before it looks patterned)
```

---

## Batch Workflow

```
[Start]
    ↓
[1] Check tracking file → calculate remaining quota
    ↓
[2] Select destination forum under quota (priority: no activity today)
    ↓
[3] Start reply-writing loop for that forum
    ↓
    [3-1] Execute SKILL.md Step 1-8 (write single reply)
    ↓
    [3-2] Update tracking file
    ↓
    [3-3] Report progress
    ↓
    [3-4] Check that forum's quota
          - Under target → back to [3-1]
          - Target reached or no suitable threads → [4]
    ↓
[4] Move to next forum (wait 20-30 minutes — longest wait of the three
    platforms, matching the highest moderation risk)
    ↓
[5] Check overall termination condition
    ↓
    YES → [End]
    NO  → Return to [2]
```

---

## Wait Time Rules

| Situation | Wait Time |
|-----------|-----------|
| Between replies in same forum | 15-20 min |
| Between forum transitions | 20-30 min |
| Can't find suitable thread | Move to next forum (wait 20-30 min) |

---

## Termination Conditions

1. **Quota complete**: daily target replies reached
2. **No threads**: no suitable specific threads across all forums
3. **User interruption**
4. **Error occurred**: 3 consecutive failures
5. **Any moderation signal** (reply removed, warning message, "flagged for
   review" banner) → **stop the entire batch immediately**, do not continue
   to other forums, report to the user before doing anything else. Do not
   treat this the way the Reddit skill treats a single-subreddit ban —
   given TripAdvisor's stricter, more centralized moderation, one flag here
   is a stronger signal about the account overall than a single subreddit
   ban was on Reddit.

---

## Skip Conditions

Skip a thread when:
- You don't have genuine, specific local knowledge to add (see SKILL.md
  Step 3) — don't fill the gap with generic platform-comparison advice
- The thread is already well-served by a Destination Expert's answer
- The thread involves a dispute with a specific named property/business —
  don't wade into it

---

## Error Handling

| Error | Response |
|-------|----------|
| Page loading failure | Wait 30s then retry (max 3 times) |
| Reply submission failure | Move to next thread |
| Login session expired | Stop batch, notify user |
| Reply removed / flagged / account warning | **Stop everything, notify user immediately** — see Termination Condition 5 |

---

## Batch Completion Report

```
---
## TripAdvisor Batch Completion Report

**Total Written**: X/5
**Time Spent**: Xh XXm
**Product mentions included**: X (should be 0 unless user explicitly
  accepted the elevated risk for this platform, per SKILL.md warning)

### Results by Forum
| Destination Forum | Written | Skip Reason |
|-----------|---------|-------------|
| ... | ... | ... |

### Potential Customers Discovered
- N (updated in leads/tripadvisor.md)

### Special Notes
- ...
---
```

> Single reply workflow (Step 1-8): See SKILL.md
> Personalization review: See resources/personalization_tripadvisor.md
