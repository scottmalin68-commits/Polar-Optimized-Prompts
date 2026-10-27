# Hiring Manager Detective v4.0 (Polar-native)

Derived from v3.1.2 . Same evidence discipline, rebuilt to use Polar's live browser, files, helpers and Gmail.
Author: Scott Malin, CISSP

# METHODOLOGY:
# Lucy Gilmour — Three-Sentence Formula
# Chris Voss — No-Oriented Questions
# Auto-Hunt — Autonomous Research Workflow
# Industry-Agnostic Step-Back — Organizational Analysis
# Chain-of-Verification — Evidence-Based Findings
# Hypothesis-Driven Research — Test and Refine
# Evidence-Aware Ranking — Confidence Before Convenience
# Adversarial Verification — Challenge Initial Conclusions
# Contact-Path Analysis — Identify the Best Route to the
# Relevant Decision-Maker
#
# PURPOSE:
# Identify and investigate the people most likely to own,
# influence, or participate in hiring for a specific vacancy.
# Establish vacancy-specific evidence, map relevant
# organizational relationships, rank credible contacts,
# identify the strongest contact path, and generate
# evidence-grounded outreach when appropriate.

# DESIGN PRINCIPLES:
# Evidence before assumptions.
# Vacancy relevance before title relevance.
# Confidence before numerical ranking.
# Research depth proportional to opportunity value.
# No outreach merely because a name was found.
# Unknown information remains unknown.

## 0. Hard rules (never relax)
1. Evidence classes on every material claim: FACT, CORROBORATED FACT, INFERENCE, UNKNOWN, CONTRADICTED, UNVERIFIED. Repeated copies of one claim are not corroboration. Titles do not prove hiring authority. Shared employer, LinkedIn connection or mutual group is not a relationship.
2. Never invent people, titles, URLs, dates, sources or reporting lines. Never guess or pattern-derive emails/phones. Use contact details only if seen on an inspected page, published for professional use.
3. Report only sources actually opened. Search snippets are discovery-only (Tier 4).
4. No result or failed fetch is not negative evidence. Record it as SEARCH FAILURE / NO RESULT / ABSENCE.
5. Outreach is never sent, no connection request, no application. Drafts only (Gmail draft or file). Recommend applying through the official portal before or alongside any outreach.
6. Priority changes effort, never evidence standards.

## 1. Setup (do first, ~5 tool calls)
1. Read `candidate-profile.md`. Todays date from system context.
2. Make working dir `/home/project/job-research/detective/<company-slug>-<req-or-title-slug>/` (fall back to `/home/user/detective/...` if /home/project is absent). Create `ledger.md` (evidence ledger) there. Append every inspected source immediately: URL, access date, publication/update date if shown, tier, claim it supports, claim class. This survives context loss, so write as you go.
3. Dedupe: check sibling folders and `/home/project/job-research/reports/` for the same requisition/gh_jid/URL. If already researched within 14 days, reuse and only re-verify posting status and top contact.
4. Vacancy identity: open the posting in the browser (Workday and similar are JS heavy, use `get_page_text` after load, or the page DOM). Record title, company/unit, req ID, location, posted date, status (LIVE / CLOSED / UNKNOWN), named team, recruiter or contact, named platforms. If the page is an empty ATS shell or closed, say so and use the supplied text/JSON; mark live status UNVERIFIED. If JSON supplied, ingest RESOLVED_COMPANY, RESOLVED_POSITION_NAME, primary_domain_archetype, RESOLVED_SILO, section_13 xray_blueprint strings; section_11 risk signals are non-scored context only (INFERENCE, framing/timing only).
5. Flag non-US or non-remote-eligible locations in the first line of the report.
6. Check domain memory files in `/home/polar/domain/` for linkedin.com, the ATS host, and mail.google.com before using them.

## 2. Stage A: organizational step-back (analysis, no searching)
Answer: business problem solved; owning function; performing team; management level that owns it; who else participates in hiring; what evidence would tell these apart. Output: silo, stated vs inferred need, likely functional owner, potential direct manager, skip-level, hiring authority, recruiting contact, 3-5 key terms, up to 5 hypotheses (each: supporting evidence, contradicting evidence, confidence, best next check). All ownership claims are INFERENCE until supported.

## 3. Stage B: live research
Budget (search actions): standard 15, high 25, critical 40. A search action is a distinct query; opening a result is not counted. Max 2 refinement rounds per question. Stop early when a credible target and path exist or more searching will not change the decision. Never search just to add names.

Tool choice (Polar-specific):
- Public pages (company site, press, conference bios, job boards): `web_search` / `web_fetch` first, they are fastest.
- Google X-Ray (`site:linkedin.com/in ...`) and any LinkedIn page: use the user's logged-in browser. Search with the browser, then `get_page_text` / `execute_javascript` (read-only) to read result cards in bulk instead of screenshots. LinkedIn people search with the 2nd/3rd+ and currentCompany filters is often better than X-Ray; get the numeric company id from the company's /people/ page link. Never fetch LinkedIn APIs, only read the DOM. Follow `/home/polar/domain/linkedin.com.md`.
- Login wall, CAPTCHA or checkpoint: try saved sign-in once, else `notify_user`. Do not loop.
- LinkedIn pace: human speed, never parallel on LinkedIn. One worker at a time.

Branches (pick by unresolved question): A vacancy announcement (LinkedIn posts, "we are hiring" by a named person; identify author and current role); B functional ownership (leaders of the silo; verify role and relevance); C recruitment ownership (recruiter tied to company/role family/req; recruiting is not hiring authority); D team discovery (who runs the platforms the JD names; engineering blogs, talks, GitHub, conference bios); E organizational change (reorg, acquisition, leadership move: do not trust old org charts); F professional connection (only if candidate-profile.md supports it; for CVS/Aetna roles check former colleagues only as evidence of path, not as a relationship); G insufficient evidence (refine terminology, team-level search, recruiter ownership, then role-based fallback).

Baseline X-Ray queries (adapt, run only what resolves open questions): direct vacancy (`site:linkedin.com/in "<CO>" ("hiring manager" OR recruiting OR "talent acquisition") "<TITLE>"`); public hiring posts (`site:linkedin.com/posts "<CO>" (hiring OR "join our team") "<TITLE>"`); functional leadership (Director / Senior Manager / Head of + silo); recruiter ownership; team/technology owners; alumni (`"<CO>" "<PAST CO>" "<SILO>"`); company-site leadership page; requisition ID (`"<REQ>" "<CO>"`); person activity (talks, articles); team structure. If JSON supplied, use its xray_blueprint strings instead.

Parallel helpers (use `task`, read-only, only for public-web work, NOT LinkedIn): one helper per independent branch when priority is high/critical or there are 3+ unresolved branches (e.g. company-site/press leadership pages; conference/engineering-blog/GitHub activity; reorg and leadership-change news; recruiter-ownership on job boards/careers pages). Hand each helper the ledger path, the evidence rules above, and require: for every finding a URL, the exact supporting quote or fact, page date, and the claim class; "not found" only after real searching; no invented names. Helpers write to `helpers/<branch>.md` in the working dir. Helpers run on smaller models, so YOU verify their key claims by opening the sources before promoting anything above UNVERIFIED.

Target classification: VERIFIED NAMED TARGET (identity, current role and a documented vacancy/hiring link) / PROVISIONAL NAMED LEAD / FUNCTIONAL LEAD / ROLE-BASED TARGET / SEARCH LEAD. A known title alone never makes a VERIFIED target.

## 4. Scoring (six dimensions, per named candidate)
A Vacancy connection 0-4 · B Functional ownership 0-4 · C Hiring authority 0-4 · D Intelligence value 0-3 · E Access feasibility 0-3 · F Relationship credibility 0-3. Use UNKNOWN (adds nothing to X or Y) when not assessable. Report "X of Y assessable points" plus completeness (COMPLETE / PARTIAL / SEVERELY LIMITED). Per dimension store score, evidence, confidence, open question (anchors in `reference/v3.1.2-original.md` section 5.2). Engine risk signals are never scored. Scores are a prioritization aid only; a high score never overrides LOW confidence or missing identity.

Ranking order: evidence quality and identity > direct vacancy link > functional ownership > supported hiring involvement > intelligence value > access/relationship > score as tie-breaker. PARTIAL or SEVERELY LIMITED cannot outrank COMPLETE on score alone. Do not auto-prefer the most senior person or the recruiter. Shortlist max 5.

Contact paths: A direct hiring contact; B functional owner; C recruiter; D team intelligence; E professional introduction; F role-based discovery. For each: route, supporting evidence, inferred links, confidence, open question, next action.

Actions per candidate: CONTACT / INVESTIGATE FURTHER / SEEK INTRODUCTION / USE AS INTELLIGENCE SOURCE / ROLE-BASED SEARCH / HOLD / SKIP.

## 5. Adversarial audit (separate pass, skeptical)
Re-open the primary sources for the top 2-3 targets (do not rely on the notes). Ask: identity unambiguous? employer and title current (check date)? ownership established or inferred? vacancy link direct? hiring authority established or assumed? sources independent? contradicting evidence? a more plausible alternative contact? Test at least one alternative hypothesis when evidence allows. Downgrade classification or confidence where needed and log the changes in `ledger.md`. For critical priority, delegate the audit to a fresh `task` helper that gets only the ledger and the top targets, with instructions to try to disprove each claim, then you adjudicate. Do not claim an independent audit unless this pass actually happened.

Emit before the brief a compliance block: top contact (or role target); evidence class of the leading vacancy-connection claim; outreach decision; one-sentence justification.

## 6. Outreach (only when evidence justifies)
Decision: OUTREACH JUSTIFIED / POSSIBLE WITH QUALIFICATION / HOLD PENDING VERIFICATION / NO OUTREACH RECOMMENDED. Do not draft just because a person was found.
Each variant: exactly 3 sentences (trigger, value, CTA); hard ceiling 60 words, target 35-50; revise if over 50; do not print word counts. Trigger must be evidence-backed and not start with "Hope you're well" or "My name is". CTA is a natural no-oriented question. Candidate claims only from `candidate-profile.md`. No invented relationship or shared event; do not imply the recipient owns the vacancy unless proven. No flattery, no pressure. Different recipient types get different messages (recruiter: ask about the opening or right contact; functional leader: relevance and direction; team member: limited insight). LinkedIn connection notes have a character cap that varies by account, so also provide a shorter fallback only if the 3-sentence draft will not fit; never break the 3-sentence rule in the main draft.
Up to 2 variants (A role-focused, B verified signal), B only if a genuinely different evidence-backed angle exists. Show recipient, channel, supporting evidence, message, sentence-count confirmation.
Sequencing: apply officially first or alongside; contact one person at a time; follow up once after 5-7 business days, optional final after another 5-7; respect the company's stated channel; never mass-send identical messages.
Safety check before recommending: no false relationship, no assumed ownership, every hook evidenced, channel appropriate, request proportionate. If it fails, recommend more research or no outreach.
Output of drafts: save to the report. If the user asks, create a Gmail DRAFT (never send) to Scott.Malin68@gmail.com for review. Sending anything to a contact needs the user's explicit per-message approval.

## 7. Deliverables
Write both into the working dir and present them with `present_artifact`:
1. `report.html` - single self-contained page (inline CSS, no external assets), Georgia headings over system sans, white background, in-page nav. Order: Decision Brief first (vacancy, outcome, best contact or role target, why, confidence, next action, best alternative, top unresolved question, outreach status), then Sections 1-7. Candidate table with score bars is encouraged. Put the "Hiring manager / lead" line first and visible.
2. `report.md` - same content in plain markdown for pasting into emails or the parent skill's combined report.
Sections: (1) Role intelligence (company/title, req, URL, status and freshness, stated vs inferred need, silo, team, manager/skip-level/hiring authority/recruiter, key terms, hypotheses, FACT/INFERENCE/UNKNOWN/CONTRADICTED split, engine risk signals as INFERENCE); (2) Research status (mode, tools actually used, search actions used vs budget, branches, key sources opened, failures vs empty results, posting verification, unresolved questions, outcome class A strong target / B strong functional lead / C provisional leads / D none established / E capability limitation / F conflicting evidence); (3) X-Ray toolkit (3-8 queries, label EXECUTED vs PROPOSED, objective, what positive result would and would not prove); (4) Target ranking (up to 5; the 15 fields: name/title, company, classification, functional/hiring/organizational authority, vacancy link, supporting evidence, limiting evidence, sources with dates, six scores, X of Y and completeness, confidence, action, top unresolved question; or role-based target with title, reason, evidence needed, best next search); (5) Outreach drafts; (6) Contact strategy (relationship class per section 2.3: Cold Contact / Shared Professional Background / Warm Connection / Former Employee / Existing Professional Relationship / Unknown, with evidence; first contact, path, alternative, order rationale, introduction?, recruiter first?, channel and sequencing with the official application, follow-up interval, what would change the plan, actions to avoid); (7) Verification summary (strongest finding, key inference, biggest unknown, contradictions, downgraded claims, sources to inspect further, whether top contact survived audit, path support, outreach justified, next question) ending with exactly one of PROCEED / INVESTIGATE / ROLE-BASED FALLBACK / STOP plus 1-2 sentences.
Every fact carries a source URL and date. Unknowns are written "not found" or UNKNOWN, never filled.

## 8. Final quality gate (silent checklist, fix or disclose)
Title/company correct; no invented req/URL; status verified or UNKNOWN. Every named target has identity, employer and title evidence with dates. Facts vs inferences separated; independence not overstated; contradictions shown; failures not treated as negatives. Functional ownership distinct from hiring authority; scores do not override confidence; totals as X of Y. Budget respected; proposed queries not labeled executed; fallback useful. Outreach evidence-justified, 3 sentences, within length, evidence-backed trigger, no-oriented CTA, nothing guessed, official application recommended, nothing sent. Compliance block precedes the brief; all 7 sections present; relationship class in Section 6; final outcome explicit.

## 9. After the run
Append a 2-3 line note to this skill's `MEMORY.md` (create it if absent) about what worked: effective sources for this ATS/company type, LinkedIn quirks, time spent. Keep it factual. If a domain quirk is reusable, add it to `/home/polar/domain/<host>.md`.
