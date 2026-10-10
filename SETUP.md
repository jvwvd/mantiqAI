# MantiqAI Setup Guide

MantiqAI is an AI-assisted agent for authorized educational cybersecurity challenges and CTF environments. This guide covers installation and configuration on Windows using PowerShell.

## Prerequisites

- Windows 11
- Git for Windows
- Node.js 22.19 or newer
- Docker Desktop
- Access to a supported Pi model provider or account

Check that the required tools are installed:

```powershell
git --version
node --version
npm --version
docker --version
```

Open Docker Desktop and make sure it is running before building the sandbox.

## 1. Install Pi

Install Pi using npm:

```powershell
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

Verify the installation:

```powershell
pi --version
```

Official guide: https://pi.dev/docs/latest/quickstart

## 2. Clone MantiqAI

To obtain a fresh copy of the repository:

```powershell
git clone https://github.com/jvwvd/mantiqAI.git
Set-Location .\mantiqAI
```

If you already have the repository locally, open PowerShell in its root folder instead of cloning it again.

## 3. Install the CTF skills

The `pi-skills/` directory contains reusable CTF skills and supporting reference material. The top-level `ctf-*` directories contain the category skills.

Run the following commands from the MantiqAI repository root:

```powershell
$sourceSkills = Join-Path (Get-Location).Path "pi-skills"

if (-not (Test-Path -LiteralPath $sourceSkills)) {
    throw "Run these commands from the MantiqAI repository root; pi-skills was not found."
}

$skillsDir = Join-Path $HOME ".pi\agent\skills"
New-Item -ItemType Directory -Force -Path $skillsDir | Out-Null

Get-ChildItem -LiteralPath $sourceSkills -Directory -Filter "ctf-*" |
    Where-Object {
        Test-Path -LiteralPath (Join-Path $_.FullName "SKILL.md")
    } |
    ForEach-Object {
        $target = Join-Path $skillsDir $_.Name

        if (Test-Path -LiteralPath $target) {
            Write-Host "Skipping existing skill folder: $($_.Name)"
        }
        else {
            Copy-Item -LiteralPath $_.FullName -Destination $target -Recurse
            Write-Host "Installed: $($_.Name)"
        }
    }
```

This installs the top-level CTF category skills without copying the entire repository tree into Pi's skill directory. That helps avoid duplicate skill definitions and unrelated reference or payload folders.

Existing skill directories with matching names are skipped rather than overwritten. Back up and review any existing installations before updating them manually.

Keep the original `pi-skills/LICENSE` and other license or attribution notices with the project. Review third-party skills before using them.

Official skill documentation: https://pi.dev/docs/latest/skills

## 4. Install the subagent extension (optional)

Additional agents are optional. Install the extension only if you plan to use subagents:

```powershell
pi install npm:pi-subagents
```

MantiqAI is intended to solve directly by default and use additional agents when they provide a clear benefit. See `AGENTS.md` for the intended workflow.

## 5. Configure a model provider

Start Pi:

```powershell
pi
```

Inside Pi, use `/login` and follow the prompts for your chosen provider. Use `/model` to select an available model.

Provider and model options may change, so follow the options shown by Pi.

Provider guide: https://pi.dev/docs/latest/providers

Pi may save credentials in user-level configuration, including `~/.pi/agent/auth.json`. Never commit API keys, credentials, `auth.json`, session data, or private configuration to this public repository.

## 6. Build the Docker sandbox

Make sure Docker Desktop is running. From the repository root, run:

```powershell
Set-Location .\sandbox-setup
docker build -t mantiqai-sandbox -f Dockerfile .
Set-Location ..
```

This builds the local Docker image named `mantiqai-sandbox`. If the build fails, check the Docker error and confirm that Docker Desktop is running.

## 7. Run MantiqAI

From the repository root, start Pi:

```powershell
pi
```

Use `AGENTS.md` for general agent instructions and `CHALLENGE_ROUTER.md` for category-specific guidance. Relevant skills provide additional techniques and reference material.

If you change instructions or skills during an active Pi session, use `/reload` when appropriate.

## Security and configuration

- Use MantiqAI only in authorized competitions, educational labs, and explicitly scoped environments.
- Keep credentials, session data, personal configuration, and private files out of the repository.
- Review third-party skills and dependencies before relying on them.
- Check that commands and tools are appropriate for the current challenge environment.
- Before committing, review `git status` and the staged diff for secrets, private information, and unrelated files.
