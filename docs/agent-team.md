# Agent team

For Mona's Project Pulse dashboard, I will use a four-agent custom team defined under `.github/agents/` and coordinated through GitHub Copilot CLI in a Codespace.

- Planner — model: Claude Opus 5.5 (copilot)
  - Responsibility: research the repo, evaluate requirements, identify constraints and edge cases, and produce the implementation plan that the orchestrator can turn into execution phases.
  - Definition: `.github/agents/planner.agent.md`

- Orchestrator — model: Claude Opus 5.5 (copilot)
  - Responsibility: break the plan into workstreams, assign files and tasks to specialists, coordinate parallel vs. sequential execution, and verify that the integrated dashboard result fits together cleanly.
  - Definition: `.github/agents/orchestrator.agent.md`

- Designer — model: Gemini 3.7 Flash (copilot)
  - Responsibility: shape the dashboard experience, UX, visual hierarchy, accessibility, information architecture, and interactive polish for the Project Pulse frontend.
  - Definition: `.github/agents/designer.agent.md`

- Coder — model: GPT-5.5 (copilot)
  - Responsibility: implement the logic, fix bugs, support runnable app setup, and make the assigned code changes within the scope defined by the orchestrator.
  - Definition: `.github/agents/coder.agent.md`

This setup uses GitHub Copilot CLI in a Codespace as the orchestration layer, with the Orchestrator delegating specialist work to Planner, Designer, and Coder while keeping the end-to-end build aligned with the Project Pulse goals.
