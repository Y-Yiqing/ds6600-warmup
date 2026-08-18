# DS 6600: Data Engineering 1 — Day-by-Day Schedule, Fall 2026 (Draft)

Tuesdays and Thursdays, 2:00–3:15pm | Data Science 246

**31 meeting slots** between Tue 8/25 and Tue 12/8, minus three university holidays, minus one optional work session = **27 substantive class sessions.**

*Year inferred as 2026 — Aug 25, Oct 6, and Nov 3 fall on Tuesdays and Nov 26 on Thanksgiving Thursday only in that year.*

**Design principle:** every unit answers a problem the students have already hit themselves, and every week moves their own project forward.

Every session carries a **Dashboard build** block: the specific thing we do to the Congress Transparency Dashboard that day. The dashboard is never a motivating anecdote — it is the artifact under the knife in all 27 sessions, and it exists in a running (if terrible) state from the second class onward.

---

## Data sources — status for Fall 2026

| Source | Access | Role in course | Set-up lead time |
|---|---|---|---|
| **api.congress.gov** | REST API, instant key | Members, bills, committees, sponsorship | None |
| **Voteview** | Bulk CSV, no auth | Roll-call votes, NOMINATE scores, ICPSR IDs | None |
| **congress.gov bill texts** | Scraping | Bill full text for the document store | None |
| **openFEC** | REST API, api.data.gov key | Contributions, **outside spending / dark money** | Minutes |
| **NewsAPI** | REST API, free tier | Articles mentioning members | Days |
| **OpenSecrets bulk** | Manual download, approval-gated | Industry/sector classification only | **Weeks — register in August** |

> ### ⚠️ OpenSecrets changed since this course last ran
> **The OpenSecrets API was discontinued on April 15, 2025**, after 17 years. Only bulk data remains, and getting it means agreeing to educational-use terms, registering a Bulk Data account, confirming email, and *waiting for approval*. The files are CC BY-NC-SA and lag the live site by months.
>
> **OpenSecrets is therefore demoted from an API source to a flat-file source**, and it is no longer load-bearing. Campaign finance moves to the FEC directly. Keep OpenSecrets for the one thing the FEC genuinely cannot give you — industry and sector classification of contributors — and design the dashboard so it degrades gracefully if approvals don't arrive for every student.

---

## Assessments at a glance

| Stream | Count | Points each | Total |
|---|---|---|---|
| Labs (Part A + Part B) | 9 | 20 | 180 |
| Data pipeline project | 1 | 180 | 180 |
| | | **Total** | **360** |

**GitHub check-ins are eliminated.** Every lab is submitted by push to the course repo and builds directly on that day's live coding, so the check-ins measured the same thing twice. **Project check-ins are also eliminated** — Part B of each lab *is* the check-in, and it now happens nine times instead of twice.

### How the two parts work

**Part A — instructor-chosen application.** Same task for everyone, known answer, autograded by pytest inside the course container. Tests correctness. *Deliberately not Congress data* — it's the transfer test that proves the technique generalizes past the in-class example.

**Part B — the student's own pipeline.** Idiosyncratic per student. **Graded on movement, not correctness** — did the pipeline advance, is the reasoning sound? A skim of the diff, not a code review.

Every Part B carries a short **written design rationale**. Mandatory, never optional: it requires the student's own data and judgment, it's the one component a coding agent can't produce for them, and it's where the real teaching signal is.

So each week has three passes at the same technique: **we do it to Congress in class, they do it to a new dataset in Part A, they do it to their own data in Part B.**

### Standing rules for Part B

- **Not every technique fits every project.** Part B is always: apply the day's technique to your pipeline, *or* justify in a paragraph why it doesn't apply and complete the stated alternative. The justification is graded as design reasoning and earns full credit.
- **Topic and sources lock Friday 9/4** (Lab 1B) — far earlier than the old first check-in. Part B is a dependency chain; a student blocked on data access in week five is blocked all semester.
- **Keep an approved fallback dataset ready.** Some students' first-choice source will fall through — an API approval that never arrives, a site that blocks scrapers, a license that won't permit republication. Plan for it in August rather than improvising in October.

### Lab due dates (all 11:59pm)

| Lab | Topic | Issued | Due | Pts |
|---|---|---|---|---|
| 0 | Setup (ungraded) | Tue 8/25 | **Fri 8/28** | — |
| 1 | Environments + **topic lock** | Thu 9/3 | **Fri 9/4** | 20 |
| 2 | Dockerfiles and Compose | Thu 9/10 | **Fri 9/11** | 20 |
| 3 | Secrets and flat files | Thu 9/17 | **Fri 9/18** | 20 |
| 4 | APIs at scale | Thu 9/24 | **Fri 9/25** | 20 |
| 5 | Scraping + entity resolution *(double)* | Thu 10/1 | **Fri 10/16** | 20 |
| 6 | Schema design + SQL *(double)* | Tue 10/20 | **Fri 10/30** | 20 |
| 7 | Document store and search | Thu 11/5 | **Fri 11/13** | 20 |
| 8 | Idempotency, testing, code review | Thu 11/12 | **Fri 11/20** | 20 |
| 9 | Visualization + working dashboard *(double)* | Thu 11/19 | **Fri 12/4** | 20 |

---

# Schedule

## Part 0 — The Walking Skeleton

The whole pipeline, badly, in the first week. Everything after this is a repair.

### **Tue 8/25 — Session 1: Introductions, syllabus, and the first pull**

**No prior Git experience assumed, and no session spent on it.** Installation and — critically — GitHub authentication happen before the semester in the Lab 0 module, so class time goes to the ritual rather than the setup. Git is not a topic in this course; it's something we do 27 times.

**Ten minutes on Git, total:** confirm every clone worked, hand out the command card, and do one `add` / `commit` / `push` together as a group. Five commands, no branching. State the **amnesty policy out loud** — if Git breaks in a way you can't fix, delete the folder, re-clone, and message me; anything pushed is safe. Every cohort has a student who lands in a detached HEAD and quietly stops pushing out of embarrassment, and naming the escape hatch in advance is worth more than any tutorial. Pair anyone who looks nervous with a Git-experienced classmate; the collaboration policy already permits it.

> **Dashboard build.** Everyone clones the course repo. We write `pull_members.py`: a single `GET /v3/member?limit=20`, `json_normalize` the response, write `data/members_raw.csv`. **We paste the API key directly into the script** — and I say out loud that this is a security bug we will fix on 9/15, so it lands as foreshadowing rather than sloppiness. Commit, push, verify on GitHub before anyone leaves.

> **Git thereafter — 90 seconds per week, at the push ritual that already ends every class.** `git log` on 9/1. `.gitignore` on 9/15, which that session needs anyway for `.env`. `git diff` on 9/22. `git checkout <session-tag>` on 10/1, doubling as the make-up mechanism for a missed class and the most persuasive possible demonstration of what version control is for. Branches and pull requests on 11/12, when Session 22 wires validation into GitHub Actions and Lab 8 needs a PR for CI to run against — by then they have twenty sessions of muscle memory and it takes ten minutes. Total added cost across the semester: about fifteen minutes.

### **Thu 8/27 — Session 2: Skeleton, part 2 — chart, app, push**

> **Dashboard build.** A seaborn countplot of our 20 members by party, saved to `figures/`. Then a 30-line Dash app: one dropdown of 20 names, one callback, a div showing name, state, and party. It runs on `localhost:8050` and it is genuinely ugly. Push it.

Spend the last 20 minutes cataloguing aloud everything wrong with it: the key is in the repo, it only runs on my machine, 20 members isn't 535, the CSV will be unusable at full scale, re-running gives different numbers, nobody outside this room can see it, and the Voteview names won't match these names. **Write the list on the board and photograph it.** It's the course roadmap, generated by the students, and every subsequent session opens by crossing something off it.

> **LAB 0 — opens two weeks before the semester, due before Session 1. Ungraded.** Moved off 8/28 and out of class entirely. Four installs, a GitHub account with working authentication, one line pushed to a **fork** of `jkropko/ds6600-warmup`, and all four credential requests submitted. Verification is just reading the repo's fork list before 8/25 — two minutes at this cohort size, and it hands you the roster of who is stuck **before** the first class rather than after.
>
> Distributed as a written module only. The real time sink is never `commit` and `push`; it's SSH keys, personal access tokens, 2FA, and the Windows credential manager — individually variable, and exactly what evaporates twenty minutes of class while everyone watches one student fight a passphrase prompt. Since there's no video to fall back on, the module leans on an explicit "email me if you're stuck more than 15 minutes" instruction, and it's worth checking the fork list a few days before 8/25 so you can reach out to anyone who hasn't appeared rather than waiting for them to ask.

> **Fork for Lab 0; save GitHub Classroom for the graded labs.** A fork needs no infrastructure, uses a URL that already exists, and teaches a real GitHub concept. It also can't self-verify — Actions are disabled by default on forked repos — but automated verification isn't worth building for a one-off ungraded check with a PhD-sized cohort. From **Lab 1 onward** the calculus flips: Part A is autograded through Actions, submissions should be private rather than public forks, and per-student repos from a template are worth the setup. Stand Classroom up in August for Labs 1–9, not for Lab 0.

> **Before the semester:** send the Lab 0 module with the welcome email. Congress.gov and api.data.gov keys are instant; NewsAPI takes days; **OpenSecrets bulk approval can take weeks and must be started in August.**

---

## Part 1 — Reproducibility and Environments

Cut from seven sessions to five. Each one opens by crossing an item off the 8/27 board.

### **Tue 9/1 — Session 3: Python environments and conda**

> **Dashboard build.** Our skeleton imports pandas, requests, seaborn, and dash, and declares none of them. We build a clean `ds6600` conda environment from nothing, install into it until `pull_members.py` runs, and export both `environment.yml` and a pinned `requirements.txt`. Then I deliberately install a wrong pandas version, run the script, and we read the traceback together — this is why pinning is not pedantry.

### **Thu 9/3 — Session 4: Introduction to Docker**

> **Dashboard build.** I ask a Mac student and a Windows student to run the same skeleton and we compare what breaks. Then we pull `python:3.12-slim`, run it interactively, `pip install requests` inside it, and execute the member pull from within the container — proving the same code produces the same result on every laptop in the room.

> ### **LAB 1 — due Fri 9/4**
> **Part A:** A repo whose `requirements.txt` fails on a clean machine. Diagnose it, produce a working conda environment and a correctly pinned requirements file, reproduce a specified output.
> **Part B — TOPIC LOCK:** Topic proposal plus three candidate sources. For each: license and terms-of-use check, access mechanism, current status of the credential request. *Rationale:* why this topic, and what question the dashboard answers.

### **Tue 9/8 — Session 5: Dockerfiles; running images from DockerHub**

> **Dashboard build.** We write the project's first Dockerfile — `FROM python:3.12-slim`, copy requirements, `pip install`, `CMD python pull_members.py` — and build `congress-pipeline:0.1`. Push to DockerHub, then everyone pulls a *neighbor's* image and runs it. The member CSV comes out identical, which is the whole point.

### **Thu 9/10 — Session 6: Docker Compose — app plus a real database service**

> **Dashboard build.** We add a `postgres` service to `docker-compose.yml` and rewrite the pull to `INSERT` the 20 members into a `members` table instead of writing CSV. The first attempt fails on connection refused, because the script says `localhost` and Compose networking wants the service name — a five-minute error worth every second, since it's the same error they'll hit in December on the deployed host.

> ### **LAB 2 — due Fri 9/11**
> **Part A:** Write a Dockerfile for a given application, build it, produce a specified output. Then a compose file adding a Postgres service; demonstrate the app connects.
> **Part B:** Containerize your own repo and push the image to DockerHub. One successful "hello world" pull from each of your three sources, proving access is real. *Rationale:* which source is primary, and what breaks if it disappears?

### **Tue 9/15 — Session 7: Volumes, networking, environment variables, and secrets**

> **Dashboard build.** `docker compose down`, then `up` — our members table is empty. We add a named volume and watch the data survive. Then we cross the first item off the 8/27 board: the Congress API key moves out of `pull_members.py` into `.env`, referenced by `env_file` in Compose, with `.env` in `.gitignore`. We now have four credentials to manage, not one. I demonstrate that a key baked in with `ARG` is still readable via `docker history`, and then **we rotate the Congress key**, because the old one has been in git history for three weeks and rotation is the actual remedy.

### **Thu 9/17 — Session 8: Flat files**

CSV, Excel, fixed-width, and **Parquet**. Stata/SAS/SPSS demoted to a reading with a `pandas` one-liner — keep them discoverable, stop spending a third of a session on them.

> **Dashboard build.** Two flat-file sources arrive with no API between them. Voteview's `HSall_members.csv` and `HSall_rollcalls.csv` download freely; **the OpenSecrets bulk files require the account we registered in August**, and we talk about why a source that was a REST API in 2024 is a gated ZIP file in 2026 — data access is a moving target and pipelines have to survive it. We inspect dtypes and find ICPSR numbers silently read as integers, `state_icpsr` losing leading zeros, and NOMINATE columns typed as strings wherever a footnote character appears. OpenSecrets' pipe-delimited format needs its own reader entirely. We fix dtypes on read, write to Parquet, and compare file size and load time against the CSV.

> ### **LAB 3 — due Fri 9/18**
> **Part A:** A deliberately awful file set — fixed-width with a separate layout document, Excel with merged multi-row headers, Parquet with conflicting dtypes. Load all three, reconcile the schemas, report reconciled row counts.
> **Part B:** One raw pull from each source landed in `data/raw/` as Parquet. Credentials in `.env`, `.env` in `.gitignore`. *Rationale:* state the grain of each source — one row equals what, exactly?

---

## Part 2 — Data Acquisition

### **Tue 9/22 — Session 9: APIs I — authentication, requests, and JSON**

> **Dashboard build.** We move from the toy 20-member call to the Congress API properly: header-based auth, checking `response.status_code` before parsing, and handling `GET /v3/member/{bioguideId}`, whose response nests `terms`, `partyHistory`, and `leadership` several levels deep. We flatten it with `json_normalize` and hit the real question — one member has many terms, so what is one row? I force a 404 with a bad bioguide ID and a 403 with a bad key so the error paths get written today, not in November.

### **Thu 9/24 — Session 10: APIs II — pagination, rate limits, retries, and caching**

> **Dashboard build.** Two APIs, two pagination strategies — which is the point. The Congress API caps at `limit=250` and pages by `offset`, so all 535 members takes three calls and every bill of the current Congress takes hundreds. **openFEC** pages at 100 results and warns against offset paging entirely on large result sets: we implement **keyset pagination** with `last_index`, and I show why naive page numbers silently drop and duplicate rows when new filings land mid-pull. That's a harder and more honest pagination problem than anything the Congress API poses. We add exponential backoff on 429, cache raw responses keyed by a hash of the request URL, and re-run the whole pull in two seconds with zero API calls — the moment students stop being afraid of re-running their own code.
>
> *Note the rate-limit budget: a registered api.data.gov key allows 1,000 calls/hour and the FEC will grant 7,200/hour on request to APIinfo@fec.gov. Worth requesting the higher tier in August.*

> ### **LAB 4 — due Fri 9/25**
> **Part A:** A paginated, rate-limited API. Retrieve the complete result set with backoff and on-disk caching, then prove idempotency: run twice, get byte-identical output.
> **Part B:** A paginated, cached, rate-limit-respecting client for your primary source. *Rationale:* estimate full-pull volume and wall-clock runtime, and say how you'll avoid re-pulling during development.

### **Tue 9/29 — Session 11: Web scraping with BeautifulSoup**

> **Dashboard build.** Bill *text* isn't fully exposed the way the metadata is, so we scrape `congress.gov/bill-texts-received-today`. We read `robots.txt` and the terms of use first, on screen, as a step in the workflow rather than a disclaimer. Then BeautifulSoup the results table for bill numbers and text links, with a polite delay between requests. The framing question: scrape only what the API won't give you, because the scraper is the most fragile thing we will build all semester.

### **Thu 10/1 — Session 12: Entity resolution I — identifier crosswalks**

> **Dashboard build.** We now hold four identifier systems for the same 535 people: bioguide IDs from Congress.gov, ICPSR numbers from Voteview, FEC candidate IDs from openFEC, and CIDs from OpenSecrets. We try the naive thing — merge Congress API members onto Voteview on name — and look at the match rate together. It's bad, and instructively so: `Michael "Mike" Kelly`, `Robert C. "Bobby" Scott`, suffixes, hyphenated names.
>
> Then we go find the crosswalk instead of building it. Voteview ships a `bioguide_id` column, and the `unitedstates/congress-legislators` YAML carries bioguide, ICPSR, FEC, and OpenSecrets IDs in one file. **The FEC entry is a list, not a scalar** — members accumulate multiple candidate IDs across cycles and across chambers — so the crosswalk is one-to-many the moment campaign finance enters, and every downstream contribution total depends on getting that fan-out right. We build `member_crosswalk` with a row per (bioguide_id, fec_candidate_id) pair and establish that no such bridge exists at all to names in news text, which sets up Thursday.

> **LAB 5 issued today.** The crosswalk half is buildable over the break; fuzzy matching arrives 10/8.

### ~~Tue 10/6~~ — **FALL BREAK, NO CLASS**

### **Thu 10/8 — Session 13: Entity resolution II — fuzzy matching and provenance**

**The highest-value addition in this revision** — it's where a multi-source project actually lives, and it transfers to every dataset these students will ever merge.

> **Dashboard build.** NewsAPI returns articles mentioning "Sen. Warner," "Mark Warner," and "Warner (D-Va.)" with no identifier of any kind. We block candidates by state and chamber, score with `rapidfuzz`, and tune a threshold while watching precision and recall move in opposite directions. Then the cases that break naive matching: two sitting Udalls, a Representative and a Senator sharing a surname, and a member whose married name appears in older coverage. We store `match_confidence` and `match_method` on every row and write ambiguous pairs to a review queue rather than guessing — the dashboard will later show a "low confidence" flag on those articles, which is what honest provenance looks like in a public product.

### **Tue 10/13 — Session 14: Filtering, recoding, aggregation**

> **Dashboard build.** From the full raw member pull: filter to the current Congress, recode `partyName` into a clean D/R/I code, collapse the minor and independent labels, derive `chamber` from whether `district` is null, and compute `tenure_years` by aggregating each member's nested terms. Then the FEC side: aggregate Schedule E rows to expenditure totals per member per cycle, split by the support/oppose indicator, which is the first time the dashboard's outside-spending panel has numbers behind it.

### **Thu 10/15 — Session 15: Merging and reshaping**

> **Dashboard build.** With the crosswalk in hand, the joins finally behave: members to NOMINATE scores one-to-one, members to sponsored bills one-to-many, bills to cosponsors many-to-many resolved into a bridge table, and members to FEC candidate IDs one-to-many — where I deliberately do the join wrong first, double-count a member with two active candidate IDs, and let the inflated total sit on screen until someone catches it. We also pivot roll-call votes from long to wide, look at the 535-column result, and agree it's a fine analysis shape and a terrible storage shape.

> ### **LAB 5 (double-width) — due Fri 10/16**
> Entity resolution doesn't compress into seven days, and a half-taught version is worse than none.
> **Part A:** Scrape a stable instructor-chosen page, then reconcile two provided identifier lists — deliberately dirty, with nicknames, suffixes, married names, and three genuine non-matches. Report match rate, enumerate unmatched records, demonstrate a false-positive check.
> **Part B:** Define join keys across your own sources; build a crosswalk table carrying confidence and provenance columns. *Rationale:* state your matching rule and threshold and defend the threshold; show five edge cases you resolved by hand.

---

## Part 3 — Wrangling and Databases

### **Tue 10/20 — Session 16: Normal forms, schema design, and documentation**

ER diagrams and dbdocs.io folded in as the deliverable of the design exercise rather than a standalone session.

> **Dashboard build.** I put up a deliberately awful single table: one row per member, committees as a comma-separated string, `party_name` and `state_name` repeated on every row, sponsored bill numbers in a list column, and a single `total_outside_spending` field that quietly conflates support and oppose. We break it to 3NF as a class — `members`, `states`, `parties`, `committees`, `committee_memberships`, `bills`, `sponsorships`, `cosponsorships`, `rollcalls`, `votes`, plus `fec_committees` and `independent_expenditures` — arguing over each decomposition. The campaign finance side forces the sharpest argument, because an expenditure's grain is (spender, target, date, support/oppose) and nothing about that fits on a member row. We publish the ER diagram to dbdocs and it becomes the reference document for the rest of the semester.

> **LAB 6 issued today.**

### **Thu 10/22 — Session 17: SQLite, PostgreSQL, and DuckDB in Python**

> **Dashboard build.** We create the schema in Postgres through SQLAlchemy and load it — and the load fails on a foreign key violation, because a sponsorship points at a bioguide ID that isn't in `members`. That's an entity resolution defect surfacing as a constraint error two weeks later, which is exactly the argument for constraints. Then DuckDB queries the Voteview and FEC Parquet files in place, no server and no load step, and we time an aggregation against the same query in Postgres.

### **Tue 10/27 — Session 18: SQL**

> **Dashboard build.** We write the queries the dashboard will actually call. Bills sponsored per member. A self-join on `cosponsorships` for a member's top ten cosponsorship partners. Party-line voting percentage using window functions. The committee roster. And the one this unit has been building toward: **the gray-money query.** Super PACs disclose their donors on Schedule A, so we self-join receipts against committees to find super PACs whose own largest donors are 501(c)(4) organizations — money that is disclosed one layer and dark the next. Each query gets saved to `sql/` as a named file; by December these are the callback bodies.

### **Thu 10/29 — Session 19: MongoDB and the document store**

> **Dashboard build.** Bill full text doesn't fit what we just built: variable length, nested sections and subsections, amendments, and no sensible column width. We load bill documents into MongoDB keyed by bill ID with metadata alongside, and answer the honest question of why we're running two databases — because a document store is right for the text and wrong for the roll-call votes, and knowing which is which is the skill.

> ### **LAB 6 (double-width) — due Fri 10/30**
> Schema design needs to be taught, attempted, critiqued, and revised; one week only buys the attempt.
> **Part A:** A denormalized instructor table. Normalize to 3NF, write the DDL, load it, answer five SQL questions of increasing difficulty against the result.
> **Part B:** Design your project schema, publish an ER diagram via dbdocs, load cleaned data into Postgres or DuckDB. *Rationale:* identify one place you deliberately denormalized and justify the tradeoff.

### ~~Tue 11/3~~ — **ELECTION DAY, NO CLASS**

### **Thu 11/5 — Session 20: Search and keyword extraction**

TF-IDF relocated here from the analysis unit so that ranking motivates the math instead of arriving three weeks later as an orphan topic.

> **Dashboard build.** We build a text index over bill documents and search "climate" across the corpus, then rank results rather than just returning them. Then TF-IDF over each member's sponsored bills to extract characteristic terms — the dashboard's "what does this member actually work on" panel. The first run returns `be it enacted`, `short title`, and `fiscal year` for everyone, which is the best possible motivation for stopword lists and domain-specific boilerplate removal.

> ### **LAB 7 — issued 11/5, due Fri 11/13**
> **Part A:** Load a provided text corpus into MongoDB, build a query interface, extract top TF-IDF terms per document group.
> **Part B:** Apply the same to your own corpus — *or*, if your project has no meaningful text, justify that in a paragraph and instead build a second analytical view in your relational store. *Rationale:* either way, what would a user search for, and does your storage choice support it?

### **Tue 11/10 — Session 21: Incremental loading and orchestration**

**This is the session that most distinguishes data engineering from data science with a database.** If anything gets deferred to DE2, defer the hands-on orchestration tooling, not the concepts.

> **Dashboard build.** Two update cadences, one pipeline. Bills change slowly enough that a watermark on `updateDate` handles them. Independent expenditures do not: **groups must report them within 24 or 48 hours**, filings get amended, and amendments supersede earlier rows — so an append-only load double-counts spending, and re-pulling everything nightly is infeasible at FEC volume. We write upserts with `ON CONFLICT DO UPDATE` keyed on filing identifiers, handle the supersede flag, run the load twice, and confirm row counts don't move. Then we sketch a Prefect flow with two schedules — nightly for bills, hourly for filings during an election period — because a transparency dashboard frozen at October isn't one.

### **Thu 11/12 — Session 22: Testing, data validation, and reviewing generated code**

XKCD #2054 is already printed in the syllabus; this is the session that answers it. Also the natural home for agent code review: **you can only safely delegate what you can verify.**

> **Dashboard build.** We write a Pandera schema for `members` — bioguide ID unique and non-null, state within the valid set including DC and territories, NOMINATE dimension one bounded to [-1, 1], party in the allowed enum — plus a referential integrity check from sponsorships back to members, a row-count delta alarm on the daily load, and a sum check on independent expenditures against the FEC's own published committee totals, which is the only external ground truth we have all semester. One check fails immediately on real data, which is the best outcome available. Then we look at agent-generated code for the same daily pull and find what the tests would have caught.

> ### **LAB 8 — issued 11/12, due Fri 11/20**
> **Part A, two pieces.** (i) Given a table and an incoming batch containing overlaps, updates, and one deletion, write an idempotent upsert. (ii) **Agent code review:** you're handed agent-generated pipeline code with four seeded defects — a silent dtype coercion, a non-idempotent insert, an unbounded pagination loop, a hardcoded credential. Find them, explain each failure mode, and write the validation check that would have caught it. *Graded on the review, not on rewriting the code.*
> **Part B:** Make your own load idempotent, add four validation checks, wire at least one into GitHub Actions. *Rationale:* what silently wrong output is each check protecting against?

---

## Part 4 — Analysis and Delivery

### **Tue 11/17 — Session 23: Exploratory analysis and static visualization**

*(These were separate sessions in the earlier draft; merging them is what makes room for the optional day on 11/24.)*

> **Dashboard build.** Cross-tab of chamber by party. The distribution of bills sponsored, severely right-skewed, and a live argument about which of mean and median belongs on a public dashboard. Then the analysis this unit was rebuilt for: **outside spending per member, split by support versus oppose and by donor-disclosure status.** The first thing students see is that the distribution is wildly concentrated — a handful of members in competitive races absorb most of it — and the second is that "opposed by $4M" is a very different fact from "supported by $4M" and cannot share an axis. We build the figure with a diverging scale, labeled axes, a source note, and a colorblind-safe palette, because this one is going in front of the general public.

### **Thu 11/19 — Session 24: Interactive visualization with plotly**

> **Dashboard build.** The NOMINATE scatter becomes interactive: hover reveals name, state, and party; color encodes party; selection filters the member list. Then the outside-spending panel: a diverging bar of support versus oppose per member, with hover showing the top spending committees and their disclosure status. The design constraint is stated explicitly — our audience is citizens, not political scientists, so every hover label has to make sense without a methods section, and "501(c)(4)" is not a label a general reader can parse.

> ### **LAB 9 (double-width) — issued 11/19, due Fri 12/4**
> Spans Thanksgiving deliberately: the break is the work time, and this replaces the old third project check-in, so nobody discovers on the final weekend that their dashboard doesn't run.
> **Part A:** Convert two provided static figures to interactive plotly, then build a two-callback Dash app against a provided database.
> **Part B:** Three publication-quality figures from your own data, plus a working local dashboard driven by your own database with at least one interactive control. *Rationale:* what claim is each figure making and for whom — and what's still broken, with your plan for the final week?

### **Tue 11/24 — OPTIONAL: Dashboard work session and design clinic**

No new material, nothing assessed, attendance entirely optional. Instructor in the room and on Zoom for drop-in help with Lab 9. Students who need to travel lose nothing.

> **Dashboard build.** Open work time on students' own dashboards. For anyone who attends, I'll take requests against the Congress dashboard — usually the callback that won't fire or the query that takes nine seconds. Announce this in August so travel can be booked early, and confirm it in the 11/19 class.

### ~~Thu 11/26~~ — **THANKSGIVING, NO CLASS**

### **Tue 12/1 — Session 25: Building the dashboard with Dash — properly**

Students have had a working (bad) Dash app since 8/27 and a better one from Lab 9, so this is refinement rather than first contact.

> **Dashboard build.** The real thing: a dropdown of all 535 members driving a multi-output callback that fills the biography card with photo, party, state, and tenure; the committee list; voting statistics; the sponsored bill table; the TF-IDF keyword panel; the outside-spending panel with its disclosure-status breakdown; and the linked news articles with their confidence flags. We profile it, find that switching members takes several seconds, and fix it with query caching and an index — the last engineering lesson of the semester and not a small one.

### **Thu 12/3 — Session 26: Deployment, publication, and the ethics of the artifact**

> **Dashboard build.** We push the dashboard image, deploy against a managed Postgres, set environment variables in the host's secret store rather than a file, and connect through a read-only database user. Then the URL goes on the screen and anyone in the world can open it, which is the moment the second half of the session earns.
>
> **Two ethical questions, not one.** First, the contributor panel lists individuals by name, employer, and ZIP code — every byte lawful FEC public record, but aggregation and searchability change what the record *is*. Second, and newer: **our dark-money panel publishes our own classification.** The FEC tells us a committee spent $2M opposing a member; it does not tell us whether that committee discloses its donors. We inferred that from committee type, filing pattern, and the Schedule A chain, and reasonable analysts would draw the line differently. So the dashboard is publishing a contestable judgment under a factual-looking label. We decide as a class what that panel actually says, how the methodology is disclosed to a reader who won't click through, and whether "dark" is a word a neutral transparency tool should use at all. This is course objective 4, and it lands harder because they built the thing.

> **Settle hosting before the semester starts.** "Publish online" needs a specific target — Render, Fly.io, Heroku, or a school-provisioned VM — with a plan for the database and for what happens to student deployments in January.

### **Tue 12/8 — Session 27: Project presentations**

> **Dashboard build.** None — the Congress dashboard is finished and deployed. Students present their own pipelines, and the comparison across topics is the point: fifteen different domains, the same eleven techniques.

---

## Final Deliverable — Data Pipeline Project (180 points)

**Due Sunday 12/13, 11:59pm.**

Because nine rounds of Part B have already produced the pipeline, this is **integration and writing**, not construction.

| Component | Points |
|---|---|
| Deployed dashboard, running and reachable | 60 |
| Reproducibility — repo, container, documented setup, passing validation checks | 40 |
| Short article formatted for a data-descriptor journal | 60 |
| Gen AI use statement and agent-commit provenance | 20 |

---

## Revised grading bands

The bands on page 5 of the current syllabus leave 255–305 and 210–251 under no rule at all, and the stated percentages don't match the stated point values. Rewritten against 360 using your own percentages:

- **A** — 306 or above (85%)
- **A−** — 252 to 305 (70–84.9%)
- **B+** — 180 to 251 (50–69.9%)
- **F** — below 180
- **A+** — an A plus an exceptional final project

---

## Notes on this draft

**"Dark money received" is a category error, and correcting it is a feature.** Members do not receive dark money in any meaningful volume — a 501(c)(4) cannot contribute to a federal candidate at scale. It spends *independently*, on ads supporting or opposing them. The measurable quantity is outside spending **about** a member, on FEC Schedule E, with a support/oppose indicator. A panel labeled "dark money received by Rep. X" would be wrong, and a transparency project can't afford to be wrong on its own definitions. Make students say the correct sentence out loud on 10/13 before they draw the figure on 11/17.

**Where the data stops is part of the lesson.** The FEC discloses the spending; the 501(c)(4) behind it files a Form 990 whose Schedule B donor list is redacted in every public copy. The chain terminates there by law. Students should be able to point at the exact record where the public trail ends — that's a more valuable outcome than any dataset that pretends it doesn't.

**The dashboard accumulates rather than restarts.** Every session's build depends on the previous one, which means a missed class leaves a hole. Keep the Zoom recordings, and keep a tagged commit per session on the course repo so a student who misses 10/22 can check out `session-17` and rejoin at 10/27 without reconstructing three weeks.

**Four deliberate failures are scheduled.** The hardcoded API key on 8/25 (fixed 9/15); the Compose connection refused on 9/10; the double-counted FEC join on 10/15; and the foreign key violation on 10/22 that traces back to an entity resolution defect from 10/8. These are the highest-value minutes in the course and they only work if you resist fixing them in advance.

**Part A is never Congress data.** In-class work is Congress; Part A is a different domain entirely. That's what tests whether the technique transferred or whether they just memorized our repo.

**Nine labs, not ten.** The Thanksgiving compression left only three sessions between 11/19 and 12/8. Rather than cram two labs into that window, visualization and the dashboard checkpoint merged into one double-width Lab 9 — which lands at 20 points each and still totals exactly 180.

**Fall break falls mid-topic, and that's usable.** Lab 5 is issued 10/1 after crosswalks and due 10/16 after fuzzy matching, so the break becomes build time on the half students can already do.

**No catch-up days remain.** Folding in entity resolution, orchestration, and testing consumed both. If you want one back, merge Sessions 24 and 25 — plotly and Dash are closely related and students will have built both in Lab 9 anyway.

**Still to verify before August.** NewsAPI's free tier restricts production use, which a genuinely public dashboard arguably is — it carries the fuzzy matching session on 10/8, so confirm it or find a substitute. Request the 7,200/hour FEC key tier from APIinfo@fec.gov. Start OpenSecrets bulk registration immediately; assume it may not clear in time and confirm the dashboard degrades gracefully without industry codes. Note also that the current syllabus names Windsurf as a Copilot-style autocomplete tool; that product is now Devin Desktop, and the category has moved from completion to delegation.
