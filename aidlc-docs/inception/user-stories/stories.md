# CourseCompass User Stories

**Status:** Original stories approved at cd3ebd5. The timetable interaction revision was approved with Workflow Planning at f36a8ed.

**Basis:** [Requirements](../requirements/requirements.md), [approved generation plan](../plans/story-generation-plan.md), and [personas](personas.md).

The stories follow the student's journey from defining a goal to revising a timetable. Each story has observable acceptance criteria. Examples use illustrative inputs and fixtures.

## 1. Establish the planning goal

### US-01 — Confirm the interpreted request

**As a** student planning a semester, **I want** to review the conditions CourseCompass understood, **so that** it schedules courses using the constraints I meant to specify.

**Personas:** P1, P2, P3. **Trace:** F1 / FR-01.

- **Given** a request for 12–15 credits and “preferably a free Friday,” **when** the app interprets it, **then** it shows the credit range and asks whether Friday is mandatory or preferred.
- **Given** the term or another critical field is missing, **when** the student asks for a plan, **then** the app asks for the missing information and shows the updated profile for confirmation.
- **Given** a student specifies 8 study hours per week, **when** the profile is displayed, **then** it identifies those hours as study outside class and distinguishes workload estimates from class meeting times.

### US-02 — Enter and verify completed courses

**As a** student with prior coursework, **I want** to enter completed course identifiers and review the list, **so that** eligibility and repeat checks use the correct information.

**Personas:** P1, P2, P3. **Trace:** F1 / FR-02.

- **Given** two completed course identifiers typed by the student, **when** the list is reviewed, **then** the app shows the identifiers and allows corrections before using them.
- **Given** a completed course appears as a candidate, **when** repeat eligibility has not been verified and the student has not excluded it, **then** the app keeps the question separate from confirmed exclusions and makes the uncertainty visible where relevant.

### US-03 — Import completed courses from a local PDF

**As a** student with a text-based transcript, **I want** to import course identifiers locally, **so that** I can populate my course history without retyping it.

**Personas:** P1, P2, P3. **Trace:** F1 / FR-02, NFR-02.

- **Given** a text-based PDF containing course identifiers and unrelated personal fields, **when** it is imported, **then** the student can verify or correct the extracted identifiers before they are used. The original PDF and raw extracted text stay local and are absent from model requests, persistent storage and logs.
- **Given** a scanned PDF or an extraction failure, **when** the student tries to import it, **then** the app explains that entry is needed and offers the manual path without creating unconfirmed course records.

## 2. Choose a term and set preferences

### US-04 — Use offerings for the selected term

**As a** student exploring courses across subjects, **I want** to see which offerings belong to my selected term, **so that** I can plan with courses that are listed for that term.

**Personas:** P1, P2, P3. **Trace:** F2 / FR-03, NFR-03.

- **Given** fixture data for two published terms and courses in different subjects, **when** the student selects one term, **then** only offerings from that term enter the schedule candidate set.
- **Given** an offering has missing section details, **when** the app displays it, **then** it identifies the gap and shows the available official source and retrieval information. Incomplete section packages are not presented as verified.

### US-05 — Adjust ranking preferences

**As a** student comparing realistic options, **I want** to adjust the relative importance of six preferences, **so that** I can favor a timetable that fits my goals.

**Personas:** P1, P2, P3. **Trace:** F4 / FR-05.

- **Given** a new profile, **when** the student opens its preferences, **then** the app offers editable defaults for career or research relevance, interest, teaching quality, workload fit, difficulty fit, and timetable convenience.
- **Given** the student increases timetable convenience, **when** recommendations are refreshed, **then** the app uses the revised weight and preserves confirmed hard constraints. Only criteria with usable evidence inform the score, and the recommendation explains which criteria were used and where evidence is missing.

- **Given** weights of 2 for interest and 6 for relevance with zeros elsewhere and evidence for both criteria, **when** they are applied, **then** they contribute 25% and 75%, respectively. Negative values are rejected.

### US-06 — Restore my own preferences

**As a** returning student, **I want** to restore preferences I chose to save, **so that** I can reuse them without re-entering each choice.

**Personas:** P1, P2, P3. **Trace:** F4 / FR-05, NFR-02.

- **Given** the student explicitly saved a preference profile locally, **when** they return to that local profile, **then** they can restore and edit the saved preferences.
- **Given** there is no saved profile, **when** the student begins, **then** the app starts with editable defaults. The app uses only this student's saved preferences for history-based personalization.

## 3. Review and customize the recommended timetable

### US-07 — Start with one validated timetable

**As a** student planning around degree requirements and time restrictions, **I want** the recommended course combination filled into a weekly timetable, **so that** I have a feasible starting point.

**Personas:** P1, P2, P3. **Trace:** F5 / FR-06, FR-07, NFR-01.

- **Given** a confirmed profile and a fixture with multiple ranked feasible schedules, **when** the planning view first opens, **then** exactly one timetable is prefilled with the highest-ranked validated combination found. It displays the term, meeting blocks, total credits, and grounded recommendation reasons.
- **Given** candidates with overlapping meetings, a missing required discussion, and back-to-back meetings, **when** feasibility is checked, **then** conflicting and incomplete combinations are excluded and back-to-back times are allowed. Each scheduled course appears once with one compatible section package.
- **Given** a candidate with unclear prerequisites, **when** its eligibility is evaluated, **then** the app asks the student to clarify or identifies eligibility as unresolved.

### US-08 — Try individual alternatives against the current timetable

**As a** student weighing timetable convenience and workload, **I want** to inspect and try alternative courses individually, **so that** I can personalize the recommendation while keeping my confirmed constraints.

**Personas:** P1, P2, P3. **Trace:** F5 and F6 / FR-06, FR-08, NFR-01, NFR-05.

- **Given** one feasible timetable and three relevant alternatives, **when** the result opens, **then** alternatives are presented as three individual course cards. Each card is visible even if the three alternatives could never form a feasible schedule together.
- **Given** a candidate section overlaps a scheduled elective, **when** the student drags it in or uses the click action, **then** the app marks and names the conflict, lists affected courses, and shows the resulting credits before confirmation. Canceling preserves the current timetable.
- **Given** the student confirms a replacement and the edited timetable passes deterministic checks, **when** the update is applied, **then** the weekly meeting blocks and credit total reflect the new combination and alternative compatibility is refreshed.
- **Given** a proposed swap would remove a pinned course or still violate a hard constraint, **when** the student attempts to confirm, **then** the previous timetable stays active and the app explains which requirement prevents the change.

### US-09 — Inspect a course and its sources

**As a** student considering an unfamiliar elective, **I want** to open the course details from the timetable or alternative list, **so that** I can verify official facts and decide how much weight to give opinions.

**Personas:** P1, P2, P3. **Trace:** F3 and F6 / FR-04, FR-08, NFR-03.

- **Given** a course on the timetable or in the alternative list, **when** the student selects it, **then** the detail panel shows the course code, available title, description, instructor, recommendation reason and official source. Relevant published program requirements remain linked references for planning.
- **Given** a permitted opinion is available, **when** it is shown, **then** it is identified as unofficial with its source and date where known. When evidence is absent, the panel labels it unavailable. Instructor research background is shown separately from teaching or workload assessments.

## 4. Refine the plan and recover from exceptions

### US-10 — Revise a plan without losing earlier constraints

**As a** student with a required course and a busy schedule, **I want** to revise a preference conversationally, **so that** I can improve an option without recreating the request.

**Personas:** P1, P2, P3. **Trace:** F7 / FR-09, NFR-01.

- **Given** a confirmed required course and an unavailable Friday block, **when** the student says “keep the required course and reduce workload,” **then** the next search changes the workload preference and retains the required course and Friday block.
- **Given** a follow-up that could remove a confirmed hard constraint, **when** the intent is ambiguous, **then** the app asks for confirmation and keeps the existing constraint until the student confirms a change.

### US-11 — Resolve an unavailable required course

**As a** student who pinned a required course, **I want** to know why the app cannot include it, **so that** I can verify my eligibility or decide whether to change my plan.

**Personas:** P1, P3. **Trace:** F5 / FR-07, NFR-01.

- **Given** a pinned course has no supported section package compatible with the current restrictions, **when** recommendations are requested, **then** the app identifies the course and the relevant conflict or data gap, and requests confirmation before changing the requirement.
- **Given** eligibility for that pinned course is unclear, **when** the app evaluates it, **then** the app surfaces the uncertainty for clarification. It never presents an unverified plan as feasible or silently removes the course.

### US-12 — Understand why no schedule was found

**As a** student whose constraints may be too restrictive, **I want** an explanation and a choice of adjustments, **so that** I can decide what to change.

**Personas:** P1, P2, P3. **Trace:** F5 / FR-07, NFR-04.

- **Given** valid course data and a fixture where hard constraints are mutually incompatible, **when** the search establishes infeasibility, **then** the app says that no option meets the current constraints, explains the relevant restrictions, and offers user-controlled adjustments.
- **Given** no option was found before the search limit or required data was missing, **when** that result appears, **then** it describes the limit or data gap. Any feasible result already validated may be displayed with the limitation noted.

### US-13 — Demonstrate using a labeled fallback

**As a** team member running the local demonstration, **I want** a labeled fallback when course or model services are unavailable, **so that** I can demonstrate the core workflow reliably.

**Persona:** P4. **Trace:** F8 / FR-10, NFR-04, NFR-06.

- **Given** live course data or the model is unavailable and a relevant local fixture or mock path is configured, **when** the demo runs, **then** it labels the fallback, shows its data context, and completes the demonstrated path without implying live results.
- **Given** the same fixture and scripted inputs, **when** the team repeats the demo, **then** it can reproduce the initial timetable, an individual course swap, a conversational revision and a no-feasible-schedule example.

## Feature coverage

| Vision feature | Requirements | Stories |
| --- | --- | --- |
| F1 Conversational intake and confirmation | FR-01, FR-02 | US-01, US-02, US-03 |
| F2 Cornell course and section data | FR-03 | US-04 |
| F3 Course details and evidence | FR-04 | US-09 |
| F4 Editable weights and local preferences | FR-05 | US-05, US-06 |
| F5 Feasibility, optimization and recovery | FR-06, FR-07 | US-07, US-08, US-11, US-12 |
| F6 Editable timetable and course details | FR-08 | US-08, US-09 |
| F7 Conversational revision | FR-09 | US-10 |
| F8 Local fallback and demo fixtures | FR-10 | US-13 |
