# 🔐 nhi-posture-scanner

> **Scan your infrastructure for static secrets masquerading as identity — and chart the path to stateless.**

---

## What It Does

`nhi-posture-scanner` audits your environment for **Non-Human Identity (NHI) posture** — finding every agent, service, bot, pipeline, and workload still authenticating with long-lived static credentials, and giving you a concrete remediation path toward **stateless, ephemeral identity**.

Most organisations know they have a secrets sprawl problem. This tool tells you *exactly how bad it is* and *what to do next*.

**Core capabilities:**

- 🕵️ **Static Secret Detection** — Surfaces long-lived API keys, service account passwords, hardcoded tokens, and stale certificates across your stack
- 📐 **NHI Inventory** — Enumerates all non-human identities: CI/CD pipelines, cloud functions, containers, SDKs, third-party integrations, and internal agents
- 🔄 **Stateless Readiness Score** — Rates each NHI on a spectrum from *fully static* → *stateless-ready*, with a migration priority ranking
- 🧩 **Workload Identity Gap Analysis** — Checks whether OIDC, SPIFFE/SPIRE, or cloud-native workload identity (AWS IRSA, GCP Workload Identity, Azure Federated Credentials) is available but unused
- 📋 **Policy Drift Detection** — Flags NHIs with excessive permissions, no rotation policy, or no owner attribution
- 🔁 **CI/CD Integration** — Runs as a pipeline step; blocks merges that introduce new static credential patterns

---

## Why It Matters

Static credentials are the **#1 attack vector for non-human identities** — and the problem is getting worse as agentic systems multiply.

The core issue:

- A secret that exists **has a blast radius**. It can be leaked, committed, scraped, or stolen.
- A stateless token minted at runtime and **expiring in minutes** has near-zero blast radius.
- Most teams know this — but migration from static → stateless is painful without tooling that maps the current state first.

This scanner exists because you **cannot migrate what you haven't measured**.

The industry is converging on standards — SPIFFE, OIDC federation, workload identity — but adoption lags because discovery is hard. `nhi-posture-scanner` closes that gap.

---

## The Static → Stateless Spectrum

```
STATIC                                                        STATELESS
  │                                                               │
  ▼                                                               ▼
[Hardcoded]  [Stored Secret]  [Rotated Secret]  [Short-lived Token]  [Workload Identity]
     🔴             🔴               🟡                  🟢                    🟢
```

The scanner places every NHI on this spectrum and generates a per-identity migration plan.

---

## Roadmap

### v0.1 — Discovery *(current)*
- [ ] Static secret pattern scanning (env vars, config files, CI definitions)
- [ ] NHI inventory builder — outputs structured YAML/JSON manifest
- [ ] CLI with human-readable posture report
- [ ] MIT licence, contribution guide

### v0.2 — Scoring & Prioritisation
- [ ] Stateless Readiness Score per NHI
- [ ] Risk ranking: credential age × permission scope × exposure surface
- [ ] Cloud provider workload identity availability detection (AWS / GCP / Azure)
- [ ] SPIFFE/SPIRE endpoint discovery

### v0.3 — Migration Playbooks
- [ ] Auto-generated migration guides per NHI type (GitHub Actions → OIDC, K8s → IRSA, etc.)
- [ ] Terraform/Pulumi snippet generation for workload identity configs
- [ ] Drift detection: re-scan and compare against baseline

### v1.0 — Continuous Posture
- [ ] Policy-as-code enforcement in CI/CD
- [ ] Webhook/Slack alerting on new static credential introductions
- [ ] Dashboard: org-wide NHI posture over time
- [ ] SPIFFE SVID lifecycle monitoring

---

## Getting Started

```bash
# Clone the repo
git clone https://github.com/vivarivian280-0ss/nhi-posture-scanner.git
cd nhi-posture-scanner

# Install (Python 3.10+)
pip install -r requirements.txt

# Scan current directory
python scanner.py --target . --report text

# Scan with cloud context (detects workload identity availability)
python scanner.py --target . --cloud aws --report json > posture-report.json
```

### GitHub Actions Example

```yaml
- name: NHI Posture Scan
  uses: vivarivian280-0ss/nhi-posture-scanner@v0.1
  with:
    target: .
    fail-on: critical        # Block PRs that introduce new static secrets
    report-format: sarif     # Native GitHub Security tab integration
```

---

## What It Scans

| Source | What It Looks For |
|---|---|
| `.env` files | Long-lived keys, passwords, tokens |
| CI/CD configs | Hardcoded vars, non-OIDC auth in pipeline YAML |
| Kubernetes manifests | Static `Secret` objects, env vars from hardcoded sources |
| Terraform / IaC | `aws_iam_access_key`, hardcoded credentials in providers |
| Docker images | `ENV` instructions embedding secrets |
| Git history | Accidentally committed credentials not yet rotated |
| Cloud IAM | Service accounts with no expiry, unused keys > 90 days |

---

## Contributing

Pull requests welcome. Open an issue first for major changes.

Areas where contributions are especially valuable:
- Additional static secret pattern signatures
- Cloud provider workload identity modules (AWS / GCP / Azure / OCI)
- SPIFFE/SPIRE integration
- Real-world migration playbooks

---

## Licence

[MIT](LICENSE) — free to use, modify, and distribute.

---

*Built for security engineers, platform teams, and anyone waking up to the reality that their agents have too many secrets and too little identity.*
