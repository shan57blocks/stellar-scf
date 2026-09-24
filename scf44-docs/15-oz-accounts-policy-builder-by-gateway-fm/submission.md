# OZ Accounts Policy Builder by Gateway.fm: OpenZeppelin Accounts Policy Builder

Source: https://communityfund.stellar.org/submissions/recTFz7yIm9fWFHSx (SCF #44, awarded $98.0K, category: Developer Tooling)

- Website: https://gateway.fm
- Architecture doc: https://gist.github.com/revitteth/191331235c2f68ba307941cbf8060867

## Links in the submission

- https://gist.github.com/revitteth/191331235c2f68ba307941cbf8060867

## Submission text (as published)

```text
Products & Services
A
record-and-generate
 toolkit for OpenZeppelin Stellar smart accounts. OZ smart accounts give Soroban real programmable authorization, but authoring a custom
policy
 today means writing and auditing a full Soroban contract implementing the
Policy
 trait — too high a bar for most developers and impossible for end users, so the delegation infrastructure goes unused. A user (or agent) points at a transaction they
already performed
; the tool auto-generates a
tightly scoped, minimum-permission policy
 that permits exactly that operation and denies everything else. It emits
human-readable Rust
, proves the policy with a permit/deny dry-run report, and
never deploys automatically
 — code-first, deploy-second. All seven RFP-required components are delivered:
-
1. Recording layer.
Stellar usage:
 reads the
SorobanAuthorizationEntry
 auth tree from the tx envelope (or simulation response) for
(contract, fn_name, args)
, and
resultMetaXdr
 for token events +
LedgerEntry
 diffs, via Soroban RPC
getTransaction
 /
simulateTransaction
.
Impact:
 turns a real transaction into a precise authorization fingerprint.
-
2. Synthesizer.
Stellar usage:
 derives scope ·
valid_until
 · value caps �� call-count · minimum permission; reuses OZ
spending_limit
 /
simple_threshold
 /
weighted_threshold
 first.
Impact:
 bounds the safety-critical generated Rust to a minimum by leaning on already-audited primitives.
-
3. Generated Rust policy.
Stellar usage:
 a Soroban contract implementing OZ's
Policy
 trait; always asserts
fn_name
 and the relevant
args
 so a grant is bounded to exactly the observed call — the gap contract-level / threshold-only policies can't close.
Impact:
 human-readable, auditable, minimum-permission policies.
-
4. MCP server (Rust).
Stellar usage:
 one binary with the synthesizer/harness exposing deterministic
record
 /
synthesize
 /
simulate
 /
verify
 tools (same input + version → same policy bytes and verdicts); no cross-language IPC.
Impact:
 a programmable, reproducible interface for tools and agents.
-
5. Agent skill.
Stellar usage:
 a packaged Claude skill over the MCP tools; elicits clarification on ambiguous scope/lifetime and requires explicit
confirm-before-deploy
; the LLM only calls the deterministic tools, never decides the bytes.
Impact:
 conversational policy authoring without handing code-generation trust to the model.
-
6. Dry-run harness.
Stellar usage:
 in
soroban-sdk
 test envs and via
simulateTransaction
, replays the original (must permit) and 5 auto-mutated deny cases (different contract, larger amount, out-of-window timing, different function, different recipient — each must revert).
Impact:
 trust is earned by demonstrated permit/deny behaviour, not asserted.
-
7. Mainnet wallet integration.
Stellar usage:
 record → generate → simulate → sign → install flow over
smart-account-kit
 /
passkey-kit
 +
launchtube
, testnet → mainnet;
kalepail/pollywallet
 is the named starting point (extended; its README lists policy recording/generation as not-implemented).
Impact:
 a real, user-signed install of a generated policy on mainnet.
Three end-to-end walkthroughs (per RFP):
 Blend yield-claim (only
claim
 on one pool,
to
 = self); SEP-41 subscription (fixed amount/cadence via a
spending_limit
 sized so one charge fills the window); bounded Soroswap (`swap_exact_tokens_for_tokens`, whitelisted
path
, capped
amount_in
, bounded by
deadline
).
Requested Budget
$98.0K
Traction Evidence
This is a new build in direct response to the RFP. Our relevant traction is operational and team-based:
-
Public Soroban RPC on mainnet and testnet, verifiable today:
rpc.mainnet.stellar.gateway.fm
 and
rpc.testnet.stellar.gateway.fm
 — both
getHealth: healthy
 on Protocol 26 (a reviewer can confirm in one
POST
). Originated in a 2024 SDF collaboration; Gateway holds no active SDF grant.
-
Directly relevant access-control engineering:
 the implementation lead is a core contributor to Gateway's
Open Privacy Suite
 (role-based access-control stack) and built an
internal MCP server (official MCP Go SDK) exposing ~90 structured tools
 over that backend, with a
confirm-before-mutate
 token flow that defeats parameter-swap/replay — the same confirm-before-deploy discipline this RFP needs, already shipped in the same domain.
-
Low-level systems track record:
 230+ commits on
cdk-erigon
; contributor to Gateway's Miden↔Agglayer cross-VM proof bridge. Public code:
https://github.com/gateway-fm
>
Honest note on the MCP criterion:
 Gateway has no
public
 MCP repo — the ~90-tool server above is internal, so we describe it rather than link it. The MCP server + agent skill this RFP needs is established ground for us, not a capability we'd be learning on this grant.
Tranche 1 (Deliverable Roadmap) - MVP
MVP (~weeks 1–6):
- Recording layer + synthesizer v1 (reuse OZ
spending_limit
 + a minimal custom
Policy
).
- MCP server v0 exposing
record
 /
synthesize
.
-
Verifiable outcome:
 a recorded testnet transfer is turned into a compilable Rust policy that
stellar contract build
 accepts.
- Budget: $19,600 (20%)
Tranche 2 (Deliverable Roadmap) - Testnet
Testnet (~weeks 7–14):
- Dry-run harness (replay original must permit + 5 auto-mutated deny cases must revert).
- Claude agent skill (clarification + confirm-before-deploy).
- All three end-to-end walkthroughs on testnet (Blend yield-claim, SEP-41 subscription, bounded Soroswap).
- Wallet integration + hosted testnet endpoint.
-
Verifiable outcome:
 a public demo + video with passing permit/deny reports.
- Budget: $29,400 (30%)
Tranche 3 (Deliverable Roadmap) - Mainnet
Mainnet (~weeks 15–20):
- Production versioned endpoint + documentation + full test suite.
- Security-audit remediation (via the SCF Soroban Security Audit Bank).
- Documented OpenZeppelin technical-reviewer sign-off on generated-code quality before mainnet.
- One mainnet wallet integration.
-
Verifiable outcome:
 a mainnet install of a generated policy; published audit report + remediation.
- Budget: $39,200 (40%)
Team
Gateway.fm
 AS — a Web3 infrastructure company founded in 2021, ~50 staff, Stavanger, Norway; builder of the Presto rollup-as-a-service platform and operator of public Soroban RPC built with SDF. GitHub:
https://github.com/gateway-fm
-
Ivan Beliakov
 — Senior Engineer,
implementation lead (full-time)
 and
primary contact
. Core contributor to Gateway's Open Privacy Suite (role-based access control) — the most direct fit, since "who may do exactly what, for how long" is this RFP's whole problem. Built the internal ~90-tool MCP server (official MCP Go SDK) with a confirm-before-mutate token flow; 230+ commits on
cdk-erigon
; contributor to the Miden↔Agglayer proof bridge. Email
ivan.beliakov@gateway.fm
 · GitHub
IvanBelyakoff
-
Max Revitt
 — Head of R&D; light-touch review and engagement / ecosystem coordination. LinkedIn
https://www.linkedin.com/in/maxrevitt/
-
Igor Mandrigin
 — CTO, ex-Erigon co-author; available for security/design review at the audit and OpenZeppelin sign-off gates. GitHub [@mandrigin](
https://github.com/mandrigin
) · LinkedIn
https://www.linkedin.com/in/mandrigin/
Daniel
```
