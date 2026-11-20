# CourseCompass User Stories Generation Plan

**Status:** Plan approved. Stories and personas generated for team review.

**Inputs:** [Approved requirements](../requirements/requirements.md), [requirements answers](../requirements/requirement-verification-questions.md), [Vision](../../../docs/vision.md), and [assessment](user-stories-assessment.md).

## Proposed approach

Keep the three student archetypes already established in Vision: planning around program requirements, exploring electives across fields, and seeking courses related to research or career goals. For the labeled-demo path, document the team demo operator as a secondary persona if needed to assign the outcome in FR-10. Personas describe planning goals and can span majors and programs.

Write stories using “As a …, I want …, so that …” with stable IDs and references to personas and approved requirements. Give each story observable Given/When/Then acceptance criteria. Keep the concepts grounded in one selected term, accessible Cornell offerings, student-confirmed constraints, official or attributed evidence, and a locally processed completed-course input. Leave implementation choices and work assignments for Application Design and Units Generation.

| Organization | Benefit | Trade-off |
| --- | --- | --- |
| User journey | Makes the path from intent to revised timetable easy to follow. | Requires cross-reference for capabilities used at several points. |
| Feature | Makes mapping to F1–F8 and coverage review straightforward. | The full student journey is less visible. |
| Persona | Makes stakeholder motivations clear. | Common tasks can be repeated across similar student personas. |
| Domain | Clarifies business areas such as course evidence and schedule feasibility. | Can make related student actions harder to follow as a sequence. |
| Epics | Gives a concise overview of larger capability groups. | Adds hierarchy for a small MVP unless used sparingly. |

The team selected A for both questions. Organize stories by the student journey and include requirement and feature references. Cover routine input and evidence gaps as acceptance cases. Write separate stories for recovery from an unavailable required course, a search with no feasible schedule, and use of the labeled demonstration fallback.

## Team questions

Reply in chat with choices such as `Q1=A, Q2=A` or fill the `[Answer]:` lines. For Other, include the choice and preferred approach.

### Q1 — How should the primary stories be organized?

A) By user journey from describing a goal through confirming inputs, comparing timetables and refining a plan, with F1–F8 and FR references in each group. Recommended for the demo flow.

B) By feature F1–F8, with a short end-to-end scenario showing how the features fit together.

C) By student persona, with common stories written once and linked from each persona.

D) Other (please specify).

[Answer]: A

### Q2 — How should exception scenarios be represented?

A) Keep ordinary field and evidence gaps as acceptance cases in the relevant story. Give distinct user recovery outcomes their own stories, including an unavailable required course, no valid schedule, and the labeled demo fallback. Recommended for separately testable recovery behavior.

B) Keep every exception as an acceptance case in the corresponding primary story, with a separate traceability row for each exception.

C) Other (please specify).

[Answer]: A

## Execution checklist

- [x] Assess the need for the stage and record the decision.
- [x] Review the approved requirements, question answers, and Vision personas.
- [x] Define the approach, story options, and deliverables.
- [x] Collect and validate both A answers. The choices are consistent and require no clarification.
- [x] Obtain explicit approval of the story generation plan.
- [x] Create `aidlc-docs/inception/user-stories/personas.md` with goals, constraints, scenarios, and story references.
- [x] Create `aidlc-docs/inception/user-stories/stories.md` using the approved organization and breakdown. Include acceptance criteria and persona and requirement mapping.
- [x] Verify coverage against F1–F8 and FR-01–FR-10. Check each story for INVEST qualities (independent, negotiable, valuable, estimable, small, testable) and for observable acceptance criteria.
- [x] Present both artifacts for review.
- [ ] Record the team's approval or requested changes.
