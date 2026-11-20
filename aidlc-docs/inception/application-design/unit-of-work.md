# CourseCompass Unit of Work

**Status:** Generated for team review.

[Unit plan](../plans/unit-of-work-plan.md) · [Dependencies and integration](unit-of-work-dependency.md) · [Story ownership](unit-of-work-story-map.md) · [Application design](application-design.md)

## U01 · Local CourseCompass application

The application is one greenfield local release. Its Streamlit interface and FastAPI process run together on a team laptop with loopback access. Catalog data and explicitly saved soft preferences may use local SQLite. Cornell's published data and the selected inference provider are accessed through adapters with labeled local alternatives. Five internal modules below are separately scoped and testable.

**Release outcome.** A student confirms a conversational planning profile, sees one verified weekly timetable and individual alternative courses, inspects details, and previews and confirms an addition or one-for-one swap. A preference-only chat revision proposes a new plan while keeping the hand-edited plan active. The application preserves pinned courses and other hard constraints, distinguishes reasons for a missing result, and labels the local demo path.

**Release acceptance.** On a single laptop, run the end-to-end scenario using deterministic fixtures and a mock model ahead of any live validation. Confirm term, credits, eligibility, required meeting components, pins and exclusions, no overlaps, source attribution and no silent mutation. Exercise stale and canceled previews, an unsupported transcript, missing review evidence and an infeasible fixture. The team confirms the API budget and owner before the first paid model call, as specified in Requirements Analysis.

## Module work packages

| Module | Responsibility and delivered interfaces | Acceptance check | Planned source boundary |
| --- | --- | --- | --- |
| M1 · Course catalog and evidence | List accessible published terms; normalize selected-term courses and compatible section packages; expose stable IDs, course facts, attributed evidence, available data quality, and labeled local snapshots. Manage the local catalog cache. | A fixture with two terms excludes cross-term sections, identifies incomplete combinations, and returns facts even when reviews are unavailable. | data/ |
| M2 · Student profile and conversation | Produce profile drafts, clarify missing fields, local completed-course import, validated preference updates, saved soft preferences and grounded explanations. Accept confirmed IDs and offer manual entry for unsupported PDFs. | A mock intake plus synthetic transcript yields a confirmable profile. Only supported preference fields persist by explicit save. Raw transcripts stay local and transient. | agent/ and local import and preference adapters |
| M3 · Recommendation and feasibility | Normalize the six weights, rank the verified initial plan and individual alternatives, build add/swap previews, and independently validate feasible schedules. Keep blocked pins, uncertain eligibility, proven infeasibility and exhausted search distinguishable. | Synthetic fixtures exercise one-to-one replacements, multiple conflicts, preserved pins and invalid components. Exhaustive comparison on small instances checks the solver and validator. | scoring/ and optimizer/ |
| M4 · Planning service and local API | Own the FastAPI-session profile, timetable, revision and pending preview. Orchestrate catalog, conversation, engine and fallback, expose typed API operations, guard commits and maintain demo provenance. | A canceled or stale confirmation leaves the active timetable unchanged. Confirming a valid edit updates it once. Chat preference changes keep the edited plan active until acceptance. Labeled fixture and mock paths exercise the integrated service. | api/ and internal planning and session code |
| M5 · Timetable and course interface | Render conversation, confirmation, the weekly view, individual course cards, detail and evidence, and drag/click preview controls. Show conflicts and other result states. | Drag and click generate equivalent preview requests. The view shows verified meeting blocks and visible source, conflict and demo labels. Changes to the active plan appear after successful confirmation. | ui/ |

## Greenfield code organization

Reuse the [technical environment's planned structure](../../../docs/technical-environment.md). Share scheduling data types in src/coursecompass/domain/. Keep provider adapters at the owning module boundary and the in-memory session with the local planning service. Use src/coursecompass/security/ for reusable input and provenance helpers where needed. Put each module's tests under tests/ and shared fixture and service integration tests alongside them. Keep Cornell-style synthetic data and labeled demo cases under fixtures/. Source, test and configuration files belong at the repository root during Construction.

## Cross-module acceptance

- M1 and M3 agree on course, term, credit and package identity. M3 consumes domain records independently of provider calls.
- M2 returns typed profile proposals. M4 controls confirmation and revision, and M5 displays the confirmed constraints and ambiguity prompts.
- M3 validates a proposed timetable. M4 admits only a current, validated proposal, and M5 reflects its new state. This applies to the initial recommendation, course swaps and chat recalculation.
- M1 and M2 provide the fixture and model-mock inputs. M4 returns provenance and outcomes, and M5 makes these visible in the demonstration.
