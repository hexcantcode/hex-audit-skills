<p align="center">
  <img src="https://img.icons8.com/emoji/96/shield-emoji.png" alt="hex-audit-skills" width="96" height="96" />
</p>

<h1 align="center">hex-audit-skills</h1>

<p align="center"><strong>The orchestration layer for multi-framework security audits.</strong></p>

<p align="center">
  Context &middot; Scan &middot; Validate &middot; Report
</p>

<p align="center">
  <img src="https://img.shields.io/badge/skills-16-blue?style=flat-square" alt="skills" />
  <img src="https://img.shields.io/badge/agents-8_parallel-orange?style=flat-square" alt="agents" />
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="license" />
  <img src="https://img.shields.io/badge/Solidity_%2B_TypeScript-supported-purple?style=flat-square" alt="languages" />
  <img src="https://img.shields.io/badge/Claude_Code-compatible-yellow?style=flat-square" alt="Claude Code" />
</p>

<p align="center">
  Composes <a href="https://github.com/pashov/skills">Pashov</a>, <a href="https://github.com/trailofbits/skills">Trail of Bits</a>, and custom audit skills into a single 4-stage pipeline with false-positive elimination and severity-ranked reporting.
</p>

---

## Why This Exists

Running security audits with individual skills means context loss between tools, duplicate findings, and no unified severity ranking. This skill solves that by orchestrating 16 specialized skills into a single command with shared context, parallel execution, and automated false-positive filtering.

---

## The Pipeline

```
                    Stage 1                    Stage 2                     Stage 3              Stage 4
               Context Building           Parallel Scanning             Validation            Reporting
              ┌──────────────────┐    ┌─────────────────────────┐    ┌──────────────┐    ┌──────────────┐
              │ audit-context-   │    │ solidity-auditor (DEEP) │    │   fp-check   │    │  Deduplicate │
              │ building         │    │ nemesis-auditor         │    │   variant-   │    │  Rank by     │
              │                  │───>│ token-integration       │───>│   analysis   │───>│  severity    │
              │ entry-point-     │    │ insecure-defaults       │    │   property-  │    │  Cross-layer │
              │ analyzer         │    │ sharp-edges             │    │   based-test │    │  report      │
              └──────────────────┘    │ spec-to-code-compliance │    └──────────────┘    └──────────────┘
                                      │ supply-chain-risk       │
                                      │ semgrep                 │
                                      └─────────────────────────┘
```

---

## Skills Composed

| # | Skill | Source | Stage | Scope |
|---|-------|--------|-------|-------|
| 1 | `audit-context-building` | Trail of Bits | 1 | Contracts + Backend |
| 2 | `entry-point-analyzer` | Trail of Bits | 1 | Contracts |
| 3 | `solidity-auditor` DEEP | Pashov | 2 | Contracts (5 internal agents) |
| 4 | `nemesis-auditor` | Custom | 2 | Backend logic |
| 5 | `token-integration-analyzer` | Trail of Bits | 2 | Token handling |
| 6 | `insecure-defaults` | Trail of Bits | 2 | Backend configs |
| 7 | `sharp-edges` | Trail of Bits | 2 | API footguns |
| 8 | `spec-to-code-compliance` | Trail of Bits | 2 | Docs vs code |
| 9 | `supply-chain-risk-auditor` | Trail of Bits | 2 | Dependencies |
| 10 | `semgrep` | Trail of Bits | 2 | Static analysis |
| 11 | `fp-check` | Trail of Bits | 3 | Finding validation |
| 12 | `variant-analysis` | Trail of Bits | 3 | Pattern matching |
| 13 | `property-based-testing` | Trail of Bits | 3 | Test gap analysis |
| 14 | `senior-security` | Custom | 2 | App-layer security |
| 15 | `feynman-auditor` | Custom | via nemesis | Logic bugs |
| 16 | `state-inconsistency-auditor` | Custom | via nemesis | State sync bugs |

---

## Prerequisites

This skill orchestrates other skills. Install them first:

```bash
# Pashov solidity-auditor
# Install from https://github.com/pashov/skills

# Trail of Bits skills (11 plugins)
/plugin marketplace add trailofbits/skills

# Custom skills: nemesis-auditor, feynman-auditor,
# state-inconsistency-auditor, senior-security
```

---

## Installation

### Marketplace (Recommended)
```
/plugin marketplace add hexcantcode/hex-audit-skills
```

### Manual
```bash
cp plugins/cream-full-audit/skills/cream-full-audit/SKILL.md \
   .claude/commands/cream-full-audit/SKILL.md
```

---

## Usage

```bash
/cream-full-audit              # Full 4-stage audit
/cream-full-audit contracts    # Contracts only
/cream-full-audit backend      # Backend only
/cream-full-audit quick        # Skip context building
```

---

## Report Output

The unified report includes:

- **Executive Summary** — overall assessment + finding counts
- **Findings by Severity** — Critical > High > Medium > Low > Informational
- **Cross-Layer Compatibility** — contract-backend state sync issues
- **Supply Chain Risk Summary** — dependency health assessment
- **Spec Compliance Gaps** — documentation vs implementation divergence
- **Recommended Tests** — fuzz targets + invariant suggestions
- **False Positives Eliminated** — what was filtered and why

---

## License

MIT
