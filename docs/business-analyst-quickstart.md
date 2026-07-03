# Business analyst quickstart (non-technical)

This guide is for business-focused analysts who want to explore the demo with minimal technical setup.

## What you can do in 30 minutes

1. Open the existing Report 01 and understand the business story.
2. Ask an AI agent to explain KPIs, pages, and modeling choices in plain language.
3. Reuse the same workflow for your own domain later.

## Option A: Claude Code path

1. Open this repository in Claude Code.
2. Ask: `Review this project as a business analyst and explain the analytics cycle steps from data to dashboard.`
3. Ask: `Show me where the KPI definitions and business meaning are documented.`
4. Ask: `Create a simple plan for a new report in my domain using the same folder contract.`

Reference video: [How to use Claude Code (YouTube)](https://www.youtube.com/watch?v=lEqekp9MFcA)

## Option B: GitHub Copilot path

1. Open this repository in GitHub Copilot Chat (VS Code, GitHub, or Copilot CLI).
2. Ask: `Summarize this repo as an AI-agent analytics demo, not only a banking use-case.`
3. Ask: `Guide me through report-lifecycle.md in business terms.`
4. Ask: `Draft requirements for a new report folder using reports/NN-name template.`

## Suggested first prompts (business-friendly)

- `Explain the difference between dataset contract, model design, and report spec with examples from Report 01.`
- `Which metrics in this demo are best for executive updates vs operational monitoring?`
- `If I move from banking to retail, what should I keep and what should I replace?`

## Where to read next

- Lifecycle: [report-lifecycle.md](report-lifecycle.md)
- Environment and deployment targets: [fabric-environment.md](fabric-environment.md)
- Report registry: [../reports/README.md](../reports/README.md)
