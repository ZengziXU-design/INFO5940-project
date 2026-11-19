# Vision Document — CourseCompass

**Project type:** New, local-first LLM-integrated web application  
**Audience:** Cornell students planning a semester  
**Planning horizon:** Inception by November 24, 2026; functional MVP by December 16, 2026
**Status:** Draft reflecting user-reported team discussion; unresolved choices and input-baseline review remain pending. See the formal requirement-verification question file.

## 1. Executive Summary

CourseCompass is a conversational course-planning assistant that recommends **combinations of Cornell classes**, not just isolated courses. Students describe academic constraints, intended career or research direction, workload tolerance, scheduling preferences, and instructor/course quality priorities. An LLM turns those preferences into a transparent, editable profile and researches available course information, while a deterministic optimizer assembles feasible schedules and explains their trade-offs. The requested MVP retains F1–F8 and targets all data-accessible published Cornell semesters and departments/subjects. Q2 will clarify selection of a single target term versus multi-semester planning; no unavailable or unpublished offering is assumed. Outputs contain up to three feasible options.

## 2. Problem Statement

A student selecting courses must jointly consider credits, meeting times, prerequisites, degree progress, topic fit, instructor experience, assessment style, workload, and individual schedules. Cornell's official roster is authoritative for published course and section details, but qualitative information can be scattered across department sites and student discussions. Optimizing courses one at a time can produce an infeasible or unbalanced schedule once the student combines them.

CourseCompass aims to reduce manual comparison and make trade-offs explicit. The user should be able to explain a goal in ordinary language, inspect or change the criteria inferred by the LLM, and receive *feasible semester combinations* with explanations and source references. It is a decision-support tool, not an official academic advisor or degree-certification service.

## 3. Target Users and Primary Jobs

| Persona | Need | Example request |
| --- | --- | --- |
| Undergraduate CS student | Balance prerequisites, interests, credits, and daily timetable | “I need 12–15 credits, want systems and algorithms, and can't attend class Friday afternoon.” |
| Student exploring a field | Compare interesting electives without overcommitting | “I want to try ORIE, but prefer project-heavy classes over exams.” |
| Research-oriented student | Prioritize technical depth and research relevance | “I want theory-focused classes but not three heavy proof courses together.” |

Users supply completed-course and requirement information. They may identify courses they cannot or do not want to repeat. Local transcript-file import has been proposed but is not confirmed (Q3); any extracted courses require user confirmation. The MVP does not authenticate with Cornell or certify graduation.

## 4. User Inputs and Preference Categories

**Hard constraints (never silently violated):** chosen semester; credit minimum and maximum; forbidden meeting windows; section meeting conflicts; at most one section package per course; required or excluded courses if explicitly supplied; declared prerequisite eligibility when reliable structured data is available; required course components such as mandatory lectures and discussions when modeled.

**Study-time input:** The user requested a weekly time budget; whether it measures out-of-class effort or total class-plus-study time is pending Q1. Workload evidence may be missing, so estimates must not be presented as verified hours.

**Soft, weighted criteria:** (1) career/research relevance, (2) subject interest, (3) teaching quality when there is evidence, (4) manageable workload, (5) difficulty fit relative to the student's background, (6) preferred days/times and compactness. The user can edit weights, and the system normalizes nonnegative weights. When evidence is missing, the criterion is marked unavailable rather than fabricated or automatically treated as a bad score.

**Cold-start default proposal:** Interest 25%, career/research relevance 20%, timetable convenience 20%, workload fit 15%, difficulty fit 10%, teaching evidence 10%. These are assistant-proposed editable defaults under the user’s request for a default priority, not a separately approved fixed policy. Missing evidence is not a negative rating. Historical personalization is pending Q4; never assume a historical-user dataset exists.

**Additional optional profile inputs:** preferred assessment method, current obligations, preferred course level, desire for exploratory electives, self-reported prior courses, and expected graduation timeline. Only those that can be handled consistently by the MVP should affect automated optimization.

## 5. Full Product Vision (Beyond MVP)

In a mature product, CourseCompass could support multiple universities and terms, verified long-horizon prerequisite planning beyond the scope agreed in Q2, official program-specific degree audits, deeper authorized academic-record integrations, richer evidence from permitted course evaluation sources, enrollment-status monitoring, and integration with personal calendars. Future optimization could model travel time, exam clustering, uncertainty in workload, and multi-term opportunity costs.

## 6. MVP Scope — Features In

| ID | Feature | User value and expected behavior |
| --- | --- | --- |
| F1 | Conversational onboarding and refinement | Interpret free-form student preferences into structured fields; ask clarification for missing critical inputs; display fields for confirmation/editing. |
| F2 | Cornell semester course ingestion | Query published Cornell course and section data for the selected term(s) and all data-accessible Cornell subjects, with exact planning semantics pending Q2; retain source URLs, retrieval time, credits, title, instructor, and meeting information where available. |
| F3 | Course information and evidence cards | Show official facts and, when available, permitted curated review evidence, with source attribution and uncertainty labeling. |
| F4 | Editable weighted preference profile | Expose six preference weights, allow user adjustments, normalize weights, and distinguish soft preferences from hard constraints. |
| F5 | Feasibility and schedule optimization | Produce up to three high-scoring, distinct schedules satisfying supported credit/meeting/exclusion constraints; report no-solution cases and actionable relaxations. |
| F6 | Timetable and comparison view | Display a weekly grid, schedule alternatives, credits and trade-offs. Each course is clickable and opens sidebar cards for title, description, professor information, reviews, difficulty/workload evidence, sources and missing-data labels. |
| F7 | Conversational revision | Update one or more preferences and recompute schedules while preserving other hard constraints. |
| F8 | Local demo/test fixtures | Support a stable curated snapshot or fixture when Cornell API or model provider is temporarily unavailable; label demo data visibly. |

**Scope boundaries:** Ask the user when prerequisite eligibility is unclear. Identify unavailable pinned courses, explain no-solution cases and never silently relax hard constraints. No promise to infer all prerequisites from free-text descriptions. The system cannot guarantee seating or enrollment. No external reviews are required for the core recommendation path.

## 7. MVP Scope — Features Out

| Feature excluded | Reason | Future direction |
| --- | --- | --- |
| Automatic enrollment, add/drop, and Cornell authentication | Requires university systems and sensitive credentials | Later integration only with authorization |
| Full degree audit or graduation guarantee | Program rules and records are complex | Program-specific verified extension |
| Universities other than Cornell / unpublished or inaccessible offering data | The requested scope is Cornell’s data-accessible published roster | Future verified adapters |
| Live seat guarantees / waitlist prediction | Inventory changes and requires validated source | Future status checking |
| Unrestricted Rate My Professors / Reddit scraping | Terms, access, reliability, and privacy concerns | Only approved API/licensed or curated evidence |
| Grade predictions and guaranteed easy classes | Misleading and weak evidence | Relative workload indicators with uncertainty |
| Voice, mobile native app, and cloud deployment | Not required for meaningful MVP | Future UX iteration |

## 8. Data and Evidence Policy

Official Cornell roster details take precedence over opinion sources for course identity, credits, instructor assignments, meetings, and availability. Descriptions and syllabi may supplement topics and assignments when accessible. Community comments may inform *qualitative* ratings only where collection and use are permitted, and must include the source, date if available, and an explicit uncertainty indicator. We will never invent instructor ratings or infer a student's likely grade from a single review. Sources requested by the team include the Cornell Class Roster and course websites, with Rate My Professors and Reddit as possible review references. Their access/integration mode is pending Q6. For a new course, a professor’s official research interests may be described, but they are not evidence of teaching quality or course workload. If a source is unavailable, label missing information and keep the official-course path usable.

## 9. Success Metrics and Acceptance Targets

| Metric | Proposed verification method | Target |
| --- | --- | --- |
| Hard-constraint validity | Property and fixture tests over generated recommendations | 100% of emitted plans satisfy implemented hard constraints |
| Optimizer correctness | Compare solver with exhaustive enumeration on small instances | Match optimum for small seeded cases under the documented objective |
| Preference extraction quality | Labeled requests with explicit expected fields | Correct schema and user-confirmed constraints; numerical dataset/accuracy targets remain proposals for review |
| Conversational revisions | Follow-up scenarios with expected changes | Apply the requested update and preserve all unaffected hard constraints |
| Provenance | Audit displayed official course facts | All externally-derived facts carry a source or explicit unavailable flag |
| Demonstrability | Offline fixtures and documented local startup | End-to-end scripted demo on a team laptop |

## 10. Example User Journey

1. Student selects a supported Cornell semester and says: “I need 12–15 credits, prefer theoretical CS and ORIE, and want Fridays free.”
2. Agent extracts 12–15 credits, topic priorities, and a Friday constraint, then asks whether Fridays free is mandatory.
3. Student marks Friday as mandatory, reviews six weights, and confirms self-reported eligibility.
4. Research service loads available offerings, and optimizer finds up to three feasible combinations.
5. UI displays weekly timetables and explains relevance, evidence quality, workload uncertainty, and trade-offs.
6. Student says: “Keep my required course but reduce the workload.” The system updates soft preferences, preserves hard constraints, and recomputes.
7. If no valid schedule exists, it explains which constraints block feasibility and *offers* changes instead of silently violating them.

## 11. Risks and Mitigations

| Risk | Consequence | Mitigation |
| --- | --- | --- |
| Cornell API changes / unavailable | Data ingestion fails | Adapter, schema validation, local snapshots and fixture-based demo |
| Incomplete section component representation | False feasibility | Model mandatory components; fail conservatively if meeting data incomplete |
| Subjective or sparse ratings | Misleading recommendations | Separate official facts from opinions; show coverage/confidence; support no-review mode |
| LLM hallucination / malformed output | Incorrect constraints | Pydantic validation, fixed tool schema, user confirmation, deterministic rules |
| Combinatorial explosion | Slow optimization | Bounded candidate pool and per-request solver timeout, return feasible incumbent when available |
| Student misunderstands recommendations | Enrollment/degree mistakes | Clear disclaimer, links to source, no enrollment or degree guarantees |
| API costs or access | Demo interruption | Configurable model key, mock LLM responses and deterministic test fixtures |

## 12. Open Questions for Team and AI-DLC Inception

The formal questions are in `aidlc-docs/inception/requirements/requirement-verification-questions.md`:

- Q1: Definition of the weekly learning-time budget.
- Q2: Meaning of all-semester coverage and the scheduling horizon; all available Cornell departments remain the requested coverage.
- Q3: Manual completed-course input versus local transcript-file import in the first release.
- Q4: Personal local history versus cross-user historical data.
- Q5: Plain-language confirmation or revision of the proposed stack/model; API setup is deferred to Construction.
- Q6: Initial integration method for RMP/Reddit review evidence.
- Q7: Meeting frequency/duration.
- Q8–Q10: Explicit AI-DLC extension opt-ins; these are workflow choices, not additional course deliverables.

The confirmed input baseline must be reviewed before downstream requirements are generated. Answering clarification questions is not approval of ungenerated requirements, stories or designs.

These questions must be explicitly discussed and resolved or narrowed through AI-DLC's actual clarification and approval workflow; the agent should not assume the proposal alone constitutes team approval.
