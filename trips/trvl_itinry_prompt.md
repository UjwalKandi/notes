Here is the reusable prompt. Copy it, fill in the bracketed variables, and submit. Everything else stays fixed.

---

# TRAVEL ITINERARY PROMPT TEMPLATE

---

```
You are an expert travel planner, local knowledge researcher, and 
photography guide specializing in solo travel itinerary design for 
weekend and short trips. Generate a comprehensive, research-driven 
travel recommendation using the trip details below.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SECTION 1 — TRIP VARIABLES [FILL IN HERE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TRAVELER PROFILE:
- Type: Solo traveler
- Experience level: [First-time traveler / Seasoned solo traveler]
- Primary interest: [Photographer / Hiker / Culture / Food / Adventure]
- Budget tier: [Student budget / Mid-range / No limit]
- Sleep: [Normal / Can operate on minimal sleep]

FLIGHTS:
- Inbound: [Origin City (CODE)] → [Landing City (CODE)]
  Date: [Weekday, Month DD, YYYY]
  Depart: [HH:MM AM/PM] | Arrive: [HH:MM AM/PM]

- Outbound: [Departure City (CODE)] → [Return City (CODE)]
  Date: [Weekday, Month DD, YYYY]
  Depart: [HH:MM AM/PM] | Arrive: [HH:MM AM/PM]

TRIP STRUCTURE:
- Land in: [City A]
- End trip in: [City B — can differ from landing city]
- Total available time: [X days]
- Travel dates: [Start Date] → [End Date]

REQUESTED DESTINATIONS (research and prioritize all):
1. [Location]
2. [Location]
3. [Location]
4. [Location]
5. [Add as many as needed]

CONSTRAINTS AND PREFERENCES:
- [Any budget limits, physical limitations, items willing to skip]
- [Any spots the traveler is willing to drop if something better fits]
- [Any permits or bookings already confirmed]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SECTION 2 — REQUIRED OUTPUT [NEVER CHANGE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Provide every section below in full. Do not skip or abbreviate any.
Output must be in clean markdown only — no HTML, no apps, no mockups.

──────────────────────────────────────────
1. FEASIBILITY ANALYSIS
──────────────────────────────────────────
- Map every requested destination against actual available time
- Calculate real drive times between each location using specific 
  highway numbers and routing
- Clearly state which locations are feasible, which are not, 
  and exactly why — give the driving math
- Identify which locations would require sacrificing better options
- Produce a viral/portfolio potential score (1–10) for each requested 
  location and use this ranking to justify what stays and what goes

──────────────────────────────────────────
2. ROUTE OPTIONS
──────────────────────────────────────────
- If the geography supports two distinct approaches, present both
- For each option: total mileage, total drive time, 
  locations covered, locations dropped
- State the explicit trade-off between the two options
- Give a clear recommendation for which option maximizes 
  the traveler's primary interest (photography / hiking / etc.)

──────────────────────────────────────────
3. PHOTOGRAPHY / PRIMARY INTEREST STRATEGY
──────────────────────────────────────────
If traveler is a photographer, state and apply this rule throughout:
  → Natural landscapes: schedule at sunrise and sunset only
  → Urban / city locations: schedule at night only
  → Never waste golden hour on an urban location
  → Never shoot landscapes at night (except intentional astro)
  
Every golden hour slot in the itinerary must be filled with the 
highest viral-score landscape available at that time.

If traveler has a different primary interest (hiking, culture, food),
define an equivalent scheduling strategy for that interest.

──────────────────────────────────────────
4. DAY-BY-DAY ITINERARY
──────────────────────────────────────────
For every day from arrival to departure, provide a time-stamped
schedule in table format including:

  - Wake time
  - Every stop: arrival time, local time zone, activity, departure time
  - Every drive leg: miles + drive time + specific roads/highways
  - Golden hour sessions: exact sunrise/sunset time for that date 
    and location (not generic — calculate for the actual date)
  - At each photography/primary interest stop:
      • What specifically to shoot: compositions, angles, subjects
      • Best physical position within the location
      • Lens recommendation (wide angle mm / telephoto mm)
      • Camera settings: ISO, shutter speed, aperture
      • The specific 15–30 min window within golden hour that 
        produces the strongest frames
      • Any unique condition that exists only at this location
  - Food stop at each location: specific restaurant or area, 
    what to order, cost range
  - Accommodation for overnight stops: specific options with 
    price range and rationale

──────────────────────────────────────────
5. GOLDEN HOUR MASTER SCHEDULE
──────────────────────────────────────────
- Table: Day | Time | Zone | Location | Subject | Score
- Total count of golden hour sessions across the full trip
- Note if moon phase creates astrophotography opportunity
  (state moon phase for the specific travel dates)

──────────────────────────────────────────
6. TIME ZONE REFERENCE
──────────────────────────────────────────
- Every time zone the trip crosses, with UTC offset
- Where and when the traveler gains or loses clock hours
- Flag any unusual DST situations (territories/states that 
  do not observe DST, or indigenous nation land that differs 
  from surrounding state)
- Practical impact on scheduling for this specific route

──────────────────────────────────────────
7. PRE-DEPARTURE BOOKING CHECKLIST
──────────────────────────────────────────
- Every item requiring advance booking, in priority order
- Timed-entry permits and trails that sell out (platform + lead time)
- Tours requiring advance reservation (with booking platform)
- Accommodation locations with limited availability
- Any passes or cards worth buying before the trip
- Rental car / transport bookings

──────────────────────────────────────────
8. ENTRY FEES + PERMITS
──────────────────────────────────────────
- Every park, trail, attraction: fee + how to pay
- Annual pass options and whether they pay off for this trip
- Any timed-entry permits (with booking platform)
- Any residency or age-based fee structures relevant to 
  the traveler's profile
- Total estimated fees for the trip

──────────────────────────────────────────
9. FOOD GUIDE
──────────────────────────────────────────
Separate from tourist-facing recommendations. Provide:
- Where locals actually eat at each major stop
- Specific restaurant names with dishes and price range
- Hidden local food knowledge (spots that require insider awareness)
- Road trip provisions: what to buy, where, water requirements 
  for any hiking or desert driving
- Budget food strategy for the full trip

──────────────────────────────────────────
10. ACCOMMODATION GUIDE
──────────────────────────────────────────
- Budget, mid-range, and one splurge option at each overnight stop
- Specific property names where possible
- Which overnight base minimizes driving to morning 
  golden hour locations
- Any locations where accommodation is scarce and must be 
  booked weeks in advance

──────────────────────────────────────────
11. BUDGET BREAKDOWN
──────────────────────────────────────────
- Itemized table: flights, rental car, gas, accommodation, 
  food, entries/permits
- Low and high estimates per category
- Realistic total target
- Specific cost-saving strategies for this exact route

──────────────────────────────────────────
12. FULL DRIVING REFERENCE TABLE
──────────────────────────────────────────
- Every leg: origin → destination, miles, drive time, route
- Total trip mileage and total drive time
- Note which drives involve time zone crossings

──────────────────────────────────────────
13. WHAT TO SKIP — WITH REASONS
──────────────────────────────────────────
- Every requested location that cannot realistically fit
- Specific reason: drive time math, wrong direction, 
  better alternative available, safety concern
- What each skipped location would be ideal for on 
  a different trip structure

──────────────────────────────────────────
14. PORTFOLIO / EXPERIENCE SHOT LIST
──────────────────────────────────────────
- The 8–12 defining images or experiences this trip produces
- For each: location, light condition, why it works for 
  portfolio or social media
- Ranked by viral/impact potential

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SECTION 3 — QUALITY STANDARDS [NEVER CHANGE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RESEARCH ACCURACY:
- Drive times based on actual highway routing with road names
- Sunrise and sunset times accurate to the specific date and 
  coordinates of each location — not approximations
- Park fees, permit costs, and shuttle policies current and accurate
- Name specific trails, viewpoints, bridges, and overlooks —
  never use generic descriptions like "the viewpoint"
- Distinguish shuttle-only from car-accessible viewpoints
- State compass direction each viewpoint faces — this determines 
  whether it is a sunrise or sunset location

LOCAL KNOWLEDGE DEPTH:
- Food recommendations must be where locals eat, not tourist traps
- Identify any hidden gems that outperform popular alternatives
- Flag overrated locations relative to lesser-known alternatives
- For each photography stop, identify the specific best physical 
  position within the location — not just the location name

PHOTOGRAPHY PRECISION:
- Compass orientation of every key viewpoint
- Tripod required vs. handheld viable — state for each
- Exact camera settings per location (not vague ranges)
- Identify the post-sunset or pre-sunrise window separately —
  note when it is stronger than the event itself
- Note any unique condition (light beam timing, reflection 
  windows, mist conditions) that exists only at specific hours

PRACTICAL REALISM:
- Account for gas stops, meal time, bathroom breaks in timing
- Flag any schedule that is genuinely tight vs. comfortable
- Do not include any location that cannot be visited with 
  meaningful time given the surrounding schedule
- Be explicit about trade-offs — if adding a stop sacrifices 
  a better one, state it directly
- Solo traveler safety: note any remote locations requiring 
  offline maps, extra water, or shared itinerary with someone

FORMAT RULES:
- Clean markdown only
- No HTML, no UI mockups, no visual apps
- Tables for: schedules, comparisons, budgets, drive times
- Times always include local time zone abbreviation
- All distances in miles
- Drive times rounded to nearest 5 minutes
- Bold location names at first mention within each day
```

---

## HOW TO USE

**Minimum edits to customize:**

| Variable | Example (this trip) | Your trip |
|---|---|---|
| Inbound flight | Austin (AUS) → Phoenix (PHX), Fri Apr 25, 8:03–8:42 PM | Fill in your flight |
| Outbound flight | Las Vegas (LAS) → Austin (AUS), Mon Apr 28, 4:11 PM | Fill in your flight |
| Land in | Phoenix, AZ | Your landing city |
| End trip in | Las Vegas, NV | Your departure city |
| Travel dates | Apr 25–28, 2025 | Your dates |
| Requested destinations | Sedona, Grand Canyon, Zion, Horseshoe Bend... | Your list |
| Traveler profile | Seasoned solo traveler, photographer, student budget | Your profile |

**Everything in Section 2 and Section 3 stays exactly as written.** Those are the instructions that produce the depth and format of this response. Only Section 1 changes per trip.

---

## WHAT THIS PROMPT PRODUCES

Running this prompt generates the same structure as the full Las Vegas trip — including:

- Feasibility verdict on every requested spot with driving math
- Two route options where geography allows, with explicit trade-offs
- Precise timing schedule from landing to departure
- Photography compositions, settings, and best positions per stop
- Golden hour schedule rated by viral/portfolio potential
- Time zone crossings and their scheduling impact
- Pre-departure booking checklist in priority order
- Local food recommendations, not tourist guides
- Itemized budget with realistic total
- Complete driving reference with road names and mileage
- Honest list of what to skip and why
