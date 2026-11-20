# CourseCompass Unit of Work Plan

**Status:** Planning decisions recorded and artifacts prepared under the team representative's instruction to complete the milestone directly. The generated units are available for team review.

**Inputs:** [Approved design](../application-design/application-design.md), [component responsibilities](../application-design/components.md), [method contracts](../application-design/component-methods.md), [service flows](../application-design/services.md), [component dependencies](../application-design/component-dependency.md), [requirements](../requirements/requirements.md), and [user stories](../user-stories/stories.md).

The team representative authorized direct generation and delegated open decomposition choices. No separate review of this plan was recorded. The decisions below come from the approved design and the documented local deployment constraint or are agent-selected planning defaults. This keeps every decision explicit without attributing answers to individual teammates.

## Decomposition decisions

| Category | Decision and basis |
| --- | --- |
| Story grouping | Group stories by the component that supplies their primary acceptance behavior. Identify one accountable module per story plus any required supporting module. This follows the approved component boundaries. |
| Dependencies | Agree on shared domain and port contracts first. Build catalog, profile and engine modules in parallel against fixtures, then integrate through the local API. The timetable UI can start with stubbed API responses. |
| Team alignment | Define logical work packages with acceptance checks. Team members assign themselves during Construction. The existing shared-session and review arrangement remains the basis for collaboration. |
| Technical considerations | One local deployment unit contains the Streamlit and FastAPI processes and local storage. It uses provider adapters and fixture paths. The team can develop and test modules separately within that release. |
| Business domain | Separate official offerings and evidence, confirmed student intent, deterministic planning, versioned orchestration, and interaction. Published sources and generated text cannot change confirmed state. |
| Code organization | Use the approved Python package under src/coursecompass/ with tests and fixtures at the repository root. Keep AI-DLC documentation under aidlc-docs/. Add application files during Construction. |

## Execution checklist

- [x] Load the approved design and confirm a greenfield local deployment.
- [x] Resolve grouping, dependencies, ownership boundaries, deployment and code organization using the approved design and authorized planning defaults.
- [x] Record the request for direct generation and the absence of a separate unit-plan review.
- [x] Generate [unit-of-work.md](../application-design/unit-of-work.md) with deployment scope, module responsibilities and greenfield code organization.
- [x] Generate [unit-of-work-dependency.md](../application-design/unit-of-work-dependency.md) with internal and external dependencies, sequencing and integration checkpoints.
- [x] Generate [unit-of-work-story-map.md](../application-design/unit-of-work-story-map.md) and verify each story, functional requirement and planned demo flow is covered.
- [x] Validate dependency direction, check cross-links and diagram references, and verify that the repository contains Inception documents only.
- [ ] Record team review and approval of the generated units.

## Milestone stop

The generated unit artifacts complete the repository deliverable set. After the team approves the units, record the approval and close Inception. Construction begins separately for the final project.
