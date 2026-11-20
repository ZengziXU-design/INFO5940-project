# CourseCompass Unit of Work Story Map

[Approved stories](../user-stories/stories.md) · [Requirements](../requirements/requirements.md) · [Unit and modules](unit-of-work.md) · [Dependencies](unit-of-work-dependency.md)

All 13 stories belong to the local product unit U01. Each row identifies the module that owns the primary outcome, plus contributing modules where an interface or integration check is needed.

| Story | Requirement | Primary module | Contributing modules | Acceptance checkpoint |
| --- | --- | --- | --- | --- |
| US-01 Confirm the interpreted request | FR-01 | M2 | M4, M5 | Parse one term, credits, time restrictions and weekly study budget. Ask about soft versus hard constraints. Show a reviewable profile before confirming. |
| US-02 Enter and verify completed courses | FR-02 | M2 | M4, M5 | Let the student edit the manual list; keep repeat eligibility distinct from confirmed exclusions. |
| US-03 Import completed courses from a local PDF | FR-02 | M2 | M4, M5 | Use local extraction and student confirmation for a text PDF. Unsupported files take the manual path. Captured provider requests and logs exclude raw content. |
| US-04 Use offerings for the selected term | FR-03 | M1 | M3, M4, M5 | Return supported selected-term records, provenance and missing or incomplete section indicators. |
| US-05 Adjust ranking preferences | FR-05 | M3 | M2, M4, M5 | Normalize six editable weights and rank with available evidence. The preference update keeps confirmed hard constraints. |
| US-06 Restore my own preferences | FR-05 | M2 | M4, M5 | Restore only explicitly saved local preferences or start with defaults. |
| US-07 Start with one validated timetable | FR-06, FR-07 | M3 | M1, M4, M5 | Select one best verified plan found. Show official meeting blocks and correct credits. Reject incomplete or conflicting packages and surface unresolved eligibility. |
| US-08 Try individual alternatives against the current timetable | FR-06, FR-08 | M4 | M1, M3, M5 | Present individual alternatives. Both input gestures use the same preview. Valid confirm updates once and refreshes candidates. Canceled, invalid and stale changes preserve the existing plan. |
| US-09 Inspect a course and its sources | FR-04, FR-08 | M5 | M1, M2, M4 | Both entry points open the same facts, rationale, attributed review if available, and an explicit missing-data cue. |
| US-10 Revise a plan without losing earlier constraints | FR-09 | M4 | M2, M3, M5 | Soft chat edits retain the hand-edited timetable until the student accepts a new one. Hard changes need explicit profile confirmation and revalidation. |
| US-11 Resolve an unavailable required course | FR-07 | M3 | M1, M2, M4, M5 | Identify the blocked required course or eligibility question. No swap silently discards a pin. |
| US-12 Understand why no schedule was found | FR-07 | M3 | M1, M4, M5 | Distinguish a verified infeasible case, missing data and an exhausted search. Offer user-controlled adjustments. |
| US-13 Demonstrate using a labeled fallback | FR-10 | M4 | M1, M2, M3, M5 | Compose labeled roster fixtures and model mocks and reproduce recommendation, swap, chat revision and failure outcomes. |

## Cross-cutting acceptance

| Requirement | Module accountability | Planned evidence |
| --- | --- | --- |
| NFR-01 Correctness | M3 validator and M4 commit guard | Generated invariants plus small exhaustive checks and stale-request integration cases. |
| NFR-02 Privacy and safety | M2 local import and minimized inference, M4 loopback and state | Synthetic PDF and mock provider checks. Explicit preference saves only. |
| NFR-03 Provenance | M1 source records, M5 presentation | Fixture comparison for official, curated, stale and unavailable evidence. |
| NFR-04 Reliability and responsiveness | M1, M2 and M3 bounded work, M4 outcomes | Stub failure, search-limit and labeled fallback demonstrations. |
| NFR-05 Usability | M5 with M4 | Walk through confirmation, one filled timetable, inspect and swap gestures, and failure feedback. |
| NFR-06 Reproducibility and testability | All modules through M4 and M5 | Stable fixture startup and scripted mock outcomes. Partial PBT uses pytest and Hypothesis in applicable Construction stages. |
