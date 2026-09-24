Source: https://drive.google.com/file/d/1KlH8w1jX95htOmpuDVU1Ba1_QIu_924w/view (Policywright-Coordination-Plan.docx)

Policywright — Ecosystem Coordination Plan
SCF #44 · RFP Track · “OZ accounts policy builder” · XXIX Labs · License: MIT
The “OZ accounts policy builder” RFP weighs ecosystem coordination explicitly: alignment with OpenZeppelin, integration with a Stellar smart-account wallet, and building on existing work (kalepail/pollywallet) rather than greenfield. This document sets out who Policywright coordinates with, what is shared and asked of each party, the cadence, and the dependencies — all anchored in the Stellar/Soroban stack.
Coordination map

Policywright coordination map: OpenZeppelin (technical reviewer), C-Address cohort wallet (integration), kalepail/pollywallet (prior art), SCF/SDF (alignment and Audit Bank)

1. OpenZeppelin — accounts package maintainers (technical reviewer)
Relationship. OZ has been consulted on this RFP and indicated interest in participating as a technical reviewer, not a co-owner. Policywright’s deliverable is independent of OZ’s own roadmap; OZ validates correctness and quality, and we align on what would be worth upstreaming.
What we share. - Design decisions on policy-library composition — when Policywright composes stock OZ policies (spending_limit, simple_threshold, weighted_threshold) versus generating a net-new Policy contract. - Samples of generated Soroban policy code for review of correctness against the OZ accounts Policy trait and lifecycle (install / can_enforce / enforce / uninstall), including storage segregation by (smart_account, context_rule_id) for stateful policies.
What we ask. - Feedback on generated-code quality and on the Policy-trait usage patterns we emit. - Agreement on which generated primitives (e.g. a frequency-limit policy) are candidates to upstream into the OZ accounts package, so the ecosystem benefits beyond Policywright.
Cadence. Initial design review before/early in the build; a generated-code review during Tranche 2; a validation pass in Tranche 3 (deliverable D3.2).
Stellar specifics. OZ Stellar accounts package on Soroban; the three composable elements (context rules, signers, policies); ≤5 policies per context rule.
Status. [note current contact and any confirmed conversation — strengthens the submission]

2. C-Address Tooling cohort wallet — reference integration
Relationship. The RFP requires integration with at least one Stellar wallet that supports OZ smart accounts, to make the install flow end-to-end demonstrable. Policywright integrates with a C-Address Tooling cohort wallet as its reference integration.
What we build together. The end-to-end flow record → generate → simulate → sign → install on a real Soroban smart account (a C… address), with the install transaction signed client-side via stellar-wallets-kit — secret keys never leave the client. Demonstrated on testnet in Tranche 2 (D2.5) and on mainnet in Tranche 3 (D3.4).
What we ask. Confirmation of the smart-account install interface and a test smart account; coordination on the signing/install UX so the generated policy installs cleanly.
Cadence. Engage early in Tranche 1 (this is the only externally-dependent deliverable); integrate in Tranche 2; mainnet demonstration in Tranche 3.
Stellar specifics. C-addresses, stellar-wallets-kit, OZ accounts install flow, testnet and mainnet.
Status / candidate wallets. [name the chosen C-Address cohort wallet and any confirmed contact]

3. kalepail / pollywallet — prior art (Tyler)
Relationship. Tyler (kalepail) built kalepail/pollywallet as an MVP proving the record-and-generate concept. The RFP asks respondents to build on it and to state explicitly what they adopt, extend, or replace. We coordinate with Tyler to avoid duplication.
Adopt / extend / replace.
Element
Decision
Rationale
Record-and-generate concept (transaction → policy)
Adopt
Validated by the MVP; it is the core idea
Synthesis quality (minimal scope, gross-outflow caps, argument constraints)
Extend
MVP is a proof; Policywright adds least-privilege rigor and a deny-case harness
Compose-first / generate-second over OZ primitives
Extend
Reuse stock OZ policies before generating new contracts
Agent surface (MCP server + Claude skill)
Add
New: makes the workflow agent-native
Production concerns (audit readiness, storage segregation, versioned release)
Add
New: takes the concept to production quality
Any MVP-specific UI/glue not aligned with the above
Replace
Rebuild around the synthesizer + MCP architecture
What we ask. A short alignment conversation to avoid duplicating effort and to confirm the extend/replace boundaries.
Cadence. Before/early in the build; revisit if Tyler’s work evolves.
Stellar specifics. Soroban smart accounts; the OZ accounts model the MVP targets.
Status. [note any conversation with Tyler]

4. SCF community / SDF tooling — alignment and Audit Bank
Relationship. Policywright aligns with the broader Soroban tooling ecosystem and uses SCF supporting programs.
Alignment. - stellar-cli — generated Soroban Policy contracts build and deploy via stellar-cli, using reproducible (digest-pinned) builds; we track the latest stable releases. - Audit Bank — the synthesizer logic and generated policy templates are submitted for audit via the Audit Bank; the audit is provided at Tranche 3 completion and is not in the budget. - Stellar Developer Docs — developer documentation is contributed upstream (Tranche 3).
Community updates. Regular status updates in the Stellar Dev Discord (#scf channels) and via open channels (Mastodon / BlueSky), consistent with the RFP’s commitment to keep the community informed.
Cadence. Audit scope submitted in Tranche 3 (D3.5); status updates at least at each tranche boundary, plus material progress in between.
Stellar specifics. stellar-cli, Soroban Audit Bank, Stellar Developer Docs.

Coordination summary
Stakeholder
Role
We share / ask
Cadence
Status
OpenZeppelin (accounts maintainers)
Technical reviewer
Composition design + generated code; ask for correctness review + upstream candidates
Design → T2 review → T3 validation
[fill]
C-Address cohort wallet
Reference integration
Install interface + test account; build record→install flow
Early T1 → integrate T2 → mainnet T3
[fill]
kalepail / pollywallet (Tyler)
Prior art
Extend/replace boundaries; avoid duplication
Early build
[fill]
SCF community / SDF tooling
Alignment + Audit Bank
stellar-cli alignment; audit scope; status updates
Ongoing; audit T3
In progress
Dependencies and risk
External coordination — chiefly the wallet integration and OZ review — is the main dependency. Mitigation: engage the wallet team early in Tranche 1, since the install flow is the only deliverable with an external dependency; the synthesizer, MCP server, and Claude skill ship regardless of integration timing. OZ participates as a reviewer, so its input refines quality rather than gating delivery. All coordination is reflected in the milestone plan, and any change is communicated to the SCF team before a modified tranche is submitted.
