# CourseCompass Requirements

- **Status:** Original requirements approved at e45ec09. The timetable interaction revision is ready for review with Workflow Planning.
- **Phase:** AI-DLC v1.0.1 Inception, Requirements Analysis
- **Baseline:** [Vision](../../../docs/vision.md), [Technical Environment](../../../docs/technical-environment.md), and [answered requirements questions](requirement-verification-questions.md)

## Intent analysis

| Item | Finding |
| --- | --- |
| User request | Complete the INFO 5940 midterm milestone for CourseCompass. Plan a conversational Cornell course recommender that translates student preferences into feasible semester schedules. |
| Request type | New project (greenfield). |
| Scope | Multiple components: a local web interface, LLM-assisted preference capture and explanation, official course data, documented evidence, and deterministic schedule optimization. |
| Complexity | Complex. Data may be incomplete, required class components must be combined correctly, and conversation updates must preserve hard constraints. |

CourseCompass serves Cornell students across majors and degree programs. Typical users include students arranging required courses, comparing electives across subjects, or prioritizing coursework related to their research or career goals. After the student confirms the interpreted profile, the core result is one prefilled recommended timetable for the selected term and a list of additional individual courses. The student can inspect details and validate proposed changes against that timetable.

## MVP boundary

- Retain F1–F8 from the Vision. Offer selection from Cornell's published terms with accessible data and search across accessible subjects. Evaluate courses and sections from one selected term per recommendation. Past offerings may be shown as context.
- Use Cornell Class Roster for published course descriptions and term-specific offerings. Use Cornell's catalog and official project or program pages for published degree requirements that students can consult and confirm for planning. Where information is unavailable or applicability is unclear, label it and allow the student to clarify.
- Keep the core workflow usable with official course information alone. Use permitted, manually curated or user-supplied review evidence with source links. Automated collection from Rate My Professors or Reddit is outside the committed scope.
- The MVP runs locally. Institutional login, automatic enrollment, seat or waitlist guarantees, GPA prediction, multi-term scheduling, and a comprehensive individual degree audit are outside scope.

## Functional requirements

| ID | Vision / answers | Requirement and acceptance condition |
| --- | --- | --- |
| FR-01 | F1, Q1 | Capture the target term, credit bounds when specified, fixed and excluded courses, unavailable times, interests and other preferences from conversation. Present the interpreted profile for confirmation or editing. Ask about missing critical fields and ambiguous restrictions such as whether Friday off is mandatory. Interpret a weekly study budget as time spent outside class. Distinguish estimated workload from verified meeting times. |
| FR-02 | F1, Q3 | Let students enter completed course identifiers manually or import them locally from a text-based PDF. Extract course identifiers needed for planning and present them for confirmation before use. If extraction fails or the PDF is scanned, show the manual entry path. Record confirmed exclusions and avoid assuming that all completed courses are prohibited repeats. |
| FR-03 | F2, Q2 | Load and normalize published Cornell course and section records for the selected term. Retain identifiers, source links, retrieval time and available course title, description, credits, instructor and meeting details. Keep offerings from other terms out of the current candidate set. Label missing or unsupported information, including incomplete required section associations. |
| FR-04 | F3, Q6 | Display course facts with sources. Distinguish Cornell material from qualitative commentary or estimates and label unavailable evidence. Include permitted curated or user-supplied reviews when available, with source links and dates if known. Research interests may appear as instructor background. Teaching quality, difficulty and workload require their own evidence. Published program requirements are references for user-confirmed planning constraints. |
| FR-05 | F4, Q4 | Let users edit six nonnegative weights for career or research relevance, interest, teaching quality, workload fit, difficulty fit and timetable convenience. Normalize the weights. New users start with editable defaults, and returning users may restore their own explicitly saved local preferences. Treat missing evidence as unavailable when scoring and identify which evidence informed the result. |
| FR-06 | F5 | Use deterministic logic to select the highest-ranked validated timetable found for the confirmed profile. Include individual courses with supported offerings in the selected term as additional recommendations when relevant. The list size depends on available data and fit. Validate the initial timetable and each proposed edit for credits, term, pinned and excluded courses, duplicate courses, required section components, and nonoverlapping meetings and unavailable blocks. Select at most one compatible section package per course. |
| FR-07 | F5 | If a prerequisite or a pinned course's eligibility is unclear, ask for clarification or explicitly label it as unresolved. Give an actionable reason when a pinned course cannot be scheduled. When no feasible result is found, explain which checked constraints or data limitations affected the search. Suggest adjustments for the user to confirm. Preserve hard constraints by default. |
| FR-08 | F6 | Prefill the weekly timetable with the recommended combination and list additional recommended courses individually. Clicking a course in either area opens its details and sources. A student can drag a course or use a click action to propose an addition or swap. Mute a candidate card with a time conflict and show a text cue. The card remains selectable for inspection and preview. Name affected courses and show the resulting credit total before confirmation. Canceling preserves the current timetable. Confirming applies the edit only if the resulting timetable passes deterministic checks. A pinned course remains protected unless the student explicitly changes that constraint. |
| FR-09 | F7 | Accept conversational revisions, confirm ambiguous changes, and recompute using the updated profile. For example, a request to reduce workload preserves confirmed hard constraints such as the required course and unavailable periods. Apply changes to hard constraints only when the user explicitly confirms them. |
| FR-10 | F8 | Provide labeled local roster snapshots or test fixtures and a repeatable demonstration path for temporary roster or model unavailability. When a fixture or mock response is used, show that distinction in the demo context. |

### Feasibility and ranking rules

1. Assess the student's confirmed hard constraints before ranking feasible results. Treat back-to-back meetings as nonoverlapping. Consult the source or ask the user where a course's prerequisite or repeat status cannot be established reliably.
2. Use the six editable preference weights to rank whole schedules. Rank individual alternative courses by relevant evidence and fit with the current profile. Re-evaluate their compatibility when the timetable changes. For initial review, the Vision proposes interest 25%, career or research relevance 20%, timetable convenience 20%, workload fit 15%, difficulty fit 10% and teaching evidence 10%. The exact default profile and scoring normalization will be checked against representative schedules during design.
3. Renormalize over criteria with usable evidence. Show when estimates have limited coverage. When evidence is insufficient for meaningful comparison, show available factual differences without creating a synthetic rating.
4. Distinguish proven infeasibility from a search ending without a feasible result because of missing data or a time limit. Return a validated feasible candidate if available, and describe limitations accurately. Do not silently discard a fixed course to make a result fit.

## Non-functional requirements

| ID | Requirement | Verification in Construction |
| --- | --- | --- |
| NFR-01 Correctness | All recommendations presented as feasible must pass independent deterministic checks. The LLM interprets requests and explains results based on validated data. | Test credit bounds, pinned and excluded courses, component compatibility, overlaps and back-to-back times. Compare optimizer output with exhaustive enumeration for small fixtures. |
| NFR-02 Privacy and safety | Run UI and API on loopback. Keep original transcripts and raw extracted transcript text off hosted model requests, persistent storage, audit logs and Git. Save personal preferences locally only when the user chooses to. Use configured credentials, validated requests and permitted tool calls. Treat external evidence as untrusted data. | Check model inputs and logs with synthetic transcript data, malformed tool arguments and evidence that contains instructions. Verify that secrets stay outside Git. |
| NFR-03 Provenance | External facts and evidence must be traceable to a source. Label missing, stale or unverified data and distinguish qualitative opinion from official information. | Inspect source links and fixture cases for missing coverage or stale information. |
| NFR-04 Reliability and responsiveness | Bound external requests, retries and optimizer work. Show actionable feedback when data retrieval or solver execution fails and support the labeled demo path. Candidate limits and timeouts will be validated in design and Construction. | Use fixture and stub cases for provider failures and test interruption of the solver. Check that the UI distinguishes the relevant outcome. |
| NFR-05 Usability | Make confirmed constraints, editable weights, the recommended timetable, and alternative-course previews visible. Course details and source links are reachable from both the timetable and the alternative list. | Walk through an initial timetable, a canceled and a confirmed swap, a follow-up revision, and an infeasible case. Check that a textual cue accompanies the conflict styling. |
| NFR-06 Reproducibility and testability | Keep extraction, data adaptation, scoring and optimization independently testable. Use documented local startup, fixtures and mock inference for repeatable tests. | Run API contract and scenario checks. Use Hypothesis with pytest for generated invariants and round-trips in scope. Use domain generators, shrinking and reproducible seeds. |

## Technical and workflow decisions

- The planned stack remains Python 3.12 and uv, Streamlit, FastAPI and Uvicorn, Pydantic, httpx, OR-Tools CP-SAT, SQLite, and OpenAI gpt-4.1-mini. Confirm the API budget and owner before the first paid call in Construction (Q5).
- Meet twice weekly for about 60 minutes, with short exchanges around class (Q7). Continue one shared AI-DLC workflow.
- Security and Resiliency Baseline extensions are disabled (Q8–Q9). The basic privacy, security, failure handling and local fallback requirements above remain part of the project.
- Property-Based Testing is enabled in Partial mode (Q10). PBT-02, PBT-03, PBT-07, PBT-08, and PBT-09 apply at the relevant Construction stages. Hypothesis is the planned framework. The current Inception artifact records the testable properties and approach for later implementation.

## Acceptance scenarios

| Scenario | Expected outcome |
| --- | --- |
| New student needs 12–15 credits and cannot attend on Friday | After confirmation, prefill one validated timetable that satisfies the restrictions. Display weekly meetings, total credits, reasons, and any individually recommended alternatives. |
| Student tries an alternative that overlaps a scheduled elective | Mark the conflict and preview the courses affected and resulting credits. Canceling preserves the timetable. Confirmation applies a valid swap while preserving pinned courses and all confirmed hard constraints. |
| Student clicks a course with no review in either list or timetable | Show official course information and source links. Clearly indicate that review or workload evidence is unavailable. |
| Student imports a text-based PDF or enters course codes | Show the extracted or entered course identifiers for confirmation. Provide manual entry for an unsupported file. |
| Student asks to keep a required course and lower workload | Update the workload preference, preserve the required course and other confirmed hard constraints, and explain the resulting change. |
| Required course has no compatible section, or conditions conflict | Explain the problem and offer a user-controlled change. Do not present a conflicting course combination as feasible. |
| Cornell or the model is unavailable during the demo | Use the clearly labeled local or mock path and demonstrate the core flow. |

## Questions carried into design

- Validate term and subject coverage and the supported rules for lecture, discussion and lab pairings.
- Define which prerequisites and repeat rules are machine-checkable using available official data and confirmed courses.
- Select representative test fixtures and validate scoring defaults, evidence gaps, individual alternative ranking, and the timetable's course-edit cases.
- Confirm the final Canvas submitter and review arrangements before release. Complete the midterm through Units Generation.
