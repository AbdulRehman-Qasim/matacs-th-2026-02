# MATACS AI Proposal Triage Design

**Candidate:** Muhammad Abdul Rehman  
**Assessment reference:** MATACS-TH-2026-02  
**Time spent:** 4 hours

## Overview

This repository describes a proposed AI-assisted workflow for extracting facts from contractor proposals and supporting a transparent, human-reviewed ranking. AI is used for document extraction and advisory evidence summaries; application rules validate mandatory criteria and calculate weighted scores.

## Deliverables

- [Process flow diagram](diagram/process-flow.png)
- [Technical write-up](WRITEUP.md)
- [AI prompts](PROMPTS.md)
- [Sample ranking](ranking.json)

## AI tool disclosure

I used ChatGPT and Google Gemini AI tools to help organize the design, draft the prompts and review the sample ranking. I did not accept a price-only ranking suggestion: Proposal B has the lowest price, but the brief requires the bidder itself to hold an Ontario electrical contractor licence, and Proposal B says its electrical subcontractor will be confirmed after award. I therefore treated the mandatory requirement as a gate rather than allowing a low price to compensate for it. I reviewed the outputs against the assessment brief and documented the scoring assumptions in `WRITEUP.md`.

## Important assumptions

The brief supplies criterion weights but does not define exact score bands. The sample scores are illustrative and use the scoring approach documented in `WRITEUP.md`; they are not prescribed by MATACS. The tool supports a reviewer and does not make the final award decision.
