# 1.1 Two gaps in the agentic web

Autonomous agents now transact on behalf of humans and organizations. Two questions must be answerable at the HTTP boundary, per request, without a human in the loop:

* **Can you pay?** — solved by **x402** (402 Payment Required).
* **Are you allowed to do this, and who stands behind you?** — the gap **x401** closes (401 Unauthorized).

The second gap is sharper in an AI-saturated environment. Deepfakes and synthetic media have made traditional "is there a human?" authentication cheap to bypass, and agentic traffic already exceeds human web traffic. x401 answers this with **deterministic cryptographic verification** rather than probabilistic heuristics: a presentation either validates against a trusted issuer's keys or it does not.
