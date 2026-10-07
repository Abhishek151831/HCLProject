# HCLProject
# Travel Reimbursement Approval Agent

## README

This notebook demonstrates a lightweight GenAI/agentic workflow for reviewing employee travel reimbursement claims against a mock policy.

### How to run

1. Install dependencies: `pip install openai pandas matplotlib`
2. Optionally set `OPENAI_API_KEY` if you want the notebook to call an LLM for the reasoning summary.
3. Run all cells in order. The final cell emits the JSON array for all five sample claims.

### Design choices

- Grounding: policy IDs are explicitly looked up before making a decision.
- Tool usage: receipt validation, per-diem checks, and approval-tier checks are implemented as discrete functions.
- Reliability: missing receipts, policy exceptions, and high-value claims are routed to `MANUAL_REVIEW` instead of forced decisions.
- Fallback logic: if no API key is present, the code uses a deterministic policy-aligned reasoning fallback so the notebook still runs offline.

## Policy-grounded workflow

The agent: (1) loads the policy references, (2) validates claim items and receipts, (3) calculates per-diem caps and rejected items, (4) checks approval tier, and (5) produces a structured final recommendation.

## Design Notes & Reasoning

The notebook intentionally keeps the rule engine simple and interpretable. Priorities are: safety, explicit policy references, and manual review for uncertain or exceptional cases. Claim `CLM-004` is routed to `MANUAL_REVIEW` because business-class airfare is a policy exception and the hotel receipt is missing; claim `CLM-005` is also manual review because a required meal receipt is missing even though the amount is otherwise ordinary. This approach reduces the risk of auto-approving claims that require human judgment.

The per-diem logic is applied to reimbursable categories only, and the final approval tier is evaluated against the reimbursable subtotal. This keeps the decision logic aligned with policy and makes the system easier to explain to reviewers.

