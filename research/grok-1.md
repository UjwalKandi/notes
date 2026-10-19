Below are seven ideas, scored against the rubric and ranked. Then a full 3-hour plan for the one I'd build.

## What tends to win with this rubric

| Criterion | What earns the points in 5 minutes with a judge |
|---|---|
| **Works (30)** | Build a **practice version of the target website or inbox** so the demo can't fail on a CAPTCHA, an expired login, or a site blocking automation. Sign in to everything ahead of time, since logins stay saved on the cloud computer. |
| **Real work (25)** | End with something real: a filled form, a sent email, a draft letter. A summary on its own won't score well. |
| **Grok Bot use (20)** | Show features only Grok Bot has: several named Bots in a **group chat passing work to each other**, the Bot **using a browser on its cloud computer**, an **approval card**, a **voice memo**, a **scheduled or triggered routine**, and **@bot on X**. |
| **Uniqueness (10)** | Avoid inbox summaries, meeting notes, research reports and sales outreach. The docs already use those as examples. |
| **Clarity (15)** | A one-sentence pitch with a before/after number, like "This letter took 40 minutes. The Bots did it in 90 seconds." |

**Adapter Mind ($100 free credits):** use it to give the Bots background knowledge from the start, such as rules, policy documents and fee schedules. Bots that cite their sources make **Real work** stronger and show you used a sponsor's tool.

---

## The ideas, ranked

These scores are my own estimates. I added up the totals with a script.

| # | Idea | Field | Works /30 | Real /25 | Grok /20 | Unique /10 | Clarity /15 | **Total** |
|---|---|---|---|---|---|---|---|---|
| 1 | 🏆 **Red Tape Runner** | Personal admin / government paperwork | 24 | 23 | 18 | 9 | 14 | **88** |
| 2 | **Pager Squad** | DevOps / SRE | 26 | 23 | 18 | 5 | 14 | **86** |
| 3 | **Money Back Desk** | Consumer finance | 24 | 22 | 16 | 7 | 15 | **84** |
| 4 | **Scroll-to-Done** | Social / attention span | 22 | 18 | 20 | 9 | 14 | **83** |
| 5 | **Prior Auth Runner** | Healthcare billing | 20 | 24 | 17 | 9 | 12 | **82** |
| 6 | **Visa-Aware Job Desk** | Careers / immigration | 23 | 20 | 16 | 6 | 13 | **78** |
| 7 | **Travel Rebooker** | Travel | 18 | 21 | 17 | 7 | 14 | **77** |

### 1. 🏆 Red Tape Runner: "Snap a letter. The Bots handle it."
Official letters pile up unopened: parking tickets, DMV renewals, jury summons, insurance notices. That's exactly the kind of thing people forget about while scrolling.

| Bot | Job | Grok Bot feature it shows |
|---|---|---|
| 📎 **Clerk** | Reads the photo, pulls out the deadline, fee and required action, and cites the rule it's based on | iPhone share sheet, image reading, Adapter Mind |
| 🏛 **Runner** | Opens the website and fills in the dispute, renewal or response form, **stopping at Submit** | Browser use on the cloud computer, approval card |
| ✍️ **Advocate** | Writes the appeal letter or email and points out what the law allows | Email draft card |
| 🔔 **Nudger** | Sends a voice memo when it's done, and a daily routine counts down to deadlines | Voice memo, routine, notifications |

- **What you build around it:** a simple practice "City of Austin Parking Portal" site (Flask, about 45 minutes), so filling in the form works every time on stage.
- **Why it wins:** it's a real everyday problem, unusual for a hackathon, and the Bot visibly filling in a form is impressive to watch.

### 2. Pager Squad: on-call help for small teams *(fits your DataOps background)*
An alert posted in Slack starts a routine. 🚨 **Triage** reads the dashboards in the browser, 🔬 **Investigator** runs terminal checks and reads the logs, and 📝 **Scribe** drafts the status-page update and the incident write-up for approval.
- **What you build around it:** a small demo service with a **"break it" button**, Grafana dashboards, and a `#alerts` channel.
- **Strengths and weaknesses:** it's the most likely to work on demo day and is clearly useful. The downside is that it's close to the docs' "Product Performance" example, so it scores low on uniqueness.

### 3. Money Back Desk: "Finds money you're owed and files the claim"
🔎 **Scout** checks a test inbox for late deliveries, price drops after purchase, forgotten subscriptions and flight delays. 💸 **Claimer** fills out refund or claim forms. 📊 **Tally** sends a voice memo like "You recovered $212."
- **What you build around it:** a test Gmail inbox filled with realistic receipts, plus a practice merchant refund page.
- **Strengths and weaknesses:** the easiest pitch to explain. The main risk is that real store websites need logins.

### 4. Scroll-to-Done: "@bot, do this"
While scrolling X, you reply "@bot do this" to any post. An event post becomes a calendar event plus tickets left at checkout. A recipe becomes a grocery cart. A job post becomes a tailored application draft.
- **Strengths and weaknesses:** it uses the most Grok-only features and fits your original attention-span idea. But @bot ignores posts with video or GIFs, response time on stage is unpredictable, and you depend on content moderation.
- **Suggestion:** add this as a **second way to send work into idea #1**, not as a project on its own.

### 5. Prior Auth Runner *(healthcare billing)*
A clinic Team Bot in Slack. It collects the needed chart details from fake patient data, fills in a practice insurer's prior-authorization form, and drafts an appeal letter that cites the insurer's policy (from Adapter Mind). It's very useful, but you need to explain the problem first, so it scores lower on clarity.

### 6–7. Lower priority
- **Visa-Aware Job Desk:** finds jobs at companies with a history of sponsoring visas and drafts applications. Useful, but "AI job search" ideas are common.
- **Travel Rebooker:** watches a flight and rebooks it when it's cancelled. Live airline websites are hard to demo reliably.

---

## Recommended: Red Tape Runner, with "@bot" as a second way in

### 3-hour plan (team of 5)

| Time | Person A: **Bot lead** | Person B: **Web/infra** | Person C: **Knowledge** | Person D: **Routines/QA** | Person E: **Pitch** |
|---|---|---|---|---|---|
| 0:00–0:30 | Create the 4 Bots, write their descriptions and boundaries | Set up the Flask practice portal | Load the city rules and fee schedule into Adapter Mind | Set up Auto Review rules | Write the 1-minute pitch, collect sample letters |
| 0:30–1:30 | Group chat and hand-off between Bots | Portal form with a confirmation page, deployed | Clerk extracts the details and cites sources | Countdown routine with a **Test run** | Prepare letters: parking ticket, DMV, jury summons |
| 1:30–2:15 | "Teach a task" on the portal, save it as a skill | Optional: a stats page ("saved 40 min, $0 late fee") | Advocate drafts the appeal letter | Test @bot capture from X | Draft slides and talking points |
| 2:15–2:45 | **Practice the full demo 3 times, start to finish** | Fix bugs | Fix bugs | Sign in to everything again | **Record a backup video** |
| 2:45–3:00 | Stop adding features. Reset the demo state | | | | Rehearse the timing |

### Bot setup (paste into each Bot's description)
```text
Clerk — Job: Read official letters. Extract issuer, deadline, amount, required action,
and cite the governing rule (Adapter Mind). Write a row to /workspace/redtape/ledger.csv,
then hand off to Runner. Never contact anyone.

Runner — Job: Complete the required web form using the ledger row. Stop before any
Submit/Pay button and request approval. Never enter payment details.
```
**Auto Review:** let Bots edit files in `/workspace/redtape/` automatically. Make them **ask first** before clicking Submit, sending an email, or paying anything.

### 60-second demo script
1. **(10s) The problem:** "The average letter like this costs 40 minutes, or a late fee because it sat under a pile."
2. **(25s) Live:** share a photo of a parking ticket from an iPhone. In the group chat, Clerk cites the rule and the deadline, then hands off to Runner. Runner fills in the portal on the cloud computer and an approval card appears.
3. **(15s)** Tap **Allow**. The confirmation page loads and a voice memo from Nudger plays: "Dispute filed. Hearing reminder set for Nov 3."
4. **(10s) Why it matters:** "Four Bots, one photo, no forms. It runs while your phone is in your pocket."

### Risks and backups
- **Signing in to sites during the demo:** sign in ahead of time. Logins stay saved on the cloud computer.
- **Approval expiring:** approvals from routines expire after about 10 minutes. Start the demo from your own message so the approval card waits for you.
- **Something breaks live:** play the backup video recorded at 2:30 and move on to explaining the value.

**Your strongest option:** if your team has DevOps experience, **Pager Squad** is the safer choice for **Works (30)**. It gives up uniqueness points, but since Works is worth three times as much as Uniqueness, that can be the better trade.

I can write the Flask practice portal and all four Bot descriptions next if you'd like.
