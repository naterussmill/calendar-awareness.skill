# Holiday & Business Day Reference

## US Federal Holidays — Actual and Observed Dates

### 2025
| Holiday | Actual | Observed | Day |
|---|---|---|---|
| New Year's Day | 1 Jan 2025 | 1 Jan 2025 | Wed |
| MLK Day | 20 Jan 2025 | 20 Jan 2025 | Mon |
| Presidents' Day | 17 Feb 2025 | 17 Feb 2025 | Mon |
| Memorial Day | 26 May 2025 | 26 May 2025 | Mon |
| Juneteenth | 19 Jun 2025 | 19 Jun 2025 | Thu |
| Independence Day | 4 Jul 2025 | 4 Jul 2025 | Fri |
| Labor Day | 1 Sep 2025 | 1 Sep 2025 | Mon |
| Columbus Day | 13 Oct 2025 | 13 Oct 2025 | Mon |
| Veterans Day | 11 Nov 2025 | 11 Nov 2025 | Tue |
| Thanksgiving | 27 Nov 2025 | 27 Nov 2025 | Thu |
| Christmas | 25 Dec 2025 | 25 Dec 2025 | Thu |

### 2026
| Holiday | Actual | Observed | Day |
|---|---|---|---|
| New Year's Day | 1 Jan 2026 | 1 Jan 2026 | Thu |
| MLK Day | 19 Jan 2026 | 19 Jan 2026 | Mon |
| Presidents' Day | 16 Feb 2026 | 16 Feb 2026 | Mon |
| Memorial Day | 25 May 2026 | 25 May 2026 | Mon |
| Juneteenth | 19 Jun 2026 | 19 Jun 2026 | Fri |
| Independence Day | 4 Jul 2026 | 6 Jul 2026 | Sat → observed Mon |
| Labor Day | 7 Sep 2026 | 7 Sep 2026 | Mon |
| Columbus Day | 12 Oct 2026 | 12 Oct 2026 | Mon |
| Veterans Day | 11 Nov 2026 | 11 Nov 2026 | Wed |
| Thanksgiving | 26 Nov 2026 | 26 Nov 2026 | Thu |
| Christmas | 25 Dec 2026 | 25 Dec 2026 | Fri |

### 2027
| Holiday | Actual | Observed | Day |
|---|---|---|---|
| New Year's Day | 1 Jan 2027 | 1 Jan 2027 | Fri |
| MLK Day | 18 Jan 2027 | 18 Jan 2027 | Mon |
| Presidents' Day | 15 Feb 2027 | 15 Feb 2027 | Mon |
| Memorial Day | 31 May 2027 | 31 May 2027 | Mon |
| Juneteenth | 19 Jun 2027 | 18 Jun 2027 | Sat → observed Fri |
| Independence Day | 4 Jul 2027 | 5 Jul 2027 | Sun → observed Mon |
| Labor Day | 6 Sep 2027 | 6 Sep 2027 | Mon |
| Columbus Day | 11 Oct 2027 | 11 Oct 2027 | Mon |
| Veterans Day | 11 Nov 2027 | 11 Nov 2027 | Thu |
| Thanksgiving | 25 Nov 2027 | 25 Nov 2027 | Thu |
| Christmas | 25 Dec 2027 | 25 Dec 2027 | Sat → observed Fri |

---

## Floating Holiday Formulas

Use these to compute holidays beyond the table above:

- **MLK Day**: 3rd Monday of January
- **Presidents' Day**: 3rd Monday of February
- **Memorial Day**: Last Monday of May
- **Labor Day**: 1st Monday of September
- **Columbus Day**: 2nd Monday of October
- **Thanksgiving**: 4th Thursday of November

**Nth weekday of month algorithm:**
1. Find the weekday of the 1st of the month (use day-of-week math from anchor)
2. Compute offset to target weekday: `(target - first_weekday) mod 7`
3. Add `(N-1) × 7` days
4. For "last Monday of May": find 1st Monday, keep adding 7 until next would exceed month end

**Observed date rule (federal):**
- Holiday falls on Saturday → observed the preceding Friday
- Holiday falls on Sunday → observed the following Monday

---

## Non-Federal Holidays to Flag

These are not federal holidays but commonly affect business operations. When business-day calculations touch these dates, flag them and ask the user whether they apply:

- **Day after Thanksgiving** (Black Friday): many private employers observe
- **Christmas Eve** (24 Dec): partial or full closure common
- **New Year's Eve** (31 Dec): partial or full closure common
- **State holidays**: Minnesota observes no additional days beyond federal, but clients in other states may
- **Company-specific holidays**: always ask in business contexts

---

## US Time Zones — Quick Reference

Default assumption: **Central Time (CT)**

| Zone | Abbrev (Standard) | Abbrev (DST) | Offset (Standard) | Offset (DST) |
|---|---|---|---|---|
| Eastern | EST | EDT | UTC−5 | UTC−4 |
| Central | CST | CDT | UTC−6 | UTC−5 |
| Mountain | MST | MDT | UTC−7 | UTC−6 |
| Pacific | PST | PDT | UTC−8 | UTC−7 |
| Arizona | MST | MST | UTC−7 | UTC−7 (no DST) |

**Relative to CT:**
- ET is CT+1
- MT is CT−1
- PT is CT−2

### DST Switchover Dates

| Year | Spring Forward (2am → 3am) | Fall Back (2am → 1am) |
|---|---|---|
| 2025 | Sun 9 Mar 2025 | Sun 2 Nov 2025 |
| 2026 | Sun 8 Mar 2026 | Sun 1 Nov 2026 |
| 2027 | Sun 14 Mar 2027 | Sun 7 Nov 2027 |

**DST trap:** Between US spring-forward and EU clock change (last Sunday of March), ET/CT/PT are temporarily offset by 1 extra hour from European zones. Flag this if scheduling international calls in mid-to-late March.

**When a time zone conflict is likely:**
- A stated time feels implausibly early or late for the context
- The other party is in a different region
- The meeting invite shows a different zone than the user's
- Always confirm zone when scheduling across regions

---

## Fiscal Year Reference

**Default assumption:** Calendar year (Q1 = Jan–Mar, Q2 = Apr–Jun, Q3 = Jul–Sep, Q4 = Oct–Dec)

**Always ask** when quarter language appears in a business or client context — do not assume calendar year. Common non-calendar fiscal years:

| FY Start | Q1 | Common in |
|---|---|---|
| 1 Jan | Jan–Mar | Most companies, default |
| 1 Apr | Apr–Jun | UK government, some nonprofits |
| 1 Jul | Jul–Sep | US federal government, many states, universities |
| 1 Oct | Oct–Dec | Some corporations |

**Many US states / universities:** FY starts 1 Jul (e.g., FY2026 = Jul 2025 – Jun 2026)
**US federal government FY:** Starts 1 Oct (so FY2026 = Oct 2025 – Sep 2026)

When the user's context suggests a non-calendar FY, ask: *"Are you working on a calendar fiscal year, or does your organization run a different cycle?"* before computing quarter deadlines.
