



Now here is the complete section-by-section breakdown with every issue called out and exact replacement text you can paste in.

---

## ① HEADER

**Issues:**
- `Austin, Texas` — use the standard abbreviation `Austin, TX`
- You have 5 contact items (phone, location, email, LinkedIn, website) — book recommends max 4; drop the personal website unless it's actively maintained and impressive
- `ujwal.kandi@utexas.edu` — university emails expire after graduation; switch to a permanent personal email (e.g. Gmail) before applying

**Paste this in:**
```
Ujwal Kandi
Austin, TX  ·  (737) 326-8598  ·  ujwal.kandi@gmail.com  ·  linkedin.com/in/ujwalkandi
```
*(Replace the Gmail placeholder with your actual personal email. Drop the website for now — only add it back if the site clearly showcases your Data Ops / Observability work.)*

---

## ② PROFESSIONAL SUMMARY

**Issues:**
1. Opens with `"I've kept systems running"` — casual tagline, not a professional opener; reads as AI-written
2. `"SRE & Observability Engineer"` — this is not your career identity; your entire background is Data Operations; pick a title that matches your actual work history
3. `"designed AI-powered edge diagnostics deployed using Docker"` — irrelevant to all four target roles; leading with AI work misrepresents your strongest skill set
4. `"architected data pipelines for Fortune 500 clients"` — generic AI phrase; you managed and maintained pipelines, not architected them from scratch (be accurate)
5. `"Core strengths: Incident Response…"` block at the end — this entirely duplicates your Technical Skills section and wastes 3 lines of prime real estate
6. The summary tries to be SRE + AI + data + Fortune 500 all at once — it ends up saying nothing specifically useful to the recruiter

**Paste this in:**
```
Data Operations & Observability Engineer with 3+ years managing production pipelines,
monitoring infrastructure, and driving incident response across 16+ enterprise environments
for clients including Disney, Bank of America, and Marriott. Reduced MTTR by 75% and
maintained 99.8% SLA compliance through observability tooling, automated alerting, and
structured RCA workflows. Hands-on with Apache Airflow, AWS, Azure, Grafana, Dynatrace,
ELK Stack, Terraform, and Python automation. M.S. in IT Management, UT Austin (May 2024).
```

---

## ③ WORK EXPERIENCE

### ③a — Apple Inc.

**Issues:**
1. `"Metadata Operations Engineer (Contract via Welo Data)"` — cramming contract info into the title looks messy; format contract separately on a second line
2. `"Optimized mission-critical metadata ingestion pipelines ensuring high availability"` — `"mission-critical"` and `"ensuring high availability"` are AI filler; replace with concrete what/how/result
3. `"across XML workflows"` — ends the bullet with a vague technical detail instead of impact
4. `"improving platform stability"` in bullet 2 — not a metric; delete or replace with a real number
5. Bullet 4: `"ensuring continuity and compliance with Apple's service-level requirements"` — passive and unmeasurable; add what the SLA actually was if you know it

**Paste this in:**
```
Apple Inc. (Contract · Welo Data)                                     Jan 2025 – Present
Metadata & Data Operations Engineer                                         Austin, TX

• Managed metadata ingestion pipelines for Apple TV's global content catalog, maintaining
  sub-2s processing latency and 99.9% pipeline uptime through automated XML validation,
  error handling, and rollback procedures
• Built Grafana dashboards and alerting for pipeline health monitoring, reducing feed
  failures by 30% and cutting mean time to detection from hours to under 15 minutes
• Developed structured RCA frameworks and debugging runbooks for batch job failures,
  reducing resolution time by 25% and eliminating recurring failure categories across
  the operations team
• Designed and documented failover procedures for metadata systems, ensuring uninterrupted
  content availability during high-volume release windows in compliance with Apple SLA targets
```

---

### ③b — Sports Excitement

**Issues:**
1. `"Sports Excitement"` with no descriptor — a recruiter has no idea what this company does; one parenthetical phrase fixes it
2. `"Extract, Transform, Load (ETL)"` — you don't need to spell out ETL; this is a technical resume; it looks like padding
3. `"(Directed Acyclic Graph orchestration, performance tracking, and failure recovery)"` — over-explaining Airflow basics signals you're padding the resume
4. `"achieving response times within service-level targets"` — what were the targets? Give the number (e.g., `under 5 minutes`)
5. No mention of what data domain this was (sports analytics, betting odds, media) — gives context to scale

**Paste this in:**
```
Sports Excitement (Sports data analytics startup)                      Aug 2024 – Jan 2025
Data Operations Engineer                                                    Austin, TX

• Built and maintained 50+ daily Airflow ETL pipelines with 99.8% job reliability,
  implementing DAG-level retry logic, SLA breach alerts, and automated failure recovery
• Reduced Azure Data Lake query latency from ~1.2s to 550ms by restructuring partition
  schemas and optimizing storage tier configurations, directly improving downstream
  ML analytics throughput
• Automated incident alerting via Airflow trigger jobs integrated with PagerDuty,
  cutting mean response time to under 5 minutes against SLA targets
• Implemented pre-deployment data quality validation and load-testing frameworks,
  reducing post-release pipeline failures by 28%
```

---

### ③c — Dover Fueling Solutions

**Issues:**
1. No label indicating this was a 2-month contract — without it, a 2-month stint looks like a firing or a failed probation; label it `(Contract)` immediately
2. `"Enhanced visibility by implementing 3-pillar observability strategy"` — passive opener; start with the result
3. Bullet 2: `"Designed and deployed agentic AI system"` — the AI angle is not relevant to your target roles; lead with the operational outcome (ticket reduction, field resolution speed)
4. Bullet 3 about `"LangChain… LangSmith… token efficiency"` — cut entirely; this is AI/ML content irrelevant to Data Ops/Tech Ops/Observability/Network roles
5. `"improving critical fault resolution to under 10min"` — mixed tenses (`reducing` vs `improving`); must be consistent
6. `"high critical incident detection rate"` — vague; give the actual percentage

**Paste this in:**
```
Dover Fueling Solutions (Contract)                                     Jun 2024 – Aug 2024
Observability & Site Reliability Engineer                                   Austin, TX

• Deployed a 3-pillar observability stack (Grafana dashboards, Azure APIM log analytics,
  Kafka metrics exporters), reducing issue detection time from 15 min to 3 min and mean
  time to resolution by 40%
• Reduced field technician support tickets by 35% by building an edge diagnostics system
  for resource-constrained IoT hardware, enabling autonomous fault identification and
  resolution in under 10 minutes without remote escalation
• Tuned alert thresholds and implemented intelligent escalation workflows, cutting alert
  fatigue by 45% while maintaining 100% detection rate for P1/P2 severity incidents
• Authored on-call runbooks and escalation playbooks for edge device monitoring,
  standardizing incident response procedures across a distributed field operations team
```

---

### ③d — Stronghold Investment Management ← REMOVE FROM WORK EXPERIENCE

**Issue (critical):** This is a graduate capstone project, not employment. Listing it under Work Experience is a misrepresentation that will surface in any background check. Remove it entirely from this section.

**Action:** Delete this entry from Work Experience. The replacement text for where it goes (Projects section) is provided in Section ⑤ below.

---

### ③e + ③f — Epsilon (two entries) → MERGE INTO ONE

**Issues:**
1. `"Epsilon"` and `"Epsilon Data Management, LLC."` are the same company listed as two separate employers — this hides the promotion and makes the resume look like two separate short stints
2. The promotion from Data Programmer I to Production Support Engineer is the strongest career progression signal on your resume — it's invisible in the current format
3. `"Orchestrated incident response lifecycle (detection → triage → resolution → RCA → prevention)"` — the arrow symbols `→` may not render in all ATS parsers; also "orchestrated" is an overused AI-resume word
4. `"preventing 6 out of 10 potential incidents"` — oddly specific phrasing that reads as fabricated; rephrase as a percentage
5. `"improving system performance by 89%"` — this number needs context; 89% improvement in what specifically? Without context it sounds exaggerated; add what metric was improved
6. `"Implemented Infrastructure as Code business continuity framework"` — what IaC tool? CloudFormation? Terraform? Be specific
7. `"accelerating root cause identification by 30%"` — this number appears in both Epsilon entries; don't repeat the same metric twice

**Paste this in (single merged entry):**
```
Epsilon (Publicis Groupe)                                              Aug 2021 – Jul 2023
Production Support Engineer  ·  Promoted from Data Programmer I (Apr 2022)  ·  Bengaluru, India

Production Support Engineer  |  Apr 2022 – Jul 2023
• Managed end-to-end incident response lifecycle — detection, triage, resolution, RCA,
  and prevention — across 16 enterprise client environments, achieving 99.8% SLA compliance
  and reducing MTTR from ~45 min to 12 min
• Built observability infrastructure integrating CloudWatch, Kibana, and Dynatrace;
  established alert baselines and anomaly detection thresholds that prevented 60% of
  potential incidents before reaching customer impact
• Authored 20+ runbooks, SOPs, and postmortem documents; eliminated repeat incident
  categories by 50% through structured root cause prevention and team knowledge transfer
• Automated production failover for data pipelines using CloudFormation, reducing recovery
  time objective (RTO) from 2 hrs to 30 min and sustaining 99.9% uptime during critical
  processing windows
• Optimized Matillion ETL job scheduling — SLA windows, retry logic, and maintenance jobs
  — improving batch completion rate by 32% and cutting operational overhead by 38%

Data Programmer I  |  Aug 2021 – Apr 2022
• Engineered and maintained ETL pipelines for 16 global clients including Disney, Bank of
  America, Marriott, and National Geographic, reducing batch failure frequency by 45%
• Built production-replica staging environments and automated test pipelines, reducing
  post-deployment defects by 33%
• Deployed CloudWatch, Dynatrace, and Kibana observability stack across client environments,
  cutting root cause identification time by 30%
• Resolved critical data ingestion failures across AWS S3 and Redshift, restoring data
  availability for client analytics platforms within defined SLA windows
```

---

### ③g — Isthriwala

**Issues:**
1. No context on what Isthriwala does — a recruiter reading this has no frame of reference for the company's domain or scale
2. `"improving query performance"` — no metric; this is the most important part of a database optimization bullet and it's missing
3. `"improving decision-making velocity"` — filler phrase that means nothing; replace with what actually changed as a result of the dashboards

**Paste this in:**
```
Isthriwala (E-commerce operations startup)                             Jun 2020 – Jul 2021
Data Management Analyst                                                  Vijayawada, India

• Optimized SQL database operations for a platform serving 35,000+ customers and 50+ B2B
  clients, improving query response time by ~40% through index restructuring and query
  execution plan analysis
• Built real-time KPI dashboards in Tableau tracking inventory, sales, and operational
  metrics; dashboards directly contributed to a 15% improvement in monthly performance
  targets and were adopted as the primary reporting tool by the management team
```

---

## ④ TECHNICAL SKILLS *(move this section up, right after Summary)*

**Issues:**
1. `CloudWatch` listed twice — once under Observability and again under AWS Cloud Platforms
2. `JavaScript` and `R` — not used in any of your roles and irrelevant to all four target roles; remove
3. `Azure AI Studio` under Cloud Platforms — an AI tool, not a cloud infrastructure tool; irrelevant to target roles; remove
4. `AI/ML & LLM` category (`LangChain, Ollama, LangSmith, ChromaDB`) — none of these are relevant to Data Ops, Tech Ops, Observability, or Network Engineering; remove this category
5. `Databricks` listed under AI/ML — Databricks is a data engineering platform; move it to Data Pipelines
6. `Microsoft Office Suite` in Tools — too generic for a technical resume; remove
7. Missing: `PagerDuty` (you mentioned it in Sports Excitement bullets); `Matillion` (used at Epsilon); `PostgreSQL` / `MySQL` as standalone database entries
8. No `Databases` category — you work with S3, Redshift, ADLS, and SQL constantly; consolidate into a visible category

**Paste this in:**
```
Technical Skills

Languages & Scripting:       Python, Bash, SQL, YAML, XML
Observability & Monitoring:  Grafana, Prometheus, Dynatrace, Kibana, Azure Monitor,
                             ELK Stack (Elasticsearch, Logstash, Kibana), CloudWatch
Data Pipelines & ETL:        Apache Airflow, Matillion, Apache Kafka, Spark, PySpark,
                             AWS Glue, Databricks
Infrastructure & IaC:        Terraform, CloudFormation, Ansible, Docker, Kubernetes
Cloud Platforms:             AWS (EC2, S3, Lambda, Redshift, CloudWatch, Glue),
                             Azure (ADLS, Log Analytics, APIM, Azure Monitor)
Databases:                   PostgreSQL, MySQL, AWS Redshift, Azure Data Lake,
                             SQLite, AWS S3
CI/CD & Version Control:     Git, GitHub, Jenkins, GitLab CI/CD
Ops & Ticketing Tools:       ServiceNow, PagerDuty, Jira, Tableau, Power BI
```

---

## ⑤ EDUCATION *(move to last section)*

**Issues:**
1. **No graduation date** — this is the single most important piece of information a recruiter needs from this section; it's completely absent
2. `"Jun 2023 – May 2024"` is shown as a date range that bleeds visually into the Projects section — the formatting creates confusion about whether these are education dates or project dates
3. `"Unstructured Data Analytics"` — not relevant to any of the four target roles
4. `"Data Governance & Responsible AI"` — not relevant to Data Ops/Tech Ops/Observability/Network
5. If you have an undergraduate degree, it must be listed here; omitting it creates a gap that recruiters notice

**Paste this in:**
```
Education

The University of Texas at Austin                                      Aug 2023 – May 2024
M.S. in Information Technology & Management — McCombs School of Business   Austin, TX
Relevant Coursework: Distributed Systems, Cloud Infrastructure, Data Management,
Database Systems

[Your Undergraduate University Name]                                   [Year Graduated]
B.Tech / B.E. in [Your Field]                                          [City, Country]
```
*(Fill in your actual undergrad details. If you don't have an undergraduate degree, remove that block.)*

---

## ⑥ PROJECTS *(reorder and replace)*

**Issues:**
1. **Order is wrong** — `A.I.D.E.` (your only project directly relevant to Observability/Data Ops) is listed last; it must come first
2. `ARIA – Adaptive Retail Intelligence Agent` — a retail voice chatbot has zero relevance to Data Ops, Tech Ops, Observability, or Network Engineering; remove from this section
3. `FlipFinder Pro – Real-Time AI System for Used-Car Arbitrage` — a car price prediction tool has no relevance to any of the four target roles; remove
4. No dates on any project — the book explicitly says dates must be present
5. `A.I.D.E.` description overloads on AI jargon; reframe it as an observability and diagnostics tool
6. Stronghold capstone must move here from Work Experience

**Paste this in:**
```
Projects

A.I.D.E. — Observability & Automated Root Cause Analysis Engine          Apr 2024
• Built a full-stack observability system combining real-time metrics analysis, log
  anomaly detection, and automated RCA workflows for API and infrastructure health monitoring
• Integrated Prometheus and Grafana for live dashboarding; deployed on-device inference
  via Ollama (Llama 3) under a 2GB memory constraint, achieving sub-second diagnostic
  response times and reducing simulated MTTR by 60% vs. manual investigation
  [github.com/ujwalkandi/aide]

Azure Document Intelligence Pipeline — Graduate Capstone               Jan–May 2024
  (Stronghold Investment Management)
• Automated ownership document verification workflows using Azure AI Studio (OCR +
  Mixtral LLM), processing 500+ documents per day with end-to-end pipeline monitoring
• Reduced manual document processing costs by 20% through automated validation;
  benchmarked response accuracy and latency across 4 Azure-hosted LLM configurations
```
*(If you have a GitHub repo for the capstone, add the link. Remove ARIA and FlipFinder from this section. If you want to preserve ARIA for breadth, add one single line: "ARIA — ElevenLabs Hackathon Winner (Dec 2024): Real-time voice retail agent using multi-agent LLM orchestration.")*

---

## ⑦ LEADERSHIP & ACHIEVEMENTS

**Issues:**
1. `"Dec 2025"` on the ElevenLabs hackathon — future date; this is a typo; fix to `Dec 2024`
2. `"Dover Fueling Solutions Hackathon'24 – First Place"` — Dover is already in your Work Experience section; listing it again here is redundant; it reads like you're padding
3. `"MSITM Campus Ambassador Aug 2023 – May 2024"` — a student ambassador role at this stage of your career adds no value to a technical hiring manager; remove
4. `"University Innovation Fellows Jan 2018 – Dec 2020"` — 5+ years old, non-technical, adds no value and creates an age signal; remove
5. Section is bloated; trim to 3 entries maximum

**Paste this in:**
```
Leadership & Achievements

ElevenLabs Worldwide Hackathon — 1st Place & Best Product Built with n8n    Dec 2024
• Won global hackathon (1,000+ participants) building ARIA, a real-time voice-based
  retail intelligence agent using multi-agent LLM orchestration and automated
  post-purchase workflows

Dover Fueling Solutions Hackathon — 1st Place                               Feb 2024
• Designed an edge AI diagnostics system for fuel station operations; presented solution
  to DFS executive leadership at the 2024 DFS User Conference

Seeed Studio Ranger — AI/IoT Developer Program                    Oct 2024 – Present
• Delivered 10+ hands-on technical workshops on edge computing and IoT systems
  to 100+ university students
```

---

## ⑧ Network Engineer Role — Honest Gap Assessment

The book is clear: a resume should represent you fairly, not over-promise. Your resume currently has **no** traditional networking credentials — no mention of TCP/IP, routing protocols, VLANs, BGP, OSPF, Wireshark, SNMP, or network monitoring tools like Nagios or SolarWinds. Applying for a Network Engineer role with this resume as-is will result in rejections.

**What you should do:**
- Target **Network Operations Center (NOC) Engineer** or **Network Observability Engineer** roles instead — these leverage your monitoring, alerting, incident response, and Kafka/API stack experience without requiring deep routing/switching credentials
- If you genuinely want Network Engineer roles, pursue a **CompTIA Network+** or **Cisco CCNA** certification; once you have it, add a Certifications section and update the skills section with: `Networking: TCP/IP, VLANs, DNS, DHCP, Azure APIM, Kafka broker monitoring`
- Your **strongest fits in priority order** are: Observability Engineer → Data Operations Engineer → Tech Ops Engineer → Network Operations (NOC)

---

## Final Checklist Before Submitting

| # | Action | Status |
|---|---|---|
| 1 | Move Technical Skills section above Work Experience | ☐ |
| 2 | Move Education section to the very bottom | ☐ |
| 3 | Remove Stronghold from Work Experience; paste into Projects | ☐ |
| 4 | Merge two Epsilon entries into one with promotion visible | ☐ |
| 5 | Fix "Dec 2025" → "Dec 2024" | ☐ |
| 6 | Add graduation date to M.S. entry | ☐ |
| 7 | Replace university email with personal permanent email | ☐ |
| 8 | Remove ARIA and FlipFinder from Projects | ☐ |
| 9 | Remove Campus Ambassador and University Innovation Fellows | ☐ |
| 10 | Remove JavaScript, R, Azure AI Studio, LLM tools from Skills | ☐ |
| 11 | Add company descriptors for Sports Excitement and Isthriwala | ☐ |
| 12 | Label Dover as Contract | ☐ |
| 13 | Delete "Core Strengths" block from Summary | ☐ |
| 14 | Verify all links (LinkedIn, GitHub, website) are live and current | ☐ |
