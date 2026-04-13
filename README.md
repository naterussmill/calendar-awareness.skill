# calendar-awareness

A Claude skill for accurate temporal reasoning. Eliminates common date and scheduling failure modes by giving Claude structured rules, explicit algorithms, and static reference tables — rather than relying on training intuition.

---

## What it fixes

Claude's out-of-the-box temporal reasoning has several known weak spots:

- **"Next [weekday]" ambiguity** — resolved inconsistently; sometimes means the coming occurrence, sometimes the one after
- **Business day counts** — typically ignores federal holidays and counts calendar days instead
- **Holiday dates** — recalled from training rather than looked up, leading to errors on floating holidays and observed dates
- **Time zone conversion** — gets DST transitions wrong, especially near switchover dates
- **Fiscal quarter assumptions** — silently defaults to calendar year even in contexts where that's wrong
- **Recurrence patterns** — "3rd Thursday of the month" and similar expressions computed incorrectly at month boundaries
- **Date format drift** — inconsistent output formats within a single response

This skill addresses all of the above.

---

## Features

| Capability | Detail |
|---|---|
| **Anchor date extraction** | Reads today's date from the system prompt; never guesses |
| **Relative date resolution** | "yesterday", "next Thursday", "in 3 weeks", "last month" → concrete date + day of week |
| **Partial date inference** | "May 15th" resolved to correct year based on context |
| **Calendar intervals** | Days, weeks, calendar months with transparent arithmetic |
| **Business day intervals** | Skips weekends + federal observed holidays; flags non-federal holidays |
| **Recurrence patterns** | Nth weekday of month, last weekday of month, every-X-weeks series |
| **Federal holidays** | Actual and observed dates for 2025–2027; floating holiday formulas for beyond |
| **Time zones** | US zone table with DST switchover dates 2025–2027; defaults to Central Time |
| **Fiscal quarters** | Calendar year default; prompts to confirm in business contexts; knows MN state and US federal FY starts |
| **Timeline sequencing** | Orders multiple events chronologically; flags past deadlines, date mismatches, weekend/holiday landings |
| **Date format** | Consistent `Mon 13 Apr 2026` output throughout |

---

## File structure

```
calendar-awareness/
├── SKILL.md                  # Core skill — always loaded when triggered
└── references/
    └── holidays.md           # Loaded on demand for business days, holidays, time zones, fiscal quarters
```

The two-file architecture keeps simple date questions fast (SKILL.md only) while pulling in the reference tables when accuracy demands it.

---

## Installation

1. Download `calendar-awareness.skill`
2. In Claude.ai, go to **Settings → Skills**
3. Upload the `.skill` file

---

## Example behavior

**Business days**
> "How many business days until May 15th?"
> → Counts calendar days, subtracts weekends, checks holiday table. Returns: **24 business days** (Mon 13 Apr → Fri 15 May 2026)

**Recurrence**
> "When is the 3rd Thursday of May?"
> → Computes from first-of-month weekday using explicit algorithm. Returns: **Thu 21 May 2026**

**Ambiguity handling**
> "My deadline is next Thursday."
> → Returns the date, states the interpretation, invites correction: *"I'm reading that as Thu 23 Apr — let me know if you meant Thu 16 Apr."*

**Holiday awareness**
> "The contract closes in 10 business days."
> → Checks the holiday table for the window, not training memory. Flags if a holiday falls within the range.

**Fiscal year**
> "I need to hit this by end of Q3."
> → Asks: *"Are you working on a calendar fiscal year, or does your organization run a different cycle?"* before computing the date.

**Time zone**
> "The call is at 3pm ET."
> → User is CT. Returns: **2pm CT.** Checks DST table if near a transition date.

---

## Reference data coverage

`references/holidays.md` contains:

- US federal holidays (actual + observed) for 2025, 2026, 2027
- Floating holiday formulas (MLK Day, Memorial Day, Labor Day, Thanksgiving, etc.)
- Non-federal holidays to flag (day after Thanksgiving, Christmas Eve, etc.)
- US time zone table (ET/CT/MT/PT/AZ) with UTC offsets
- DST switchover dates for 2025–2027
- Fiscal year reference grid (calendar, UK, US federal, MN state)

---

## Built with

[Claude Skill Creator](https://claude.ai) — the skill-creator skill for Claude.ai
