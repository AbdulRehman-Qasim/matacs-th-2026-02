# AI Proposal Triage Design

## 1. AI vs. rules

I would use AI to extract facts from proposal PDFs because document layouts and wording vary. The model should return structured fields with evidence quotes and page references, and use `null` when a fact is missing or unclear. Ordinary application code should validate the schema, apply the mandatory pass/fail checks, calculate weighted scores, sort eligible proposals, and generate the final JSON. These operations are deterministic and should not depend on an AI model's judgment. The proposal text is untrusted input: instructions inside a document must never override the system prompt, administrator rubric, or application rules.

## 2. Consistency and explainability

Each scoring run uses a saved, versioned rubric with validated weights that total 100%. I would define explicit scoring bands for experience, price, timeline, warranty, and team/approach, then apply a fixed formula: `total = sum(weight × criterion_score / 5)`, where each criterion score is from 0 to 5. Price and timeline scores use documented bands; missing or ambiguous evidence is flagged rather than guessed. Store the source evidence, extracted values, rubric version, scoring-rule version, model/prompt version, criterion-level scores, and final score. With the same validated inputs and rubric version, the rule-based result should be identical. The reviewer can inspect the evidence and reason behind every score.

For a reproducible sample, I use these illustrative bands (the administrator would configure and approve them before a real evaluation):

- **Experience:** 5 = 12+ qualifying installations; 4 = 8–11; 3 = 5–7; 2 = 2–4; 1 = 1; 0 = none evidenced. The stated experience period must also meet the admin's five-year requirement; unclear recency is flagged.
- **Price:** 5 = CAD $200,000 or less; 4 = $200,001–$225,000; 3 = $225,001–$240,000; 2 = $240,001–$250,000; 1 = $250,001–$260,000; 0 = over budget. Price is still subject to the mandatory budget ceiling.
- **Timeline:** 5 = 10 weeks or less; 4 = 11–12 weeks; 3 = 13–14 weeks; 2 = 15–16 weeks; 0 = over 16 weeks. An unstated timeline is `null` and requires review, not a score of zero.
- **Warranty:** use the workmanship warranty as the differentiator because all rankable sample proposals state a 25-year panel warranty: 5 = 11+ years; 4 = 9–10; 3 = 6–8; 2 = 5; 1 = 1–4; 0 = none evidenced.
- **Team/approach:** 5 = named lead, described crew, safety plan, and additional relevant planning/monitoring; 4 = named lead and crew plus a safety or progress plan; 3 = partial team details with some delivery planning; 2 = coordinator or limited details; 1 = minimal evidence; 0 = none evidenced.

Using these bands gives A = 84, E = 73, and C = 67. These are illustrative design scores, not scores prescribed by the brief. Before a real procurement, administrators should approve and freeze the bands before reviewing bids.

## 3. Human in the loop

A reviewer confirms extracted facts when evidence is missing, contradictory, low-confidence, or relevant to a mandatory requirement. The interface should show the original document alongside extracted values and evidence references. Reviewers can correct extraction, request clarification, and override a proposed status or score only with a recorded reason. A human makes the final award decision. The audit log records reviewer identity, timestamps, source file hash, extracted data and corrections, rubric and prompt versions, model version, criterion scores, flags, overrides and reasons, and the final decision. Access to proposals and logs should be role-based.

## 4. Changing criteria

Do not silently apply edited criteria to existing results. Save every administrator change as a new rubric version, validate that weights total 100%, and preserve previous results with the rubric version that produced them. The reviewer can explicitly request a rerun using the new version; affected proposals should then be rescored consistently and the new ranking clearly labelled. Keep both runs for audit comparison, and require human review before relying on a changed ranking.

## 5. Risks and safeguards

- **Incorrect PDF extraction or OCR:** validate field types and ranges, retain page/quote evidence, and send unclear fields to a reviewer.
- **Fabricated or inferred facts:** require evidence-backed extraction; missing values stay `null`; never treat missing evidence as proof of compliance.
- **Prompt injection in a proposal:** treat proposal content as untrusted data, isolate it from system instructions, ignore embedded commands, and flag suspicious text. Proposal C includes such an instruction and must not receive special treatment.
- **Incorrect eligibility decision:** implement mandatory checks in code. The bidder itself must have the stated Ontario electrical contractor licence; a subcontractor to be confirmed after award does not satisfy the stated requirement. Flag uncertain evidence for human review.
- **Unspecified or unfair scoring:** publish objective score bands, use the same rubric for all bidders, test examples, and log score reasons. Do not use irrelevant personal or protected characteristics.
- **Missing timeline or conflicting facts:** do not invent a value. Flag Proposal D because its completion schedule is undetermined and compliance with the 16-week limit cannot be established.
- **Criteria drift and nondeterministic outputs:** version prompts, models, and rubrics; keep AI focused on extraction; use deterministic code for final scoring and sorting.
- **Privacy and cost:** use only authorized proposal data, restrict access, define retention limits, and set file-size, page-count, rate, and budget limits. Avoid sending documents to external AI services without appropriate approval.

## Sample ranking assumptions

Proposal B is marked `ineligible` because the bidder's own licence is not confirmed, although a licensed subcontractor is planned. Proposal D is `flagged` because its timeline is not stated; it is not ranked until the reviewer resolves that uncertainty. Proposals A, E, and C are ranked using the illustrative rubric described above. The final award remains a human decision.
