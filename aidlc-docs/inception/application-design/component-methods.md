# CourseCompass Component Methods

[Components](components.md) · [Services](services.md) · [Dependencies](component-dependency.md)

These are planned Python-style contracts. Exported API types are validated with Pydantic. Domain structures and detailed algorithms will be refined during Functional Design.

## Shared request and result types

| Type | Key fields and interpretation |
| --- | --- |
| StudentProfile | Term ID, credit bounds, pinned and excluded course IDs, unavailable intervals, confirmed course history and eligibility, normalized soft weights, interests, and weekly hours outside class. |
| ProfileDraft | Proposed changes, missing fields, and ambiguities, including which constraints need explicit confirmation. |
| Course and SectionPackage | Stable term and course identity. A package contains compatible required components, their meeting intervals, credits, eligibility flags and source references. |
| CatalogSnapshot | Snapshot ID, selected term, sections and course metadata, source and retrieval time, coverage flags, and fixture provenance if applicable. |
| Schedule | Selected section-package IDs, total credits, verified meetings, score and evidence coverage. A plan can be active only after deterministic validation. |
| AlternativeCard | Course and candidate package IDs, reason and evidence coverage, fit and conflicts relative to the active timetable. Cards are independently ranked and can overlap one another. |
| CourseEdit | One incoming course and, for a swap, at most one outgoing course. Section ID may be supplied or selected from verified compatible packages during preview. |
| ChangePreview | Edit ID, base revision and snapshot ID, added and removed courses, time and credit delta, conflicts and feasibility status. Preview variants cover a one-course edit and a conversational suggestion. |
| SessionView | Session ID, revision, confirmed profile, active schedule if available, individual alternatives, pending preview or recommendation, result messages and provenance. |
| PlanningOutcome | A validated feasible result, proven infeasibility, a required-course clarification, incomplete data, or search limit reached. A verified incumbent may accompany an exhausted search and carries that limitation. |
| Error | Invalid request, stale revision or preview, blocked edit or missing local session. Stable reason codes and displayable detail accompany errors. |

## Planned interfaces

| Component | Method | Contract |
| --- | --- | --- |
| Timetable UI | render(view: SessionView) -> None | Present a single active weekly timetable, individual cards, course details and preview state. Conflict styling includes text. |
| Timetable UI | request_edit(course_id: CourseId, revision: Revision) -> CourseEditRequest | Map drag and click to one edit command. Official section meetings determine placement. |
| Local API | handle(request: ValidatedRequest) -> SessionView or OperationOutcome | Reject malformed payloads, forward the intended operation and map domain outcomes to typed responses. |
| Planning Service | start_session() -> SessionView | Open an empty local session and expose editable default weights. Local preferences can be restored explicitly. |
| Planning Service | interpret(session_id: SessionId, revision: Revision, message: str) -> ProfileDraft | Request clarification or return a proposed profile patch. Empty, invalid or ambiguous model responses leave the confirmed profile unchanged. |
| Planning Service | confirm_profile(session_id: SessionId, revision: Revision, draft: ConfirmedInput) -> SessionView | Accept only explicit user changes, including any hard-constraint change. Accept verified course IDs or manual edits in this path. |
| Planning Service | recommend(session_id: SessionId, revision: Revision) -> PlanningOutcome | Select and validate one feasible starting plan when available. If a compatible active plan is already present, keep it active and stage the fresh proposal for review. |
| Planning Service | preview_edit(session_id: SessionId, revision: Revision, edit: CourseEdit) -> ChangePreview | Compute affected courses, times and credits. Block edits requiring multiple removals or removing pinned courses. |
| Planning Service | confirm_change(session_id: SessionId, revision: Revision, preview_id: PreviewId) -> SessionView or Error | Revalidate the server-side preview against the current profile and snapshot. Commit atomically only if revision and preview ID still match and the result is feasible. |
| Planning Service | cancel_change(session_id: SessionId, revision: Revision, preview_id: PreviewId) -> SessionView or Error | Clear the matching pending preview while preserving active selection and credits. |
| Planning Service | course_detail(session_id: SessionId, course_id: CourseId) -> CourseDetails | Resolve both timetable and candidate cards against the same selected term. Preserve official facts, source categories and missing evidence flags. |
| Planning Service | restore_preferences(session_id: SessionId, revision: Revision) -> SessionView | Load the user's explicitly saved soft preferences into the draft for review. |
| Planning Service | save_preferences(session_id: SessionId, revision: Revision) -> SavedPreferences | Persist the confirmed soft preferences only when requested. |
| Session Store | read(session_id: SessionId) -> SessionView | Return a snapshot for computation against a known revision. |
| Session Store | transition(session_id: SessionId, expected: Revision, guard: PreviewGuard, change: StateTransition) -> SessionView or Error | Check revision and pending ID atomically. State changes atomically replace the revisioned view. Pending preview identity can change independently of the active revision. |
| Conversation | interpret_message(message: str, context: MinimizedContext) -> ProfileDraft | Use validated, allowlisted inference. Return proposed fields and questions. |
| Conversation | explain(facts: ExplanationInput) -> Explanation | Explain a validated result with cited evidence and limitations. A deterministic template remains available. |
| Catalog and Evidence | list_terms() -> TermListing | Return accessible published terms and the source or offline status. |
| Catalog and Evidence | load_term(term_id: TermId, mode: SourceMode) -> CatalogSnapshot or SourceFailure | Retrieve and normalize the selected term within bounded limits. Return a snapshot or explicit data failure. |
| Catalog and Evidence | details(snapshot: CatalogSnapshot, course_id: CourseId) -> CourseDetails | Resolve official course facts and optional attributed evidence. |
| Schedule Engine | normalize_weights(input: PreferenceWeights) -> NormalizedWeights or InputIssue | Require nonnegative finite inputs. An all-zero submission prompts for a positive weight or restoring defaults. |
| Schedule Engine | search(profile: StudentProfile, snapshot: CatalogSnapshot, current: Schedule or None, budget: SearchBudget) -> PlanningOutcome | Rank feasible results under the approved criteria. Use the current combination as context for a preference revision. Label bounded-search results. |
| Schedule Engine | alternatives(profile: StudentProfile, snapshot: CatalogSnapshot, schedule: Schedule) -> list[AlternativeCard] | Independently rank relevant individual courses, with current-timetable conflict text and remaining evidence coverage. |
| Schedule Engine | check(profile: StudentProfile, snapshot: CatalogSnapshot, schedule: Schedule) -> ValidationResult | Check term, credit bounds, pins/exclusions, eligibility, complete compatible components, duplicate courses and meeting conflicts. |
| Schedule Engine | preview(profile: StudentProfile, snapshot: CatalogSnapshot, schedule: Schedule, edit: CourseEdit) -> EditResult | Calculate one proposed edit, identify all conflicts and possible verified sections, and return a validation result with the exact changes. |
| Completed-course Import | extract(pdf: LocalPdf) -> CourseIdDraft or ManualEntryNeeded | Parse a local text-based document and return only identifiers. |
| Preference Store | save(preferences: SavedPreferences) -> None | Save supported soft fields in local storage after explicit confirmation. |
| Preference Store | load() -> SavedPreferences or None | Return the current user's local profile if one was saved. |

## Local API operations

Use JSON under the planned /api/v1 prefix. The Streamlit process invokes completed-course extraction locally and sends only confirmed course identifiers to the API.

| Operation | Planned route |
| --- | --- |
| New session and session view | POST /sessions and GET /sessions/{session_id} |
| Published terms | GET /terms |
| Parse or clarify a message | POST /preferences/parse |
| Confirm a profile or patch | POST /preferences/confirm |
| Start planning or request a fresh recommendation | POST /schedules/recommend |
| Preview a manual change | POST /schedules/edits/preview |
| Confirm or cancel a pending change | POST /schedules/changes/{preview_id}/confirm and POST /schedules/changes/{preview_id}/cancel |
| Course details | GET /courses/{course_id} with session context |
| Restore or save preferences | POST /preferences/restore and POST /preferences/save |

Mutation requests carry the expected revision and session context. A stale edit returns a conflict outcome and the latest view. It can be retried by creating a fresh preview. Provider errors, search limits and infeasibility are structured planning outcomes so the UI can label each one accurately. Final route and schema implementation belongs in Construction.
