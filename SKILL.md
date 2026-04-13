---
name: calendar-awareness
description: >
  Use this skill whenever a conversation involves dates, times, scheduling, deadlines, or timeline reasoning.
  Triggers include: references to "today", "yesterday", "tomorrow", "next week", "last month", any named day
  of the week, any calendar date (past or future), duration calculations ("how many days until X"),
  business days, holidays, time zones, fiscal quarters, recurrence patterns ("third Thursday of the month",
  "every other Monday"), sequencing of events ("before the meeting", "after the deadline"), or any question
  where knowing the current date or day-of-week is relevant to giving a correct answer. Also use when the
  user references a date without specifying the year and the correct year must be inferred. Always consult
  this skill before answering date-related questions — do not rely on intuition or training knowledge for
  current date, holidays, or time zone offsets.
---

# Calendar Awareness Skill

## Purpose

Help Claude reason accurately about:
- What today's date is (including day of week)
- What a referenced past or future date resolves to (including day of week)
- How many calendar days, business days, weeks, or months are between two dates
- Recurrence patterns ("3rd Thursday of the month", "every other Monday")
- US federal holidays — actual and observed dates
- Time zone conversions (default: Central Time)
- Fiscal quarter boundaries (calendar vs. non-calendar fiscal years)
- How events relate to each other on a timeline

**Reference file:** Load `references/holidays.md` whenever the calculation involves business days, holidays, time zones, or fiscal quarters. Do not rely on memory for these — use the tables.

---

## Step 1: Establish the Anchor Date

The system prompt injects today's date in a line like:

> `The current date is Monday, April 13, 2026.`

**Always extract this date first** before doing any date reasoning. Parse it as:
- Day of week: Monday
- Full date: April 13, 2026
- ISO: 2026-04-13

If no date is injected, acknowledge uncertainty but still reason from the most contextually plausible date rather than refusing to engage.

---

## Step 2: Resolve Referenced Dates

When the user mentions a date or time expression, resolve it to a concrete date + day of week.

### Relative expressions

| Expression | Resolution rule |
|---|---|
| "today" | Anchor date |
| "yesterday" | Anchor − 1 day |
| "tomorrow" | Anchor + 1 day |
| "this [weekday]" | The named weekday within the current week (Mon–Sun) |
| "next [weekday]" | The named weekday in the *following* week |
| "last [weekday]" | The named weekday in the *preceding* week |
| "this week" | The Mon–Sun week containing today |
| "next week" | The Mon–Sun week starting 7 days from this Monday |
| "in X days/weeks/months" | Anchor + X units |
| "X days/weeks/months ago" | Anchor − X units |

**Ambiguity flag:** "next [weekday]" is genuinely contested — some people mean the coming occurrence, others mean the one after. When the difference matters (e.g., a deadline), state your interpretation and invite correction: *"I'm reading 'next Thursday' as Thu 23 Apr — let me know if you meant Thu 16 Apr."*

### Partial dates (month + day, no year)

- If the month/day has not yet occurred this year → use the current year
- If the month/day has already passed this year → use the next year (future/deadline contexts) or current year (past-event contexts)
- When ambiguous, state your assumption explicitly

### Always state the resolved day of week

Whenever you reference a specific date in your answer, include the day of week.

**Preferred date format:** `Mon 13 Apr 2026` (with day of week) or `13 Apr 2026` (without)
- ✅ "Mon 20 Apr 2026"
- ✅ "20 Apr 2026"
- ❌ "April 20, 2026" or "April 20th"

---

## Step 3: Calculate Intervals

### Calendar days
1. Resolve both dates to concrete ISO dates
2. Compute the difference in the requested unit
3. Clarify inclusive vs. exclusive if it matters

**Show your work briefly** for multi-step calculations.

For month intervals: count calendar months, not 30-day blocks. Note when the day-of-month shifts (e.g., Jan 31 + 1 month = Feb 28).

### Business days
→ **Load `references/holidays.md` before computing.**

1. Count calendar days between dates
2. Subtract weekends (Sat/Sun)
3. Subtract any federal observed holidays that fall within the range (use the table — do not recall from memory)
4. If context suggests non-federal holidays may apply (company, state), flag it and ask

Example: "10 business days from Mon 13 Apr 2026"
- 10 business days forward, skipping weekends
- Check holidays table: no federal holidays in this window
- Result: Fri 24 Apr 2026

---

## Step 4: Recurrence Patterns

### Nth weekday of the month

Algorithm:
1. Find the weekday of the 1st of the target month (use day-of-week math)
2. Compute offset to target weekday: `(target_weekday - first_weekday) mod 7`
3. First occurrence = 1st + offset days
4. Nth occurrence = first occurrence + (N−1) × 7 days
5. For "last [weekday]": find first occurrence, add weeks until the next would exceed month end

Example: "3rd Thursday of May 2026"
- 1 May 2026: anchor Mon 13 Apr + 18 days = Fri. 1 May is Friday (Fri=4)
- Thu=3. Offset = (3−4) mod 7 = 6. First Thursday = 1 May + 6 = 7 May
- 3rd Thursday = 7 May + 14 = **Thu 21 May 2026** ✅

### Every X weeks from a start date
- Next occurrence = start + (n × X × 7) for n = 1, 2, 3…
- State the next 2–3 occurrences when listing a recurrence series

---

## Step 5: Time Zones

→ **Load `references/holidays.md` for the full zone/DST table.**

**Default assumption:** User is in Central Time (CT). Apply this unless another zone is stated or implied.

Key offsets relative to CT:
- ET = CT + 1 hour
- MT = CT − 1 hour
- PT = CT − 2 hours

**When to flag a time zone issue:**
- A stated time feels implausibly early or late for the context
- The other party appears to be in a different region
- The date falls near a DST transition (check the switchover dates in the reference file)
- Scheduling involves international parties (EU clocks change on a different date than US)

Always confirm zone when scheduling across regions.

---

## Step 6: Fiscal Quarters

→ **Load `references/holidays.md` for FY reference table.**

**Default:** Calendar year (Q1 = Jan–Mar, Q2 = Apr–Jun, Q3 = Jul–Sep, Q4 = Oct–Dec).

**Always ask** when quarter language appears in a business or client context — do not silently assume calendar year. Prompt: *"Are you working on a calendar fiscal year, or does your organization run a different cycle?"*

Known non-calendar defaults:
- Minnesota state government: FY starts 1 Jul
- US federal government: FY starts 1 Oct

---

## Step 7: Timeline Reasoning

When a conversation involves a sequence of events, deadlines, or milestones:

1. Identify all referenced dates/times and resolve each one
2. Order them chronologically
3. Flag conflicts, implausibly tight gaps, or likely misremembers
4. Relate each event to today: past / present / future, and how far away

### Red flags to surface
- Deadline is already past
- Two events land on the same day when the user seems to think they're separate
- "Next week" and a specific date don't actually align
- A referenced weekday doesn't match the stated date (e.g., "Monday the 15th" but the 15th is Tuesday)
- A deadline falls on a weekend or holiday — flag and ask if the intent is the preceding Friday or following Monday

---

## Step 8: Communication Standards

- Always lead with the resolved concrete date
- Include the day of week in every date reference
- Use format `Mon 13 Apr 2026`
- Be explicit about assumptions (year inference, fiscal year, time zone, "next" weekday interpretation)
- Keep arithmetic transparent — show the count, don't just assert it
- Gently correct date errors before answering
- When flagging an ambiguity, state your interpretation and invite correction rather than asking a blocking question
- **Verify internally before writing.** Complete all date checks and resolve any potential flags before composing the response. Never narrate the verification process or include self-corrections in the output — only surface an issue if it remains unresolved after checking. Present the clean answer, not the scratch work.

---

## Quick Reference: Day-of-Week Math

1. Count calendar days from the anchor date
2. Apply modulo 7 to find offset
3. Map: Mon=0, Tue=1, Wed=2, Thu=3, Fri=4, Sat=5, Sun=6

Example: Anchor is Mon 13 Apr 2026. What day is 25 Apr?
- 25 − 13 = 12 days → 12 mod 7 = 5 → Mon + 5 = **Sat 25 Apr 2026** ✅

---

## Examples

**Q: When is next Friday?**
Anchor: Mon 13 Apr 2026
→ This Friday = Fri 17 Apr (4 days away)
→ *Next* Friday = Fri 24 Apr 2026 (11 days away)
→ Note: stating interpretation in case user meant Fri 17 Apr

**Q: How many business days until May 15th?**
Anchor: Mon 13 Apr 2026. Target: Fri 15 May 2026.
→ Calendar days: 32. Weekends in window: 8 days (4 weekends).
→ Check holidays.md: no federal holidays Mon 13 Apr – Fri 15 May.
→ **Business days: 32 − 8 = 24 business days**

**Q: When is the 2nd Tuesday of June?**
→ 1 Jun 2026: Mon 13 Apr + 49 days. 49 mod 7 = 0 → Monday. 1 Jun is Mon.
→ Offset to Tue: (1−0) mod 7 = 1. First Tue = 2 Jun.
→ 2nd Tuesday = 2 Jun + 7 = **Tue 9 Jun 2026**

**Q: The call is at 3pm ET — what time is that for me?**
→ User is CT. ET = CT+1. 3pm ET = **2pm CT**
→ Check: is this near a DST transition? (load holidays.md if uncertain)

**Q: I need to submit by end of Q2.**
→ Clarify: calendar year or fiscal year? Assuming calendar:
→ Q2 ends **Tue 30 Jun 2026** — 78 days from today (Mon 13 Apr), ~11 weeks

**Q: My deadline is Monday the 20th.**
→ Mon 20 Apr 2026 is indeed a Monday ✅ — no correction needed.
→ That is 7 days from today.
