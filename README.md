# CourseCompass — Conversational Course Planning for Cornell Students

**Course:** INFO 5940, Fall 2026  
**AI-driven development methodology:** AWS AI-DLC **v1.0.1**  
**Coding agent:** OpenAI Codex (using `AGENTS.md` and `.aidlc-rule-details/`)  
**Product LLM:** OpenAI gpt-4.1-mini via the OpenAI API. The team will confirm the budget and owner before the first paid call during Construction.

## Team

**Team 15** — member names and NetIDs are listed below; Zengzi Xu’s NetID was supplied in teammate commit `ba84f14`.

| Full name | Cornell NetID |
| --- | --- |
| Gangming Zhang | gz285 |
| Junxiang Gong | jg2582 |
| Shiyu Cheng | sc3559 |
| Zengzi Xu | zx377 |

## Project Overview, Problem, and Why an LLM

**Input review status:** The team approved the original input documents and Requirements Analysis at revision e45ec09. The recommendation and timetable revision was approved with Workflow Planning at f36a8ed.

Choosing a Cornell semester schedule requires more than finding interesting classes. Students must satisfy credit and degree constraints, avoid overlapping meeting times, assess prerequisite eligibility, and consider workload, instructor experience, subject interests, and future goals. Cornell's official program pages and catalog list published degree requirements. The Class Roster provides course descriptions and term-specific offerings. Syllabi, instructor pages, and student discussions can provide additional context. Students currently need to reconcile those sources manually while making trade-offs across an *entire combination* of courses. CourseCompass is a local web application that helps Cornell students across majors and degree programs explore course options, express their preferences conversationally, and customize a feasible semester schedule. Students can consult their official program requirements and confirm which courses or constraints to include in their plan.

An LLM is useful for translating nuanced, incomplete student requests into structured constraints and preferences. For instance, “I need 12–15 credits, prefer theoretical classes, don't want more than two heavy courses, and would rather keep Fridays free” includes hard and soft requirements that cannot all be handled through keyword search. The LLM asks targeted clarifying questions, extracts preferences in a validated schema, summarizes retrieved course evidence with citations, and explains trade-offs among proposed schedules. Deterministic software—not the LLM—checks credit bounds, meeting-time conflicts, and other supported feasibility constraints and computes schedule combinations.

## Planned LLM Integration

The student talks to a preference-extraction agent, reviews the interpreted profile, and adjusts a small set of weighted criteria: topical/career relevance, personal interest, perceived teaching quality, workload fit, difficulty fit, and timetable convenience. A course-research tool queries Cornell's published course offerings for one selected semester; optional curated evidence links and extracts may be included when permitted. A deterministic schedule optimizer ranks feasible combinations and provides one recommended timetable for the student's confirmed profile. The agent explains why that timetable and additional individual courses fit the student's needs, labels uncertain review-derived claims, and supports conversational refinements such as “keep my first two classes but make Thursday lighter.” The application must not assert that it has checked enrollment seats or verified degree completion unless those capabilities are explicitly implemented and validated.

## MVP Scope and Feasibility

By **December 16, 2026**, the MVP targets F1–F8 and lets students select any published Cornell term with accessible data, across all accessible subjects. Each recommendation covers a single selected term. Historical offerings can provide context. Students specify credit and timing constraints, preferences and a weekly budget for study outside class. They can enter completed courses manually or import a text-based PDF locally and verify the extracted course identifiers. The app opens with the recommended timetable filled in. A side panel shows individually recommended courses that the student can try adding or swapping. Clicking a course in either area opens its details. The app previews affected meetings and credits and asks for confirmation before replacing courses that conflict. It uses editable default preference weights for new users and can restore preferences they choose to save locally. Roster data powers scheduling. Review cards use permitted curated or user-supplied evidence and source links, and label missing information. Automated enrollment, comprehensive degree audits, live seat guarantees and GPA prediction are outside the MVP.

The project is structured into independent, testable work packages: Cornell data ingestion, structured preference capture, evidence-backed scoring, constraint-based schedule optimization, and the web conversation/calendar UI. We will test scheduling feasibility exhaustively on small synthetic datasets and with representative Cornell schedule cases; we will also assess preference extraction and conversational revisions using labeled scenarios. A recommendation will distinguish official course facts from community opinions and assumptions. The final demo will show an initial student request, one recommended timetable, a confirmed course swap, and a conversational refinement that preserves the student's hard constraints.

## Collaboration Plan

All four members participate in operating Codex and reviewing artifacts. The team reports in-person meetings during class and before or after class, and a shared group chat between meetings. Each working session has one active operator while the others discuss and review; operators may rotate, and everyone continues the same AI-DLC state rather than creating independent Inception workflows. The team plans to meet twice a week for about 60 minutes, with short exchanges before and after class. The final Canvas submission owner will be assigned before submission. GitHub Issues and pull requests remain proposed ways to coordinate Construction. Before each gate approval, the team reviews the documents and checks `aidlc-docs/audit.md` and `aidlc-docs/aidlc-state.md`. Stage approvals are recorded in the AI-DLC audit. The Canvas submission follows final milestone review.

## Repository and Workflow

- `docs/vision.md`: product goals, scope, users, and risks.
- `docs/technical-environment.md`: planned technical stack, architecture, security, tests, and example code for team review.
- `AGENTS.md` and `.aidlc-rule-details/`: **official AI-DLC v1.0.1 rules** installed for Codex. `AGENTS.md` is the unmodified official `core-workflow.md`; `.aidlc-rule-details/VERSION` records the source release. [Upstream release](https://github.com/awslabs/aidlc-workflows/releases/tag/v1.0.1).
- `aidlc-docs/`: **created and maintained by AI-DLC**, including Inception artifacts, audit, and state.
- Application code (during Construction): at repository root in `src/` and `tests/`, **not** under `aidlc-docs/`.

**Milestone (Nov 24):** Complete Inception through Units Generation, then stop before Construction.  
**Final (Dec 16):** Complete Construction, test the MVP, and submit the required demonstrations and presentation.

## Mid-semester Workflow Boundary

The course's mid-semester milestone requirements take precedence when choosing AI-DLC stages. The required Inception stages are Requirements Analysis, User Stories, Workflow Planning, Application Design, and Units Generation. Do not omit their required artifacts because the general workflow labels a stage conditional. After the team approves Units Generation, stop before Construction; Operations is not required for this course project.

The files in `docs/` are team-authored inputs. Team members must review and agree to them before starting the shared workflow. Rule installation alone does not establish that the agent loaded the rules or that Inception has begun. Workflow progress and approvals must come from actual shared sessions and be recorded by AI-DLC.

Once the team is ready, start a fresh Codex session in the repository root and use a prompt such as:

```text
Using AI-DLC v1.0.1, start CourseCompass from docs/vision.md and
docs/technical-environment.md. Follow the INFO 5940 mid-semester milestone:
complete Requirements Analysis, User Stories, Workflow Planning,
Application Design, and Units Generation. Ask the team the necessary
questions, wait for actual approvals at the required gates, and keep
aidlc-docs/audit.md and aidlc-docs/aidlc-state.md accurate. After we approve
Units Generation, stop before Construction. Do not generate application code.
```

At each approval gate, review the artifacts together and check the audit and state files. Reset context at gates as the course instructs, then resume in the same repository using the state file and relevant artifacts. Do not prefill team decisions or backfill approvals that did not occur. Submit the repository URL on Canvas after committing and pushing all milestone work, and verify the URL opens without signing in.

## Authoritative References

- [Cornell Class Roster](https://classes.cornell.edu/)
- [Cornell Programs of Study and Graduate Fields](https://catalog.cornell.edu/programs/)
- [AWS AI-DLC v1.0.1 release](https://github.com/awslabs/aidlc-workflows/releases/tag/v1.0.1)
- [AI-DLC v1.0.1 input quickstart](https://github.com/awslabs/aidlc-workflows/blob/v1.0.1/docs/writing-inputs/inputs-quickstart.md)
