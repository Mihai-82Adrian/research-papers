# Research Papers

Independent technical research by **Mihai-Adrian Mateescu**.

This repository contains public research papers, technical notes, and publication artifacts related to AI systems, agent governance, software architecture, and human–AI engineering workflows.

The underlying implementation work may originate from private research environments and repositories. Only materials intentionally prepared for public distribution are published here.

## Publications

### Zero-Trust Semantic Code Auditing with TypeSafe Jev

A technical whitepaper exploring the use of TypeSafe AI's Jev System One
as an external semantic policy evaluator for code produced by autonomous
coding agents.

The work examines:

- separation between code generation and code verification;
- machine-evaluable engineering policy;
- typed semantic auditing with Jev;
- fail-closed enforcement;
- audit provenance and reproducibility;
- the relationship between semantic evaluation, tests, and system replay.

**[Read the whitepaper (PDF)](./Mihai-Adrian_Mateescu_Zero_Trust_Semantic_Code_Auditing_with_Jev_v1.1.pdf)**

*Version 1.1 · September 2026*

## Current evidence status of the Jev semantic-auditing work

The original v1.1 paper should be read as the first formulation and implementation of the research hypothesis.
Subsequent independent review identified weaknesses in the original ABE enforcement layer. Follow-up experiments in
Orkaid therefore separate deterministic enforcement from semantic-model performance and preserve both positive and
negative results.

- **Architecture / research thesis:** remains under active empirical evaluation. It now has direct supporting
  evidence from the Orkaid real-code pilot, at pilot scale: on one real source file, of three controlled
  engineering-invariant violations that the typecheck and all existing domain tests missed, the evaluator detected
  two and missed one.
- **Original implementation claims:** the stronger claims of v1.1, such as "certified", "production ready" and fully
  enforced fail-closed and model-pinning guarantees, are not treated as established by the original ABE evidence.
- **Current evidence:** the frozen experiments, their reproduction tooling and a claim-by-claim status are in
  **[experiments/jev-semantic-auditing](./experiments/jev-semantic-auditing/)**
  ([thesis status](./experiments/jev-semantic-auditing/THESIS_STATUS.md)). The Orkaid Phase-1 governance audit ended
  with the formal result CONFLICT; the real-code pilot detected 3 of 4 controlled mutants with a stable control.

The v1.1 PDF is unchanged and remains the original publication.

---

## About the author

**Mihai-Adrian Mateescu**  
Independent Researcher & Solo Developer

My work focuses on practical architectures for governed AI systems, autonomous software engineering, structured decision workflows, and verifiable human–AI collaboration.

Some of the research published here originates from experiments conducted within **Agentic Blueprint Engine (ABE)**, a private research platform used to explore governed AI engineering workflows.

## Independence

The research in this repository is independent unless explicitly stated otherwise.

References to companies, products, models, or APIs do not imply affiliation, sponsorship, or endorsement.

## License

Unless a publication states otherwise, the documents in this repository are provided for reading, citation, and non-commercial research use.

Copyright © Mihai-Adrian Mateescu.
