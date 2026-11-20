# CourseCompass Application Design

**Status:** Design presented at cb3d20b. The team representative authorized continuing to Units Generation. Q1=A, Q2=A, Q3=A are recorded in the [design plan](../plans/application-design-plan.md).

**Basis:** [Approved requirements](../requirements/requirements.md), [user stories](../user-stories/stories.md), and [technical environment](../../../docs/technical-environment.md).

## Student experience

CourseCompass runs locally and opens with a conversation. The student sees and confirms the interpreted profile. With accessible term data and a verified recommendation, the planning page opens with one prefilled weekly timetable. A side panel shows individually recommended courses. Their usefulness comes from their fit with the student's profile and active plan, and the courses can overlap one another. Selecting a class in the timetable or side panel opens the same course detail view with official information, recommendation rationale, attributed evidence and visible data gaps.

A student can drag an alternative or click an action to preview adding it or swapping one scheduled course. The preview shows conflicting meetings and the affected courses, candidate section package, and resulting credits. A change applies only after the student confirms it and the resulting plan passes validation. An edit that would remove a pinned course is blocked while the hard requirement is in effect.

## Recorded decisions

| Question | Design decision |
| --- | --- |
| Q1 Replacement scope | One incoming course and at most one outgoing course in a manual edit. A compatible section may avoid the conflict. A candidate that needs multiple removals is clearly blocked. |
| Q2 Conversational revision | Keep the hand-edited timetable active after a soft preference update. Use it during recalculation, and present suggested changes for explicit acceptance. A confirmed hard-constraint change can invalidate the old schedule. |
| Q3 Working state | FastAPI owns the confirmed profile, active timetable and pending preview in a local in-memory session. Streamlit renders that state. Only explicitly saved soft preferences persist as student data. |

For example, a 14-credit plan has one pinned seminar and a 3-credit elective. A similarly relevant 3-credit candidate overlaps the elective. The preview lists the conflicting elective and shows a 14-credit result. Canceling holds the original plan. Confirming swaps only that elective if the resulting plan passes all checks. If a new candidate also overlaps the pinned seminar, its card remains inspectable and the edit is blocked.

## Design documents

| Document | Details |
| --- | --- |
| [Components](components.md) | Responsibilities and boundaries for Streamlit, FastAPI, local sessions, planning services, Conversation, Cornell data and evidence, scheduling, transcript import, and preference storage. |
| [Component methods](component-methods.md) | Typed domain and service contracts and planned local API routes. |
| [Services](services.md) | Confirmation, initial recommendation, course edits, chat revisions, evidence, failure cases and local fallback. |
| [Component dependencies](component-dependency.md) | Dependency matrix, provider boundaries and data-flow diagrams with text equivalents. |

## Traceability

| Feature | Requirements | Stories | Design behavior |
| --- | --- | --- | --- |
| F1 | FR-01 | US-01 | Conversation provides a profile draft and clarifying questions before the student confirms it. |
| F1 | FR-02 | US-02, US-03 | Manual and local PDF course-ID entry share one verified input path. |
| F2 | FR-03 | US-04 | Term-scoped Cornell normalization yields supported section packages and provenance. |
| F3 | FR-04 | US-09 | Details use attributable evidence and mark missing or uncertain claims. |
| F4 | FR-05 | US-05, US-06 | Normalize six editable weights and restore only explicitly saved local preferences. |
| F5 | FR-06, FR-07 | US-07, US-08, US-11, US-12 | One verified result, individually ranked alternatives, separate feasibility checks and distinct failure outcomes. |
| F6 | FR-08 | US-08, US-09 | Prefilled timetable, editable using preview and confirmation, with details from each course area. |
| F7 | FR-09 | US-10 | A follow-up applies only confirmed changes and proposes a new plan while the valid current plan remains active for soft revisions. |
| F8 | FR-10 | US-13 | Labeled local data and mock model path support reproducible scenarios. |

NFR-01 is addressed through independent validation, a typed result model and revision-checked commits. NFR-02 uses loopback services, a local-only transient PDF path, minimized model inputs and explicit preference saves. NFR-03 makes source and uncertainty metadata part of course details and explanations. NFR-04 carries source and search limits through to the UI. NFR-05 includes textual conflict cues, click and drag actions, and visible course-change consequences. NFR-06 specifies fixture and provider interfaces, deterministic checks, and tests planned for Construction.

## What the team should review

- The initial grid shows one verified plan and a separate list of course alternatives. Course details can be opened from either location.
- Canceling a proposal, trying to remove a pin or confirming a stale proposal leaves the active timetable intact. A valid confirmation updates the schedule and refreshes the individual alternatives.
- Chat can revise the preference profile without discarding the student's edits. The user sees and accepts a new recommendation before it replaces an active feasible timetable under the same hard constraints.
- The UI can distinguish missing evidence, an unresolved required course, catalog coverage gaps, proven infeasibility, and a bounded search without a verified result. Demo data and mock inference are labeled.

## Validation planned for Construction

Use small fixtures and exhaustive enumeration to verify the solver objective and feasibility. Use Hypothesis with pytest for profile serialization round-trips and feasibility invariants for section packages, credit bounds, pin retention, and nonoverlap. Include domain generators and reproducible shrinking. Use example-based tests for a conflict that spans lecture and discussion meetings, cancellation, stale and consumed preview IDs, and a conversation revision that preserves a locked course. Validate model interpretation with labeled messages and a mock model. Start by checking calendar integration for the required drag and click behavior in the existing local UI stack.

Property-Based Testing remains Partial. PBT-02, PBT-03, PBT-07, PBT-08 and PBT-09 are N/A to the Inception Application Design review and carry forward to their applicable Construction stages. Security and Resiliency Baseline extensions remain disabled, with the ordinary privacy and failure-handling requirements still in effect.

This stage defines application boundaries and behavior. Detailed search budgets, scoring calibration, model calls and runtime integration are verified during Construction. Upon explicit approval of this design, proceed to Units Generation. The midterm stops after the units are approved.
