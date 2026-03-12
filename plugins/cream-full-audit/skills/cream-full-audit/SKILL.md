---
name: cream-full-audit
description: Orchestrates a comprehensive 4-stage security audit for CREAM Trading Funds. Composes Pashov solidity-auditor, Trail of Bits skills (entry-point-analyzer, insecure-defaults, sharp-edges, spec-to-code-compliance, supply-chain-risk-auditor, fp-check, variant-analysis, token-integration-analyzer, semgrep, property-based-testing, audit-context-building), and custom skills (nemesis-auditor, senior-security) into a unified pipeline with dedup and severity-ranked output. Trigger on /cream-full-audit or "run full audit".
---

# CREAM Full Security Audit

You are the orchestrator of a 4-stage comprehensive security audit for the CREAM Trading Funds platform. You invoke specialized skills, collect their outputs, and produce a unified severity-ranked report.

## Scope

| Layer | Paths | What gets audited |
|-------|-------|-------------------|
| Contracts | `contracts/src/FundETH.sol`, `contracts/src/FundFactoryETH.sol` | Solidity smart contracts on Base L2 |
| Backend | `src/lib/server/`, `src/routes/api/` | SvelteKit server routes, API auth, crypto, wallet management |
| Integration | Both layers | Contract-backend compatibility, state sync, signer correctness |
| Dependencies | `package.json`, `pnpm-lock.yaml`, `contracts/lib/` | Supply chain risk |
| Specs | `docs/` | Spec-to-code compliance |

## Mode Selection

- **Default** (no arguments): Full 4-stage audit
- **`contracts`**: Stages 1-2 contract skills only
- **`backend`**: Stages 1-2 backend skills only
- **`quick`**: Skip Stage 1 context building, run Stage 2 scanning only

## Execution

### Pre-flight

Before starting, verify the environment:

```
1. Run: pnpm check (must pass with 0 errors)
2. Run: forge build (must compile)
3. Print: "CREAM Full Audit — Starting 4-stage pipeline"
4. Record start timestamp
```

### Stage 1 — Context Building (Sequential)

**Purpose:** Build deep architectural understanding before scanning. This reduces false positives in Stage 2.

**Step 1.1 — Audit Context Building**

Invoke `audit-context-building` skill. In the prompt, specify:
- Target paths: `contracts/src/FundETH.sol`, `contracts/src/FundFactoryETH.sol`, `src/lib/server/managedWallet.ts`, `src/lib/server/agentApiKey.ts`, `src/routes/api/v1/agent/`
- Focus: fund lifecycle states, access control modifiers, signing flows, session management

Capture the architectural context output — it feeds into Stage 2 agents.

**Step 1.2 — Entry Point Analysis**

Invoke `entry-point-analyzer` skill on `contracts/src/`. Capture:
- All state-changing external/public functions
- Access classifications (public, onlyTrader, onlyPlatform, onlyPlatformAdmin, onlyFactory)
- The attack surface map

Print a summary table of entry points before proceeding.

### Stage 2 — Parallel Scanning

Launch ALL of the following as parallel Agent tool calls. Each agent gets the context from Stage 1 in its prompt.

**Agent 2A — Solidity Audit (Pashov DEEP)**

Invoke the `solidity-auditor` skill with argument `deep`. This spawns 4 vector-scan agents + 1 adversarial reasoning agent internally. Do NOT interfere with its internal orchestration.

**Agent 2B — Backend Logic Audit (Nemesis)**

Invoke the `nemesis-auditor` skill. Target:
- `src/lib/server/managedWallet.ts` — session cache, key derivation, signing
- `src/lib/server/agentApiKey.ts` — API key auth
- `src/lib/server/signingSessionHelper.ts` — session validation
- `src/lib/server/tradeStateMachine.ts` — trade lifecycle
- `src/lib/server/relayer.ts` — on-chain tx relay
- `src/lib/server/liquidation.ts` — position liquidation
- `src/routes/api/v1/agent/` — all agent API routes
- `src/routes/api/cron/` — cron job routes

**Agent 2C — Token Integration (Trail of Bits)**

Invoke `token-integration-analyzer` skill on `contracts/src/FundETH.sol`. Focus on:
- ETH handling patterns (receive, fallback, value transfers)
- ERC20 interactions if any
- Weird token edge cases

**Agent 2D — Insecure Defaults**

Invoke `insecure-defaults` skill on `src/lib/server/` and `src/routes/api/`. Look for:
- Hardcoded fallback secrets
- Fail-open behavior when env vars are missing
- Default crypto parameters

**Agent 2E — Sharp Edges**

Invoke `sharp-edges` skill on `src/lib/server/`. Focus on:
- Crypto API ergonomics (argon2, xchacha20, HKDF)
- Session token generation
- Rate limit configuration

**Agent 2F — Spec-to-Code Compliance**

Invoke `spec-to-code-compliance` skill with:
- Specs: `docs/API.md`, `docs/AGENT_INTEGRATION.md`, `docs/SECURITY_AUDIT.md`, `CLAUDE.md`
- Code: `contracts/src/`, `src/routes/api/v1/agent/`, `src/lib/server/`

**Agent 2G — Supply Chain Risk**

Invoke `supply-chain-risk-auditor` skill on the project root. Covers both npm (package.json) and Foundry (contracts/lib/) dependencies.

**Agent 2H — Semgrep Static Analysis**

Invoke the `semgrep` skill (from `static-analysis` plugin) on `src/`. Use "important" mode for security-focused rules.

### Stage 3 — Validation

After ALL Stage 2 agents complete:

**Step 3.1 — False Positive Check**

Collect all findings from Stage 2 agents. For each finding rated Medium or above, invoke `fp-check` skill with the finding details and the relevant code. This produces TRUE POSITIVE or FALSE POSITIVE verdicts.

Remove all FALSE POSITIVE findings from the report.

**Step 3.2 — Variant Analysis**

For each TRUE POSITIVE finding, invoke `variant-analysis` skill to search for similar patterns elsewhere in the codebase. Add any variants found as new findings.

**Step 3.3 — Property-Based Testing Gaps**

Invoke `property-based-testing` skill. Ask it to:
- Review the current Foundry test suite (`contracts/test/`)
- Identify uncovered invariants based on Stage 2 findings
- Recommend specific fuzz tests or invariant tests to add

### Stage 4 — Unified Report

Merge all validated findings into a single report.

**Deduplication Rules:**
- Same root cause across different scanners = 1 finding (keep highest confidence version)
- Same function flagged for different issues = separate findings
- Contract finding + backend finding on same flow = link them in a "Cross-Layer" section

**Severity Ranking:**
| Level | Criteria |
|-------|----------|
| Critical | Direct loss of funds, privilege escalation to drain |
| High | Incorrect state transitions, broken access control, fund lockup |
| Medium | Logic errors with limited impact, missing validation, DoS vectors |
| Low | Gas optimization, code quality, non-critical edge cases |
| Informational | Best practice suggestions, documentation gaps |

**Report Structure:**

```markdown
# CREAM Full Security Audit Report

**Date:** {date}
**Scope:** Contracts + Backend
**Duration:** {elapsed time}
**Skills Used:** {list all invoked skills}

## Executive Summary
{2-3 sentences: overall assessment, finding counts by severity}

## Scope Table
| File | Lines | Skills Applied |
|------|-------|---------------|

## Critical Findings
### [C-01] {title}
**Severity:** Critical | **Confidence:** {High/Medium} | **Source:** {skill name}
**Location:** {file:line}
**Description:** ...
**Impact:** ...
**Recommendation:** ...

## High Findings
...

## Medium Findings
...

## Low Findings
...

## Informational
...

## Cross-Layer Compatibility
{Findings where contract behavior and backend assumptions diverge}

## Supply Chain Risk Summary
{From supply-chain-risk-auditor}

## Spec Compliance Gaps
{From spec-to-code-compliance}

## Recommended Tests
{From property-based-testing}

## False Positives Eliminated
{List of findings removed by fp-check with reasoning}
```

**Output:** Print the full report to terminal. If the user passed `--file-output`, also write to `docs/security-checks/FULL_AUDIT_REPORT.md`.

## Important Rules

1. **Never skip stages.** Even if Stage 1 seems redundant, the context it builds reduces Stage 2 false positives.
2. **Never modify code.** This skill is read-only. It reports findings but does not fix them.
3. **Respect skill boundaries.** When invoking a sub-skill, pass it the right scope and let it run its own internal logic. Do not duplicate what a sub-skill already does.
4. **Time budget.** Print elapsed time after each stage. The full audit typically takes 15-30 minutes.
5. **Parallel where possible.** Stage 2 agents MUST run in parallel (8 concurrent agents). Stage 3 steps can also be parallelized.
