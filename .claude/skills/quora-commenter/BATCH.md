# Quora Commenter - Batch Mode

> Executed when "Fill today's Quora quota" or "Run Quora batch" command given
> See SKILL.md for the single-answer workflow

---

## Batch Execution Trigger

- "Fill today's Quora quota"
- "Run Quora batch mode"

---

## Pre-Start Check

```
1. Check tracking/quora/today's-date.md (create from template.md if missing)
2. Check current answer count by Space/topic
3. Calculate remaining quota:
   - Total daily target: 8 answers across the topics in resources/spaces.md
     (lower than Reddit's 24 — Quora rewards depth over volume, and rapid
     posting reads as spammy faster here)
   - Subtract today's written answers
```

---

## Batch Workflow

```
[Start]
    ↓
[1] Check tracking file → calculate remaining quota
    ↓
[2] Select topic/Space under quota (priority: no activity today > oldest
    last activity > available quota)
    ↓
[3] Start answer-writing loop for that topic
    ↓
    [3-1] Execute SKILL.md Step 1-8 (write single answer)
    ↓
    [3-2] Update tracking file
    ↓
    [3-3] Report progress
    ↓
    [3-4] Check that topic's quota
          - Under target → back to [3-1]
          - Target reached or no suitable questions → [4]
    ↓
[4] Move to next topic (wait 15-20 minutes — longer than Reddit's 5-15,
    Quora's velocity detection is stricter)
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
| Between answers on different questions, same topic | 10-15 min |
| Between topic transitions | 15-20 min |
| Can't find suitable question | Move to next topic (wait 15-20 min) |

---

## Termination Conditions

1. **Quota complete**: daily target answers reached
2. **No questions**: no suitable unanswered/thin questions across all topics
3. **User interruption**
4. **Error occurred**: 3 consecutive failures

---

## Skip Conditions

Skip a question when:
- It's already exhaustively answered by 10+ substantive answers covering the
  obvious angles, and you have no new angle
- It's a debate/opinion-bait question with no genuine informational content
  ("Is country X better than country Y", asked with no criteria)
- It requires medical, legal, or immigration-status-specific advice beyond
  general knowledge — redirect only if genuinely helpful, don't fabricate
  expertise
- Answering would require claiming a personal experience/identity not known
  to be true for the account owner (see SKILL.md Caution #4)

Report when skipping:
```
{Question title} skipped - {reason}
→ Moving to next question in {topic}
```

---

## Error Handling

| Error | Response |
|-------|----------|
| Page loading failure | Wait 30s then retry (max 3 times) |
| Answer submission failure | Move to next question |
| Login session expired | Stop batch, notify user |
| "Needs Improvement" flag or answer collapsed shortly after posting | Stop batch, notify user — this signals the account is being flagged, don't keep posting through it |

---

## Batch Completion Report

```
---
## Quora Batch Completion Report

**Total Written**: X/8
**Time Spent**: Xh XXm

### Results by Topic
| Topic/Space | Written | Skip Reason |
|-----------|---------|-------------|
| ... | ... | ... |

### Potential Customers Discovered
- N (updated in leads/quora.md)

### Special Notes
- ...
---
```

> Single answer workflow (Step 1-8): See SKILL.md
> Personalization review: See resources/personalization_quora.md
