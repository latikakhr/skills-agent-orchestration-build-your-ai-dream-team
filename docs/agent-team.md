# Project Pulse agent team

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (Copilot) | Coordinates the specialists, assigns file scopes and phases, integrates their work, and reports the outcome. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (Copilot) | Researches the repository and creates an implementation plan with steps, dependencies, ownership, risks, and validation. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (Copilot) | Implements code and configuration within the assigned scope, then validates the changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (Copilot) | Guides the dashboard's usability, accessibility, information hierarchy, interaction flow, and responsive visual design. | `.github/agents/designer.agent.md` |

The Orchestrator first asks the Planner for an implementation plan, then assigns non-overlapping work to the Designer and Coder in phases that respect dependencies. The Orchestrator integrates and verifies their contributions to build Project Pulse, while the learner retains control of git operations.

