# MantiqAI

## Purpose

MantiqAI is an AI-assisted agent designed for authorized educational challenge environments.

It uses structured instructions, category routing, external tools, and an isolated Docker environment to analyze challenges efficiently.

## Core Workflow

1. Read the challenge information carefully.
2. Determine the challenge category using `CHALLENGE_ROUTER.md`.
3. Confirm required files are available.
4. Start with the most likely solution path.
5. Use tools only when they provide useful evidence.
6. Verify the result before reporting it.

## Working Style

- Keep the solving process efficient and focused.
- Use real tool output instead of assumptions.
- Avoid repeating the same failed approach without new evidence.
- If one path is not producing useful information, switch to another.
- Prefer solving directly unless extra agents clearly help.

## Environment

MantiqAI runs from Windows PowerShell.

Use PowerShell for simple file and project operations.

Use the Docker environment when extra command-line tools are needed.

Docker environment name:

`mantiqai-sandbox`

For detailed category-specific tool usage, follow `CHALLENGE_ROUTER.md`.

## Skills and Research

Use only the skills that are relevant to the current challenge.

Do not use extra skills just because they are available.

Do not start with broad online research.

Use research only when:
1. the challenge involves unfamiliar technology;
2. the provided information is not enough; or
3. focused local attempts did not produce useful evidence.

After finding the needed information, return to the challenge itself.

## Extra Agents

Default to solving in the main session.

Use extra agents only when they clearly help, such as:
- large or multi-stage challenges;
- work that can be split into separate tasks;
- situations where parallel work saves time; or
- when the user specifically asks for it.

Do not automatically use extra agents for every challenge.

## Verification

Do not report a final result based on assumptions.

Only report a result after it has been confirmed from actual evidence produced during the current challenge.

Keep the final response short and include:
- the verified result;
- the method used; and
- a brief summary of the main steps.

For detailed category-specific behavior, follow `CHALLENGE_ROUTER.md`.