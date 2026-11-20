# CourseCompass Components

[Design overview](application-design.md) · [Method contracts](component-methods.md) · [Services](services.md) · [Dependencies](component-dependency.md)

## Component boundaries

| Component | Responsibility | Interface |
| --- | --- | --- |
| Timetable UI | Streamlit chat, profile confirmation, weekly grid, individual alternative cards, and course details. Converts a drag or a click into the same course-change request. Shows conflicts, previews, and explicit confirmation controls. | Sends typed requests to the local API and renders a revisioned session view. Keeps temporary display state only. |
| Local API | Loopback FastAPI routes. Parses request bodies, resolves the local session, calls application services, and returns structured outcomes. | Pydantic request and response models. |
| Planning Service | Coordinates intake, profile confirmation, term data, scheduling, manual edits, conversation revisions, and explanation. Enforces transition order and determines which result is active. | Operations described in the method and service documents. Receives provider ports and storage interfaces. |
| Session Store | FastAPI-process memory keyed by local session ID. Owns the confirmed profile, active timetable, term snapshot reference, working course history, and one pending edit or recommendation. | Returns immutable versioned views. Applies guarded transitions against the expected revision and pending ID. |
| Conversation | Interprets messages into a structured profile draft or patch, asks for missing information, and explains validated facts, proposals, and limitations. Uses the configured OpenAI adapter or deterministic demo adapter. | Accepts minimized model context and a scoped tool allowlist. Returns validated drafts and grounded explanations. |
| Catalog and Evidence | Lists accessible published terms and subjects, loads the selected term through the Cornell adapter or a labeled local snapshot, and resolves course facts, section packages, and permitted evidence. | Provides immutable term snapshots, stable course and package IDs, provenance, and structured unavailable or stale flags. |
| Schedule Engine | Normalizes weights, ranks complete schedules and individual candidates, runs bounded search, checks feasibility independently of the optimizer, and constructs deterministic edit previews. | Receives the confirmed profile and a term snapshot. Returns validated schedules, alternative cards, or typed failure reasons. Has no provider or session dependency. |
| Completed-course Import | Parses a text-based PDF locally and returns proposed course identifiers for student confirmation. Offers manual entry if unsupported or unsuccessful. | Returns typed candidate identifiers or a manual-entry outcome. Raw bytes and extracted text are transient. |
| Preference Store | Saves and loads the current user's explicitly saved soft preferences and normalized weights. | Local SQLite behind a preference repository. Course history, session records and pending edits are excluded from this store. |

## Ownership

The UI owns presentation and any pending click or drag gesture. The Local API and Planning Service own request validation and permitted transitions. The Session Store owns the working revision. Catalog and Evidence owns retrieved facts and source provenance. The Schedule Engine decides feasibility. Conversation can advise and propose, and cannot commit a plan or modify confirmed constraints on its own.

A course may have multiple section packages. A package includes its lecture and all required discussion or lab meetings. The timetable draws every meeting in the selected package at its official time. Optional review text carries its source type and date when known. Unknown eligibility, unverified meetings, and missing evidence are explicit states. Evidence can influence an explanation or score only where its coverage supports the claim.
