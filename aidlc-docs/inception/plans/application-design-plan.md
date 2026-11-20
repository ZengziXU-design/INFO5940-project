# CourseCompass Application Design Plan

**Status:** Answers validated. Design artifacts generated and ready for team review.

**Basis:** [Approved execution plan](execution-plan.md), [requirements](../requirements/requirements.md), [user stories](../user-stories/stories.md), [personas](../user-stories/personas.md), and the [technical environment](../../../docs/technical-environment.md), reviewed at f36a8ed.

## Design scope

The planning view opens after the student confirms the interpreted profile. When a verified recommendation is available, it prefills one weekly timetable and shows individually recommended courses. Each card can be inspected and tried as a possible edit. Drag and click initiate the same preview. The preview must show the affected courses, times and credits. A committed timetable always meets confirmed constraints. Students can change a pinned course only by explicitly changing the underlying requirement.

Use the planned local Streamlit and FastAPI setup. Propose a small set of components for the web UI, session and application orchestration, profile interpretation, Cornell data and evidence, deterministic ranking and search, and validation of proposed edits. Consider shared domain types where the UI and service need the same semantics for section packages, evidence, and outcomes. Keep the LLM behind a validated boundary and use deterministic code for course-package compatibility, feasibility, preview calculation and final commit. Course details remain reachable from either pane. Preserve the distinction between Cornell facts, student commentary, workload estimates and missing evidence.

Specify responsibilities, method inputs and outputs, error and result variants, and component boundaries. Include versioning for preview and commit so a pending preview cannot be applied after the active timetable or profile changes. Define result states that distinguish a valid timetable, proven infeasibility, unresolved required-course eligibility, incomplete data, and a bounded search with no result. Show how a locally imported text PDF yields student-confirmed course identifiers while the raw transcript stays local and transient. Cover fixture and mock behavior as part of the same service contracts.

## Focused design decisions

Enter the letter after each [Answer]: or send a reply such as Q1=A, Q2=A, Q3=A. For Other, describe the intended behavior.

## Question 1 — Replacement scope

If trying one alternative would require removing more than one currently scheduled course, how should the first version respond?

A) Support adding one course or swapping one course at a time. Show all conflicts. Offer a compatible section package or a valid one-for-one swap when available. If a candidate would require several removals, explain that and leave the timetable unchanged. Recommended for a clear preview and a smaller scope.

B) Let one candidate replace several conflicting electives in a single action when the resulting timetable is feasible. List every affected course and the credit change in one preview, then require explicit confirmation. Pinned courses remain protected.

C) Other (please describe after the answer tag).

[Answer]: A

## Question 2 — Keep or replace a hand-edited timetable after a chat update

When the student asks for a soft preference change after manually editing the timetable, how should the application apply the new recommendation?

A) Consider the current timetable during recalculation and keep it active while the student reviews the proposed changes. Apply the new recommendation only after acceptance, while preserving confirmed hard constraints. Recommended because the student can keep manual edits without losing access to a fresh recommendation.

B) Replace the active timetable as soon as a newly computed recommendation passes validation after the student confirms the profile change. Display what changed and preserve all confirmed hard constraints. This gives faster results, while previous unpinned choices may change.

C) Other (please describe after the answer tag).

[Answer]: A

## Question 3 — Owner of the current plan and pending preview

Where should the local application store the confirmed profile, active timetable, and pending edit during the planning session?

A) Keep them in memory in the local FastAPI service, keyed by a local session identifier. Streamlit renders the current state and sends actions with its revision. Persist user preferences only on explicit save. Recommended because preview and commit share one source of truth.

B) Keep the working state in the Streamlit session. Send the current plan, confirmed profile, and edit to a stateless FastAPI validator for each preview and commit. Persist user preferences only on explicit save. This keeps server lifecycle simpler and requires the UI to carry the full validated state more often.

C) Other (please describe after the answer tag).

[Answer]: A

## Answer analysis

Q1, Q2 and Q3 all select A. These choices are compatible. Both a manual edit and a chat recommendation use a versioned proposal and explicit confirmation. The local service owns the revision and can protect the active plan. Q2 applies to soft preferences. Confirmed hard-constraint changes always revalidate the active plan. No clarification is required.

## Execution checklist

- [x] Reload the approved execution plan, requirements, stories, personas, input documents, and workflow rules.
- [x] Identify component, method contract, service orchestration, dependency and interaction decisions.
- [x] Prepare focused questions about changes that affect both the UI and the service.
- [x] Collect and validate the answers, and resolve any ambiguity.
- [x] Produce `../application-design/components.md` with component purposes, responsibilities, and interface boundaries.
- [x] Produce `../application-design/component-methods.md` with typed method contracts, validation responsibilities, and outcomes.
- [x] Produce `../application-design/services.md` with the intake, initial recommendation, swap, conversational revision, evidence and fallback flows.
- [x] Produce `../application-design/component-dependency.md` with direction of dependencies, a matrix, and validated data-flow diagrams with text alternatives.
- [x] Produce `../application-design/application-design.md` as the consolidated design with requirement and story traceability.
- [x] Validate consistency against F1–F8, FR-01–FR-10 and US-01–US-13. Check preview cancellation, pinned courses, stale revisions, an infeasible result, missing evidence and the labeled model or roster fallback.
- [x] Present the artifacts for team review.
- [ ] Record explicit Application Design approval before Units Generation.

## Inception boundary

Design contracts and interactions here. Functional design, implementation, package installation and paid model calls occur after the midterm. Property-Based Testing remains Partial. Carry Hypothesis, schedule invariants and round-trip verification forward to the relevant Construction stages. Keep Units Generation in scope and stop after its approval.
