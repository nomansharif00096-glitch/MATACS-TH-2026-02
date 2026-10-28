# MATACS-TH-2026-02: AI Proposal Triage Design

**Name:** [Noman Sharif Chandio]
**Reference:** MATACS-TH-2026-02
**Time actually spent:** [about 2.5 hours]

## How I used AI

I used Claude to help me understand the brief and to draft a first version of the flow, the prompts and the write-up.

**Where I disagreed with the AI:** Claude's first design only *flagged* Proposal C, the one with hidden text telling automated reviewers to rank it first, and left the decision open. I disagreed. Hidden instructions aimed at manipulating the review are an attempt to cheat, so I changed the design to mark that proposal **ineligible (disqualified)** and not score it. I kept one safeguard: a human reviewer has to confirm the disqualification, because an automatic scan could sometimes match innocent text, and disqualifying a bidder is a serious step. I then updated the diagram, `ranking.json`, `WRITEUP.md` and `PROMPTS.md` so they all match this decision. Missing information, like Proposal D's timeline, stays *flagged*, because that is a gap to clarify, not cheating.

## Files

- [WRITEUP.md](WRITEUP.md): answers to the five questions
- [PROMPTS.md](PROMPTS.md): extraction and reason prompts
- [ranking.json](ranking.json): expected ranking of proposals A to E
- [diagram/flow.png](diagram/flow.png): process flow from upload to ranked output
