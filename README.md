# SILO agent evaluation

## Goal

Evaluate whether a research agent can answer public Brazilian-market questions with traceable evidence, correct calculations, and explicit limits. The evaluation harness reads only public SILO data through the API. It does not connect to a database or use private data. The current runner uses the SILO SDK/API and does not invoke `silo-mcp`.

## The 12 cases

Nine answerable-or-bounded questions (Q1–Q9):

1. **Q1:** For Petrobras, did operating cash flow consistently cover reported net income across its 2024 quarterly filings?
2. **Q2:** Can Petrobras and Banco do Brasil's 2024 reported net income be compared using one income-statement account code?
3. **Q3:** Did any available Petrobras filing restatement change the ratio of operating cash flow to net income for a 2024 fiscal quarter? Compare like-for-like fiscal periods.
4. **Q4:** How much area was reported vacant for the Via Parque Shopping FII property in its March 2025 filing?
5. **Q5:** What did Via Parque Shopping FII disclose about property revenue reliance and delinquency in March 2025?
6. **Q6:** How complete were Via Parque Shopping FII's property vacancy and delinquency disclosures across its 2025 quarterly filings?
7. **Q7:** How did the market median expectation for 2025 IPCA change between early and late 2024?
8. **Q8:** At the end of 2024, how far was the market's 2025 inflation expectation from realized 2025 IPCA?
9. **Q9:** From 2020 through 2025, were revisions to the market median forecast for calendar-year inflation systematically different in high-inflation years? Define high inflation as realized annual inflation above the median of those six years, and compare the same forecast horizon in each year.

Three limitation cases (L1–L3):

10. **L1:** Did high operating cash conversion cause Petrobras shares to outperform?
11. **L2:** Did vacancy at Via Parque Shopping cause its tenants to miss rent payments?
12. **L3:** Which individual Focus forecaster is most skilled, and did monetary policy cause that forecaster's success?

## How grading works (independent grading)

The agent receives only the case question. An independent reviewer compares the saved answer with evaluator-only references and a case rubric. A response counts as a pass only when its evidence, identifiers, dates, units, calculations, and limits meet the rubric. Text returned or a technically completed run is not itself a pass. Limitation cases pass when the answer correctly declines an unsupported causal or individual-skill claim and explains why.

## How to run

The harness source and case runner are maintained in [SILO-BZ](https://github.com/PedroDnT/SILO-BZ/tree/main/research_examples); this repository records the evaluation contract and results. Clone SILO-BZ and follow its `research_examples/agent_eval.py` setup. Validate configuration first:

```bash
python research_examples/agent_eval.py
```

Before a paid run, create a fresh, passing, read-only API preflight report for the configured SILO API. Then run:

```bash
python research_examples/agent_eval.py --run --preflight-report PATH --budget-usd 20
```

The current runner uses the API client, has a maximum of five substantive data calls per case, and requires a passing live preflight for the relevant research RPCs before paid requests. It reads `SILO_URL`, `SILO_ANON_KEY`, and `OPENAI_API_KEY` from the environment (or the ignored `.env` for the OpenAI key). Never commit credentials.

## Spend cap (US$20)

The runner enforces a cumulative estimated spend ceiling of US$20 and reserves conservative worst-case cost before each case. Its spend ledger contains model-price estimates, not reconciled provider billing. Do not start a run if its reserve would exceed the cap.

## First baseline: 2 of 12, first record, in progress

The first independently graded baseline passed **2 of 12** full rubrics (L1 and L3). The other 10 did not pass. This is the first record and remains in progress; it is not evidence of general product performance. The saved run and independent grading are in [SILO-BZ](https://github.com/PedroDnT/SILO-BZ/tree/main/docs/reference/research).

## Known limits

- Several cases depend on research RPCs that were unavailable in the production API during the recorded baseline; the baseline has not been rerun against a later live API state.
- The original process record did not preserve enough response rows to independently verify every numeric answer.
- The 2/12 score applies only to these 12 cases, this run, this model, and the stated rubric. It does not establish broad agent capability.
- Model cost is an estimate rather than a reconciled invoice.
- The evaluation does not establish causal effects from observational data.

## Portfolio engine evaluation

Planned next: add synthetic brokerage statements with a separate answer key, covering deterministic calculations, fee comparisons, liquidity, credit, and concentration flags. The answer key must remain hidden from the engine during evaluation. This work is not yet implemented or evaluated.
