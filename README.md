# Agent Security Survey

A comprehensive literature survey on LLM agent security.

## Overview

This repository hosts the artifacts of a systematic literature survey on the
security of LLM-based agents. The survey covers three domains:

**Agent security (core domain)**

- Prompt injection (direct and indirect)
- Tool / MCP & skill supply chain security
- Memory safety (poisoning, leakage, persistence)
- Multi-agent risks (trust, communication, collusion)
- Agentic cyber offense & defense
- Identity & permissions (delegation, authorization)
- Benchmarks & red-teaming
- System-level defenses (sandboxing, isolation, runtime guardrails)
- Governance & real-world incidents

**RAG security**

- Retrieval corpus & knowledge-base poisoning
- RAG-specific jailbreaks, data exfiltration, and defenses
- Citation and grounding integrity

**Embodied agent security**

- Physical-world agents: robots, drones, autonomous vehicles
- GUI / computer-use and web agents operating in real environments
- Perception spoofing, sim-to-real safety gaps, and physical harm prevention

## Key Numbers

| Metric | Value |
| --- | --- |
| Paper corpus | 4,913 papers |
| Abstract-level LLM screening | K = 1,414 / S = 1,011 / D = 748 + 1,740 baseline/union; +674 backfilled from a legacy keyword-database recheck |
| Core set (full text parsed via MinerU) | 152 papers |
| Trend documents | 3 |
| Coverage | 2021 – 2026 |
| Citation snapshot | 2026-10-13 (Semantic Scholar) |

## Repository Contents

| Path | Description |
| --- | --- |
| `manifest.csv` | Machine-readable index of the 4,913-paper corpus: id, title, year, authors, citations, domain, subtopic, verdict, note, plus local flags (pdf/parsed status) |
| `reports/` | Main survey report and a curated reading list with guide (in Chinese) |
| `trends/` | Three trend analysis documents: agent security, RAG security, embodied agent security (in Chinese) |

## Notes

- The reports and trend documents are written in Chinese; the manifest is
  machine-readable (UTF-8 CSV) and safe to process programmatically.
- The corpus was built via the arXiv API and the Semantic Scholar API.
- All arXiv IDs in the manifest are verifiable against public arXiv records.

## Disclaimer

This repository is a research compilation for informational purposes only.
It does not constitute security advice. Citation counts are Semantic Scholar
snapshots taken on 2026-10-13 and may drift over time. Inclusion of a paper
in the manifest does not imply endorsement of its claims.
