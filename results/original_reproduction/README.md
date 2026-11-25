# Historical reproduction attempt

The original implementation was run at commit `7474c77` with the WikiText
identifier updated to `Salesforce/wikitext`, which is required by current
Hugging Face tooling. Four of five published retrieval rows were close to the
reported values; the contribution-gated row was 7.5% rather than 32.8% and was
below its 10.9% no-regularization baseline.

This is a historical record. The current edge-level implementation and
resolvability analysis are the basis for the repository's conclusions.
