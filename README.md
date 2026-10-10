# MantiqAI

MantiqAI is an AI-assisted agent for authorized educational cybersecurity challenges, CTF competitions, and explicitly scoped lab environments.

## Overview

MantiqAI combines structured agent instructions, challenge-category routing, reusable CTF skills, and an isolated Docker environment to support a more organized challenge-solving workflow.

## Key Components

- **`AGENTS.md`** — General agent behavior, problem-solving guidelines, and verification rules.
- **`CHALLENGE_ROUTER.md`** — Category-specific workflows and tool guidance.
- **`pi-skills/`** — Reusable CTF skills and cybersecurity reference material.
- **`sandbox-setup/`** — Docker configuration for an isolated analysis environment.
- **`SETUP.md`** — Installation, configuration, and security guidance.

## Approach

MantiqAI prioritizes focused problem-solving, evidence-based analysis, and verification against the current challenge instance. It solves directly by default and uses additional agents when they provide a clear benefit.

## Getting Started

See [`SETUP.md`](SETUP.md) for installation and configuration instructions.

## Safety

MantiqAI is intended for authorized competitions, educational labs, and explicitly scoped environments. Do not use it against systems or services without permission.

Review third-party skills and dependencies before use, and never commit private credentials or configuration files.
