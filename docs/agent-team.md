# Agent team

I will use GitHub Copilot CLI in a Codespace to build Mona's Project Pulse
dashboard with this custom agent team. Each agent currently uses the `Auto`
Copilot model target:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Planner** | Auto (copilot) | Researches the repository, documentation, dependencies, and edge cases, then gives the Orchestrator a phased implementation plan with file ownership, dependencies, and validation expectations. Plans only; does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Auto (copilot) | Defines the dashboard's accessible, responsive user experience and visual direction. For Project Pulse, focuses on polished project cards, status badges, priority treatment, readable spacing, and consistent styling hooks. | `.github/agents/designer.agent.md` |
| **Coder** | Auto (copilot) | Implements assigned application logic and support configuration with clear, testable code and explicit errors. When assigned runnable-app work, makes Project Pulse straightforward to launch and preview, including the specified VS Code launch configuration. | `.github/agents/coder.agent.md` |
| **Orchestrator** | Auto (copilot) | Coordinates the specialists: converts the plan into phases, assigns explicit file scopes, schedules independent work in parallel and dependent work sequentially, integrates the result, and reports it. Coordinates but does not implement. | `.github/agents/orchestrator.agent.md` |
