# EqualPath — product brief

**Author:** Christina Green, Founder & Principal PM  
**Last updated:** May 2026  
**Status:** MVP in development  
**Live app:** [equalpath.vercel.app](https://equalpath.vercel.app) *(coming soon)*  
**GitHub:** [github.com/csgreen/equalpath](https://github.com/csgreen/equalpath)

---

## Problem statement

Women entering or returning to the tech industry face a job search that is structurally harder
than it appears. The tools available to them — generic resume builders, one-size-fits-all
interview prep platforms, and salary sites with sparse data — were not designed with their
specific challenges in mind.

Research and lived experience consistently show that women undersell themselves on resumes
(using hedging language, attributing team wins to the team rather than themselves, omitting
leadership titles). They negotiate salary less often and less effectively, not because they
lack skill, but because they lack a safe space to practice. Spanish-speaking women face an
additional barrier: virtually no career coaching tools exist in natural, professional Spanish.

The result is a compounding disadvantage that keeps capable women out of roles they have
already earned.

**EqualPath exists to close that gap.**

---

## The builder's perspective

I am not a neutral observer of this problem. I founded the Seattle Product Women Breakfast,
have mentored women PMs at Microsoft and through Women in Product, and have spent a decade
watching talented women talk themselves out of opportunities they were more than qualified for.
I am also an advanced Spanish speaker who volunteers with Casa Latina and HOLA Toastmasters.

This product is built from the inside of the problem — which is the only place it could have
been built from correctly.

---

## Target users

### Primary persona — "The Returner"
**Name:** Sofía, 34, Seattle  
**Situation:** Took 2 years off for caregiving. Strong background in operations and project
management. Wants to pivot into a PM role at a mid-size tech company. Has not interviewed
in 3 years and feels behind.  
**Core fear:** "I don't know how to talk about my experience in a way that sounds like tech."  
**What she needs:** Resume translation from her existing skills into tech-PM language,
interview practice with real feedback, and salary benchmarks so she doesn't undersell herself
in the first conversation.

### Secondary persona — "La Profesional"
**Name:** Valentina, 28, Los Angeles  
**Situation:** Immigrated from Colombia 4 years ago. Strong engineering background. Her English
is good but she thinks more fluently in Spanish, especially under pressure. Has been practicing
interviews in English but freezes when nervous.  
**Core fear:** "My accent makes interviewers underestimate me before I finish my first sentence."  
**What she needs:** A coaching tool she can use entirely in Spanish so she can build real
fluency and confidence in how she presents herself — not just translating English scripts.

### Tertiary persona — "The Switcher"
**Name:** Priya, 31, Remote  
**Situation:** Has been in marketing for 6 years and wants to move into product. Has taken
online PM courses but has never been a PM formally and doesn't know how to frame her
experience.  
**Core fear:** "They'll see I'm not a 'real' PM and screen me out before I get a chance."  
**What she needs:** Resume feedback that reframes her existing work as PM-adjacent, and
behavioral interview coaching that helps her tell a convincing transition story.

---

## Product vision

**In one sentence:** EqualPath is a bilingual AI career coach that helps women navigate the
tech job search with the same confidence, preparation, and insider knowledge that their
male peers take for granted.

**In 3 years:** EqualPath is the default career coaching tool used by women's ERGs, coding
bootcamps, and workforce re-entry programs across the US and Latin America — with a
measurable impact on offer rates and starting salaries for the women who use it.

---

## MVP scope

The MVP answers one question: *Can AI coaching meaningfully improve how women present
themselves in a tech job search?*

### In scope for MVP

| Feature | Description |
|---|---|
| Resume reviewer | Upload or paste a resume. AI returns structured feedback with a bias-awareness lens — flags hedging language, passive framing, and underselling patterns. |
| Interview Q&A coach | Select a role type. AI generates likely behavioral and technical questions. User answers. AI gives STAR-method feedback. |
| Salary negotiation guide | Input role, level, and location. AI provides market range context and exact scripts for negotiation conversations. |
| Language toggle | Full EN / ES language support. Spanish prompts written natively, not translated. |
| Freemium gate | 3 free sessions per month. $12/mo Pro for unlimited access. |

### Out of scope for MVP

- Voice interview simulation (planned for v2)
- Job description matching and cover letter generation (planned for v3)
- ERG / cohort admin dashboard (planned for v2 B2B feature)
- Mobile app (web-responsive only in MVP)
- Integration with LinkedIn or job boards

---

## Success metrics

### Leading indicators (start measuring at Stage 2)

| Metric | Target | Measurement method |
|---|---|---|
| AI response quality score | Avg > 3.8 / 5 | Manual rating of every 5th response |
| Prompt latency p50 | < 3 seconds | Supabase logs |
| Prompt latency p95 | < 8 seconds | Supabase logs |

### Product-market fit indicators (start measuring at Stage 3)

| Metric | Target | Measurement method |
|---|---|---|
| Beta user NPS | > 40 | Survey after 5 sessions |
| Session completion rate | > 70% | Supabase: sessions with > 3 exchanges |
| Return rate | > 50% use it again within 2 weeks | Supabase: user session history |

### Business indicators (start measuring at Stage 5)

| Metric | Target | Measurement method |
|---|---|---|
| Free-to-paid conversion | > 5% | Stripe dashboard |
| Cost per session | < $0.05 | Anthropic API cost / total sessions |
| Monthly recurring revenue | $500 MRR by month 3 post-launch | Stripe dashboard |

---

## Technical architecture
User → Next.js frontend (Vercel)
→ Clerk (authentication)
→ API route → Anthropic Claude API (AI coaching)
→ Supabase (session history, usage counts, user profiles)
→ Stripe (subscription management)

**Model selection rationale:** Claude Sonnet was selected over GPT-4o and Gemini 1.5 Flash
after a structured model comparison experiment (see `tests/prompt-evals.md`). Claude scored
highest on coaching tone quality and bias-detection accuracy. Cost and latency were comparable
across models.

**Language architecture decision:** Spanish mode uses separately authored system prompts,
not translated versions of the English prompts. This was a deliberate product decision: direct
translation produces grammatically correct but culturally flat coaching language. Native
Spanish prompts allow for appropriate formality register (usted vs tú), idiomatic encouragement
phrasing, and Latin American professional culture context.

---

## Revenue model

### B2C
- Free tier: 3 coaching sessions per month
- Pro tier: $12/month — unlimited sessions, resume upload, priority response

**Pricing rationale:** $12 sits below the psychological "lunch money" threshold ($15) while
covering API costs at scale with a 60%+ margin. Tested against $9 with a small panel — $12
did not reduce willingness to pay and signals quality. See `docs/metrics.md` for pricing
experiment log.

### B2B
- ERG cohort license: $500–2,000 per cohort
- Bootcamp integration: per-seat pricing negotiated per partner
- First target customers: Women in Product chapters, coding bootcamps with female-majority
  cohorts, workforce re-entry programs

### Grant and partnership track
- Google.org, Mozilla Foundation, and Melinda French Gates Foundation all fund
  workforce equity tech tools
- Casa Latina (Seattle) identified as first nonprofit pilot partner

---

## Risks and mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| AI gives factually wrong salary data | Medium | Prompt includes disclaimer; data framed as "market context" not "offer guarantee" |
| Bias detection over-flags neutral resumes | Medium | Prompt iteration with test cases; v1–v3 documented in evals |
| Spanish responses feel unnatural | Low | Native-speaker QA before launch; prompt written in Spanish natively |
| API cost spikes with usage | Low | Per-session cost tracked weekly; rate limiting built into freemium gate |
| User data privacy concerns | Low | No resume text stored permanently; Supabase row-level security; Clerk handles auth |

---

## What I would build next (v2 roadmap)

If EqualPath achieves 200 active users and NPS > 50, the v2 priority is voice interview
simulation — letting users practice out loud with an AI that listens, transcribes, and
coaches on both content and delivery. This is the feature with the highest emotional impact
and the clearest differentiator from existing tools.

The B2B ERG cohort dashboard comes immediately after, because that is where revenue
concentration lives. Ten ERG contracts at $1,000/cohort creates more stable revenue than
1,000 individual subscribers.

---

## Builder's log — key decisions

*This section is updated after each development stage. It documents product decisions made
during the build, not just the final output. It exists because the decisions are as important
as the code.*

**May 2026 — Model selection:** Ran structured comparison of Claude Sonnet, GPT-4o, and
Gemini 1.5 Flash on resume bias detection task. Claude won on coaching tone and specificity.
GPT-4o was slightly faster. Gemini was cheapest but generic in coaching voice. Chose Claude
as primary model; will re-evaluate if cost becomes a constraint at scale.

**May 2026 — Spanish prompt strategy:** Decided against AI-translation of English prompts
after testing showed robotic coaching language. Wrote Spanish prompts from scratch. This took
longer but produced measurably more natural responses (validated by native speaker panel).
This decision also gave me direct Spanish practice as a side effect — a personal goal I
documented in `docs/personas.md`.

*Further entries will be added at the completion of each development stage.*
