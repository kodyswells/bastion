# Bastion

**Bastion** is a Windows-focused host-based intrusion detection and persistence monitoring tool. It establishes a baseline of high-risk persistence surfaces and detects drift over time, highlighting newly added, modified, or removed artifacts that may indicate compromise.

Bastion is designed as a defensive, blue-team oriented project with an emphasis on correctness, explainability, and operational realism. It is intended as a portfolio and learning project, not a replacement for enterprise endpoint protection platforms.

---

## Project Goals

The primary goal of Bastion is to answer a simple but critical security question:

> What changed on this host that could indicate malicious persistence or execution risk?

To achieve this, Bastion focuses on:
- High-signal persistence mechanisms
- Reliable baselining and drift detection
- Clear, explainable output suitable for triage
- Clean architecture that can be extended incrementally

Noise reduction, application-specific presets, and advanced heuristics are intentionally deferred until the core detection model is solid.

---

## Current Scope (v1)

Bastion v1 focuses on **baseline creation and drift detection** across common Windows persistence surfaces.

### Monitored Surfaces

- **Registry persistence**
  - Run and RunOnce keys (HKCU and HKLM)
  - Winlogon shell and userinit values
- **Scheduled Tasks**
  - Task actions, triggers, run context, and enabled state
- **Windows Services**
  - Service binary paths, start type, and service accounts
- **Startup Folders**
  - User and system startup directories
  - File-backed persistence with hashing

Each surface is collected independently, normalized into a common artifact model, and stored locally for comparison.

---

## How Bastion Works

1. **Baseline**
   - Collects persistence artifacts from the host
   - Stores them as a known-good baseline in a local database

2. **Scan**
   - Re-collects the same artifacts
   - Compares current state against the baseline
   - Detects added, removed, or modified artifacts

3. **Report**
   - Outputs a summary of detected changes
   - Highlights potential risk factors such as:
     - New persistence entries
     - Modified execution paths
     - SYSTEM-level persistence

Bastion prioritizes transparency over automation. Every alert is tied to a concrete system artifact that can be manually verified.

---

## Design Principles

- **Baseline-first detection**  
  Trust is established through observation, not assumptions.

- **No blind allowlisting**  
  Nothing is ignored at collection time. Noise management is handled later at the reporting layer.

- **Explainable output**  
  Every finding can be traced back to a specific registry key, task, service, or file.

- **Incremental complexity**  
  Advanced features are added only after the core system is stable and testable.

---

## Architecture Overview

```
bastion/
  src/bastion/
    cli.py
    config.py
    logger.py

    collectors/
      registry.py
      tasks.py
      services.py
      startup.py

    normalize/
      artifact.py

    storage/
      sqlite.py
      schema.sql

    detect/
      diff.py

    report/
      console.py

  tests/
  README.md
  pyproject.toml
```

Each collector gathers raw data, which is normalized into a consistent artifact format before being stored or compared.

---

## Installation

### Requirements
- Windows 10 or Windows 11
- Python 3.11 or newer
- Administrator privileges recommended for full visibility

### Setup

```bash
git clone https://github.com/yourusername/bastion
cd bastion
pip install -r requirements.txt
```

---

## Usage

Create a baseline:

```bash
bastion baseline
```

Run a scan and detect drift:

```bash
bastion scan
```

Future versions will expand reporting and monitoring capabilities.

---

## What This Project Demonstrates

Bastion is intended to demonstrate:
- Understanding of Windows persistence mechanisms
- Host-based intrusion detection fundamentals
- Baseline and drift-based detection strategies
- Clean Python architecture for security tooling
- Practical blue-team engineering mindset

This project is suitable as a portfolio artifact for security engineering, detection engineering, or defensive infrastructure roles.

---

## Roadmap

Planned future enhancements include:
- Severity scoring improvements
- Artifact allowlisting and suppression rules
- Application-specific noise reduction profiles
- YARA-based file scanning
- Continuous monitoring mode
- MITRE ATT&CK technique mapping
- Packaged Windows executable

---

## Disclaimer

Bastion is an educational and research project. It is not intended to replace enterprise endpoint detection or antivirus solutions.
