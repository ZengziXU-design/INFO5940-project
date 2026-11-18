# CourseCompass — Conversational Course Planning for Cornell Students

**Course:** INFO 5940, Fall 2026  
**AI-driven development methodology:** AWS AI-DLC **v1.0.1**  
**Coding agent:** OpenAI Codex (using `AGENTS.md` and `.aidlc-rule-details/`)  
**Product LLM:** OpenAI `gpt-4.1-mini` via the OpenAI API (planned; provider/model choice and budget remain subject to team confirmation; a working integration is not required for the mid-semester milestone)

## Team

**Team 15** — names retained from the existing GitHub README. Cornell NetIDs must be confirmed and filled in before submission.

| Full name | Cornell NetID |
| --- | --- |
| Gangming Zhang | gz285 |
| Junxiang Gong | jg2582 |
| Shiyu Cheng | sc3559 |
| Zengzi Xu | zx377 |

## Project Overview, Problem, and Why an LLM

Choosing a Cornell semester schedule requires more than finding interesting classes. Students must satisfy credit and degree constraints, avoid overlapping meeting times, assess prerequisite eligibility, and consider workload, instructor experience, subject interests, and future goals. Useful information is distributed across the Cornell Class Roster, course descriptions, syllabi, instructor pages, and student discussions. Students currently need to reconcile those sources manually while making trade-offs across an *entire combination* of courses. CourseCompass is a local web application that helps Cornell students explore course options, express their preferences conversationally, and compare feasible semester schedules.

An LLM is useful for translating nuanced, incomplete student requests into structured constraints and preferences. For instance, “I need 12–15 credits, prefer theoretical classes, don't want more than two heavy courses, and would rather keep Fridays free” includes hard and soft requirements that cannot all be handled through keyword search. The LLM asks targeted clarifying questions, extracts preferences in a validated schema, summarizes retrieved course evidence with citations, and explains trade-offs among proposed schedules. Deterministic software—not the LLM—checks credit bounds, meeting-time conflicts, and other supported feasibility constraints and computes schedule combinations.

## Planned LLM Integration

The student talks to a preference-extraction agent, reviews the interpreted profile, and adjusts a small set of weighted criteria: topical/career relevance, personal interest, perceived teaching quality, workload fit, difficulty fit, and timetable convenience. A course-research tool queries Cornell's published course offerings for one selected semester; optional curated evidence links and extracts may be included when permitted. A deterministic schedule optimizer produces up to three feasible and meaningfully different schedule options, returning fewer when fewer distinct feasible options exist. The agent then explains why each option fits the student's needs, labels uncertain review-derived claims, and supports iterative refinements such as “keep my first two classes but make Thursday lighter.” The application must not assert that it has checked enrollment seats or verified degree completion unless those capabilities are explicitly implemented and validated.

## MVP Scope and Feasibility

By **December 16, 2026**, the MVP will support one Cornell semester and a limited set of departments, initially **CS, INFO, and ORIE**, with the exact term selected based on Cornell API availability. Students will enter desired credits, completed prerequisites (self-reported), unavailable times, preferred days/times, topics/career interests, and editable preference weights. The app will retrieve and normalize course/section information, display source-linked course cards, enforce credit and meeting-time rules, and generate up to three distinct course schedules with a weekly calendar. A small permitted/curated dataset of instructor or course feedback may inform *uncertain* teaching/workload signals, but the core recommender will still function without external reviews. Advanced degree audits, automatic enrollment, all-university coverage, live seat guarantees, unrestricted scraping, and highly personalized GPA predictions are out of scope.

The project is structured into independent, testable work packages: Cornell data ingestion, structured preference capture, evidence-backed scoring, constraint-based schedule optimization, and the web conversation/calendar UI. We will test scheduling feasibility exhaustively on small synthetic datasets and with representative Cornell schedule cases; we will also assess preference extraction and conversational revisions using labeled scenarios. A recommendation will distinguish official course facts from community opinions and assumptions. The final demo will show an initial student request, feasible schedule alternatives, and a follow-up request that changes the recommended combinations without silently relaxing constraints.

## Collaboration Plan

The team will meet **twice weekly** for approximately **60 minutes per session**, using in-person sessions when possible and video calls otherwise. We will maintain one shared AI-DLC workflow during Inception: one designated operator runs Codex, while every member participates in the discussion, answers clarification questions, reviews each proposed artifact, and approves or requests changes. The technical lead will review architecture and testing decisions; another member will review user requirements and data-source reliability; all members will review the final user stories, constraints, and MVP scope. Between meetings we will coordinate in a shared group chat, track tasks through GitHub Issues, and use pull requests for substantive changes in Construction. Before approving each AI-DLC gate, the team will confirm that `aidlc-docs/audit.md` and `aidlc-docs/aidlc-state.md` accurately reflect the actual shared process.

## Repository and Workflow

- `docs/vision.md`: product goals, scope, users, and risks.
- `docs/technical-environment.md`: proposed technical baseline, architecture, security, tests, and example code for team review.
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
- [Cornell University Courses of Study](https://catalog.cornell.edu/)
- [AWS AI-DLC v1.0.1 release](https://github.com/awslabs/aidlc-workflows/releases/tag/v1.0.1)
- [AI-DLC v1.0.1 input quickstart](https://github.com/awslabs/aidlc-workflows/blob/v1.0.1/docs/writing-inputs/inputs-quickstart.md)
