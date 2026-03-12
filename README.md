# hex-audit-skills

Comprehensive security audit orchestration skills for Claude Code. Composes multiple audit frameworks into unified pipelines.

## Skills

### `/cream-full-audit`

A 4-stage security audit pipeline for DeFi platforms (Solidity contracts + backend).

**Stage 1 — Context Building** (sequential)
- `audit-context-building` — Ultra-granular code comprehension
- `entry-point-analyzer` — State-changing attack surface mapping

**Stage 2 — Parallel Scanning** (8 concurrent agents)
- `solidity-auditor` DEEP (Pashov) — OWASP Smart Contract Top 10
- `nemesis-auditor` — Feynman + State Inconsistency fused audit
- `token-integration-analyzer` (Trail of Bits) — ERC20/721 conformity
- `insecure-defaults` (Trail of Bits) — Fail-open configs, hardcoded secrets
- `sharp-edges` (Trail of Bits) — Footgun APIs, dangerous configurations
- `spec-to-code-compliance` (Trail of Bits) — Spec vs implementation gaps
- `supply-chain-risk-auditor` (Trail of Bits) — Dependency risk assessment
- `semgrep` (Trail of Bits) — Pattern-based security scanning

**Stage 3 — Validation**
- `fp-check` (Trail of Bits) — False positive elimination
- `variant-analysis` (Trail of Bits) — Find similar vulns across codebase
- `property-based-testing` (Trail of Bits) — Fuzz/invariant test recommendations

**Stage 4 — Unified Report**
- Deduplicated by root cause
- Severity-ranked (Critical > High > Medium > Low > Informational)
- Cross-layer compatibility section (contract-backend state sync)

## Prerequisites

This skill orchestrates other skills. You need these installed:

### Pashov Skills
```bash
# Install from https://github.com/pashov/skills
```

### Trail of Bits Skills
```bash
/plugin marketplace add trailofbits/skills
```

Install these plugins: `audit-context-building`, `entry-point-analyzer`, `building-secure-contracts`, `insecure-defaults`, `sharp-edges`, `spec-to-code-compliance`, `supply-chain-risk-auditor`, `static-analysis`, `fp-check`, `variant-analysis`, `property-based-testing`

### Custom Skills
- `nemesis-auditor` (Feynman + State Inconsistency)
- `senior-security` (App-layer security)

## Installation

### As a marketplace
```
/plugin marketplace add hexcantcode/hex-audit-skills
```

### Manual
Copy `plugins/cream-full-audit/skills/cream-full-audit/SKILL.md` to `.claude/commands/cream-full-audit/SKILL.md` in your project.

## Usage

```
/cream-full-audit           # Full 4-stage audit
/cream-full-audit contracts  # Contracts only
/cream-full-audit backend    # Backend only
/cream-full-audit quick      # Skip context building
```

## License

MIT
