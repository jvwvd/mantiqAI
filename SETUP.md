# MantiqAI Setup Guide

MantiqAI is an AI-assisted agent for authorized educational cybersecurity challenges. These instructions use **Windows PowerShell** and the public repository at `https://github.com/jvwvd/mantiqAI`.

## Prerequisites

- Windows 11
- Git for Windows
- Node.js 22.19 or newer
- Docker Desktop
- A supported Pi model provider or account

Check the installed tools in PowerShell:

```powershell
git --version
node --version
npm --version
docker --version
```

Open Docker Desktop and make sure it is running before building the sandbox.

## 1. Install Pi

Install Pi from npm:

```powershell
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

Verify the installation:

```powershell
pi --version
```

Official guide: https://pi.dev/docs/latest/quickstart

## 2. Clone MantiqAI

```powershell
git clone https://github.com/jvwvd/mantiqAI.git
cd mantiqAI
```

If you already have the repository locally, open PowerShell in its root folder instead of cloning it again.

## 3. Install the skills

The reusable skills are stored in `pi-skills/`. Before installing them, review their contents and keep the `pi-skills/LICENSE` file with the project. Do not remove or replace third-party license and attribution notices.

Copy the skills into Pi's user-level skills directory. Run these commands from the repository root:

```powershell
$skillsDir = Join-Path $HOME ".pi\agent\skills"
New-Item -ItemType Directory -Force -Path $skillsDir | Out-Null
Copy-Item -Path ".\pi-skills\*" -Destination $skillsDir -Recurse -Force
```

**Note:** `-Force` can replace files with matching names in your existing Pi skills directory. If you already use other skills, inspect or back up that directory before running the copy command. If `pi-skills/` is missing from the repository, add it before continuing.

## 4. Install the subagent extension (optional)

Subagents are optional. Install the extension only if you plan to use it:

```powershell
pi install npm:pi-subagents
```

MantiqAI should solve directly by default and use extra agents only when they provide a clear benefit. See `AGENTS.md` for the intended workflow.

Package page: https://www.npmjs.com/package/pi-subagents

## 5. Configure a model provider

Start Pi:

```powershell
pi
```

Inside Pi, use `/login` and follow the prompts for your chosen provider. Use `/model` to select an available model. Provider and model options can change, so choose from the options Pi shows you.

Pi may save credentials in user-level configuration, including `~/.pi/agent/auth.json`. **Never commit API keys, credentials, `auth.json`, session data, or private configuration to this public repository.**

Provider guide: https://pi.dev/docs/latest/providers

## 6. Build the Docker sandbox

Make sure Docker Desktop is running. From the repository root:

```powershell
Set-Location .\sandbox-setup
docker build -t mantiqai-sandbox -f Dockerfile .
Set-Location ..
```

This builds the local image named `mantiqai-sandbox`. If the build fails, read the Docker error and confirm Docker Desktop is running before retrying.

## 7. Run MantiqAI

From the repository root, start Pi:

```powershell
pi
```

Pi can use this repository's `AGENTS.md` for general behavior. Use `CHALLENGE_ROUTER.md` to choose category-specific guidance and relevant skills.

## Security and configuration

- Use MantiqAI only in authorized competitions, educational labs, and explicitly scoped environments.
- Keep credentials, session data, personal configuration, and private files out of the repository.
- Review third-party skills and dependencies before relying on them.
- Before committing, review `git status` and the staged diff for secrets, private information, or unrelated files.
