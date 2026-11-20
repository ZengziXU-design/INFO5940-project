# Vision Document — CourseCompass

**Project type:** New, local-first LLM-integrated web application  
**Audience:** Cornell students across majors and degree programs who are planning a semester\
**Planning horizon:** Inception by November 24, 2026; functional MVP by December 16, 2026
**Status:** Original baseline approved at e45ec09. The timetable interaction revision was approved with Workflow Planning at f36a8ed.

## 1. Executive Summary

CourseCompass is a conversational course-planning assistant for Cornell students. Students describe academic constraints, intended career or research direction, workload tolerance, scheduling preferences, and instructor or course quality priorities. An LLM turns those preferences into an editable profile and asks the student to confirm it. A deterministic optimizer selects one recommended feasible timetable for the chosen term. The web view opens with that timetable filled in and shows additional individual courses that the student can try adding or swapping. The MVP retains F1–F8 and targets Cornell terms and subjects with accessible roster data. Historical offerings can provide context.

## 2. Problem Statement

A student selecting courses must jointly consider credits, meeting times, prerequisites, degree progress, topic fit, instructor experience, assessment style, workload, and individual schedules. Cornell's catalog and official program pages provide published degree and program requirements. The Cornell Class Roster provides course descriptions and term-specific section details. Qualitative information can be scattered across department sites and student discussions. Optimizing courses one at a time can produce an infeasible or unbalanced schedule once the student combines them.

CourseCompass aims to reduce manual comparison and make trade-offs explicit. The user should be able to explain a goal in ordinary language, inspect or change the criteria inferred by the LLM, and start with a feasible semester timetable and evaluate individual course changes using explanations and source references. It is a decision-support tool, not an official academic advisor or degree-certification service.

## 3. Target Users and Primary Jobs

CourseCompass serves students across Cornell majors and degree programs. The examples below are organized by planning goals.

| Persona | Need | Example request |
| --- | --- | --- |
| Student planning around program requirements | Use published requirements to identify required courses, then balance prerequisites, interests, credits, and meeting times | “I need to take a course required by my program, reach 12–15 credits, and keep Friday afternoon free.” |
| Student exploring another field | Compare electives across subjects while managing workload | “I want to try a public policy or design elective that fits around my required courses.” |
| Research-oriented student | Prioritize technical depth and research relevance | “I want theory-focused classes but not three heavy proof courses together.” |

Users can look up the published requirements for their program in the applicable catalog or on the official program page, then confirm which requirements to use in their semester plan. Users also supply completed-course information. They may identify courses they cannot or do not want to repeat. The user can enter completed courses manually or use optional local text-based PDF import. Confirm extracted course identifiers before use. Scanned PDFs are outside the MVP. The MVP does not authenticate with Cornell or certify graduation.

## 4. User Inputs and Preference Categories

**Hard constraints (never silently violated):** chosen semester; credit minimum and maximum; forbidden meeting windows; section meeting conflicts; at most one section package per course; required or excluded courses if explicitly supplied; declared prerequisite eligibility when reliable structured data is available; required course components such as mandatory lectures and discussions when modeled.

**Study-time input:** The weekly time budget measures study outside class. Display workload evidence as an estimate or label it unavailable.

**Soft, weighted criteria:** (1) career/research relevance, (2) subject interest, (3) teaching quality when there is evidence, (4) manageable workload, (5) difficulty fit relative to the student's background, (6) preferred days/times and compactness. The user can edit weights, and the system normalizes nonnegative weights. When evidence is missing, the criterion is marked unavailable rather than fabricated or automatically treated as a bad score.

**Cold-start default proposal:** Interest 25%, career/research relevance 20%, timetable convenience 20%, workload fit 15%, difficulty fit 10%, teaching evidence 10%. These are assistant-proposed editable defaults under the user’s request for a default priority, not a separately approved fixed policy. Missing evidence is not a negative rating. The app may restore preferences the current user has chosen to save locally. New users start with editable defaults.

**Additional optional profile inputs:** preferred assessment method, current obligations, preferred course level, desire for exploratory electives, self-reported prior courses, and expected graduation timeline. Only those that can be handled consistently by the MVP should affect automated optimization.

## 5. Full Product Vision (Beyond MVP)

In a mature product, CourseCompass could support multiple universities and terms, multi-term prerequisite planning, official program-specific degree audits, deeper authorized academic-record integrations, richer evidence from permitted course evaluation sources, enrollment-status monitoring, and integration with personal calendars. Future optimization could model travel time, exam clustering, uncertainty in workload, and multi-term opportunity costs.

## 6. MVP Scope — Features In

| ID | Feature | User value and expected behavior |
| --- | --- | --- |
| F1 | Conversational onboarding and refinement | Interpret free-form student preferences into structured fields; ask clarification for missing critical inputs; display fields for confirmation/editing. |
| F2 | Cornell semester course ingestion | Query published Cornell course and section data for one selected published term and all data-accessible Cornell subjects; retain source URLs, retrieval time, credits, title, instructor, and meeting information where available. |
| F3 | Course information and evidence cards | Show official facts and, when available, permitted curated review evidence, with source attribution and uncertainty labeling. |
| F4 | Editable weighted preference profile | Expose six preference weights, allow user adjustments, normalize weights, and distinguish soft preferences from hard constraints. |
| F5 | Feasibility and schedule optimization | Select one recommended feasible schedule for the confirmed profile and validate proposed course changes against the same credit, meeting, and eligibility rules. Report no-solution cases and actionable adjustments. |
| F6 | Editable timetable and course detail | Prefill one weekly grid and show a side panel of individual course recommendations. Support drag and click actions, replacement previews, conflict warnings, and explicit confirmation of swaps. Clicking any course opens its title, description, instructor, recommendation reason, available reviews and workload estimates, and sources. |
| F7 | Conversational revision | Update one or more preferences and recompute a recommendation while preserving other hard constraints. |
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

Use [Cornell Programs of Study and Graduate Fields](https://catalog.cornell.edu/programs/) and the corresponding official program pages for published degree and program requirements. Use the [Cornell Class Roster](https://classes.cornell.edu/) for course descriptions, credits, instructors, and sections and meeting times in the selected term. Official course pages and syllabi can supplement topics and assignments. Published program rules inform planning through user-confirmed requirements and do not provide an individual degree audit. Community comments may inform *qualitative* ratings only where collection and use are permitted, and must include the source, date if available, and an explicit uncertainty indicator. We will never invent instructor ratings or infer a student's likely grade from a single review. Sources requested by the team include the Cornell Class Roster and course websites, with Rate My Professors and Reddit as possible review references. The MVP displays source links and accepts permitted curated or user-supplied evidence. Users can open the source in a browser. Inclusion in the MVP does not depend on automated retrieval from either review site. For a new course, a professor’s official research interests may be described, but they are not evidence of teaching quality or course workload. If a source is unavailable, label missing information and keep the official-course path usable.

## 9. Success Metrics and Acceptance Targets

| Metric | Proposed verification method | Target |
| --- | --- | --- |
| Hard-constraint validity | Property and fixture tests over generated recommendations | 100% of emitted plans satisfy implemented hard constraints |
| Optimizer correctness | Compare solver with exhaustive enumeration on small instances | Match optimum for small seeded cases under the documented objective |
| Preference extraction quality | Labeled requests with explicit expected fields | Correct schema and user-confirmed constraints; numerical dataset/accuracy targets remain proposals for review |
| Manual course swaps | Fixture with a compatible replacement, an overlap, and a conflict with a pinned course | Only confirmed feasible swaps change the timetable |
| Conversational revisions | Follow-up scenarios with expected changes | Apply the requested update and preserve all unaffected hard constraints |
| Provenance | Audit displayed official course facts | All externally-derived facts carry a source or explicit unavailable flag |
| Demonstrability | Offline fixtures and documented local startup | End-to-end scripted demo on a team laptop |

## 10. Example User Journey

1. Student selects a supported Cornell semester and says: “I need 12–15 credits, prefer theoretical CS and ORIE, and want Fridays free.”
2. Agent extracts 12–15 credits, topic priorities, and a Friday constraint, then asks whether Fridays free is mandatory.
3. Student marks Friday as mandatory, reviews six weights, and confirms self-reported eligibility.
4. After the student confirms the profile, the research service loads available offerings and the optimizer selects one feasible recommended timetable.
5. The week opens with the recommended courses scheduled. The side panel lists individual alternatives. The student selects a course in either area to inspect its details and available evidence.
6. The student drags an alternative onto the timetable or clicks the replacement action. The app previews the affected courses, meetings, and credits, then applies a valid change only after confirmation.
7. The student says “Keep my required course but reduce the workload.” The system updates the soft preference and preserves confirmed hard constraints. When no timetable can be verified, it explains the reason and offers user-controlled adjustments.

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

Q1–Q10 are recorded in the formal requirements question file. Remaining design work includes:

- Validate the Cornell roster’s term and subject coverage and identify which lecture, discussion, and lab combinations have enough data to verify feasibility.
- Define which prerequisite and repeat-enrollment rules can be checked using official data and user-confirmed completed-course identifiers.
- Validate the editable default weights and workload scoring against representative scheduling examples.
- Confirm extraction and calendar acceptance scenarios, including course swaps, unavailable required courses, and infeasible schedules.
- Identify the API budget owner and spending limit before the first paid call in Construction.
