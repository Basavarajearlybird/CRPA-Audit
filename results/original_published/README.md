# Historical published artifacts

This directory preserves the result files committed by the original project at
`7474c77`. They are not regenerated and are not current evidence.

The original single-seed table reported 32.8% retrieval for `crpa_causal`. The
current audit found that its row-pair overlap statistic and its masked edge were
different objects; causal no-op masks were also classified as suppressible.
The task's measured retrieval floor is about 52.8%, so the reported CRPA rows
do not demonstrate retrieval.

Use `--intervention_mode legacy_rowpair` only to inspect the historical
selection behavior. Current experiments use edge-level interventions.
