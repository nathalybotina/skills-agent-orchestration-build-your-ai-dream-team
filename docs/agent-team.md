# Agent team

The Mona's Project Pulse dashboard team is coordinated through GitHub Copilot CLI in a Codespace:

- **Orchestrator** — Uses **Claude Opus 5.5 (copilot)** to coordinate the work, delegate tasks to the specialist agents, manage dependencies and file ownership, and verify the integrated result. Definition: `.github/agents/orchestrator.agent.md`
- **Planner** — Uses **Claude Opus 5.5 (copilot)** to research the repository and requirements, identify risks and edge cases, and produce an ordered implementation plan without writing code. Definition: `.github/agents/planner.agent.md`
- **Coder** — Uses **GPT-5.5 (copilot)** to implement code, fix bugs, create assigned runnable-app support files, and validate deterministic, testable behavior. Definition: `.github/agents/coder.agent.md`
- **Designer** — Uses **Gemini 3.7 Flash (copilot)** to shape the dashboard's UI/UX, accessibility, information hierarchy, interaction flow, responsive behavior, and visual clarity. Definition: `.github/agents/designer.agent.md`

The Orchestrator starts with the Planner, then assigns implementation and design work to the Coder and Designer in parallel where their file scopes allow, and finally verifies that the complete dashboard works together.
