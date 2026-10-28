# Write-up

## 1. AI vs rules

**AI is used for:** reading messy proposal text and pulling out facts (price, weeks, warranty years, licence, team details) with a quote as proof, and writing the one-line reason at the end.

**Plain code and rules are used for:** PDF to text, scanning for manipulation attempts, pass/fail mandatory checks, the disqualification rule, all the maths, weights, sorting, ranking, and flagging.

I drew the line there because AI is good with language but should not do the maths or decide eligibility. Code gives the same answer every time and anyone can re-check it.

## 2. Consistency and explainability

- The AI only extracts facts into a fixed JSON format at temperature 0 with a versioned prompt. Scoring is a fixed formula in code.
- Extracted facts are saved against the file hash. Ranking the same proposals twice reuses the saved facts and gives an identical result. A fresh extraction could vary slightly, so it only runs if the reviewer asks for it.
- The reviewer sees a table per proposal: extracted fact, quote from the proposal, points, weight, and total. The reason sentence only repeats those numbers.

## 3. Human in the loop

- After extraction, the reviewer can see and correct facts next to their quotes.
- The tool can mark a proposal ineligible (failed mandatory check or manipulation attempt), but a reviewer must **confirm every disqualification** before it is final. The quoted evidence is shown to them.
- Flagged proposals (missing or unclear info) cannot be ranked until a person decides.
- The ranking is advice only. A person makes the award and can override any position, but must write a reason.
- **Logged:** who, when, file hash, model and prompt version, criteria version, extracted JSON, scores, flags, disqualifications and who confirmed them, overrides with reasons, and the final decision.

## 4. Changing criteria

Criteria are versioned. An edit creates a new version, and old scores stay labelled with the version they used. Weights must total 100%. Because extracted facts are stored separately from scoring, re-scoring under a new version only re-runs the maths, so there is no new AI cost. If a new criterion needs a fact that was never extracted, only that fact is extracted. The tool refuses to compare proposals scored under different versions and tells the reviewer.

## 5. Risks

- **Wrong extraction:** a quote is required for every fact, the reviewer checks it, and code checks that values are sane (price vs budget ceiling, weeks vs the 16-week limit).
- **Made-up facts:** "null if not stated" rule, and code rejects any value without a matching quote.
- **Bias** (favouring polished writing or big firms): the AI never scores, only the fixed rubric does, and company names are not used in scoring.
- **Manipulation / prompt injection:** proposal text is treated as data and the AI has no power over scores. If a proposal contains text aimed at manipulating the review, code marks it ineligible (disqualified) and does not score it. Because a scan can give a false positive, a human confirms before it is final.
- **Cost:** extract once per proposal and cache it, re-scoring needs no AI, and a small model is enough for extraction.
- **Missing or vague info:** flagged instead of guessed.
- **Bad PDF reading:** if text conversion fails or the result looks too short, the proposal is flagged.

## How the sample set came out

| Proposal | Result | Why |
|---|---|---|
| A | Rank 1 (98.0) | Passes all checks, strongest on experience, timeline and team |
| E | Rank 2 (91.7) | Passes all checks, best warranty and monitoring |
| D | Flagged | No timeline given, so it cannot be fully scored |
| C | Ineligible (disqualified) | Hidden text trying to manipulate the automated review; not scored; reviewer confirms |
| B | Ineligible | Bidder does not hold the electrical licence itself |

Scoring formulas (my own simple choice, since the brief gave none): experience by number of qualifying installs; price and timeline relative to the best bid that is not ineligible; warranty as half panel and half workmanship; team as 25 points each for named lead, crew, safety plan, monitoring plan. A stays first and E second even if the flagged bid is left out of the price baseline.

## Assumptions

- Flagged proposals get no rank, so a human decides.
- A manipulation attempt means disqualification, but only after a human confirms it.
- "Since 2020/2021" install counts may include installs older than 5 years, so the reviewer verifies them.
