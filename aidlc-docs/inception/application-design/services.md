# CourseCompass Application Services

[Design overview](application-design.md) · [Components](components.md) · [Method contracts](component-methods.md)

The Planning Service exposes these operations through the local API. It reads the current revision, performs provider or solver work against an immutable view, then applies changes through the Session Store only if the view is still current. The UI renders the returned view.

## Intake and confirmation

1. Open a local session and show the editable defaults. Restore saved soft preferences if the student asks.
2. Pass a bounded, minimized message and context to Conversation. Use its typed draft to show the term, credit bounds, pinned and excluded courses, unavailable blocks, interests, workload preferences, and any clarifying questions.
3. Offer locally extracted course IDs or manual entry. Let the student verify or edit the identifiers. Keep the raw PDF and raw text out of model requests, storage and logs. Preserve a distinction between completed courses, confirmed exclusions and unresolved repeat or prerequisite eligibility.
4. Record the confirmed profile before starting planning. An ambiguous model suggestion cannot modify a confirmed field. In particular, a soft preference cannot unlock a required course or relax a time block.

## Initial recommendation

1. Load and normalize data for the selected term. Build verified section packages from compatible required components. Retain snapshot identity and evidence availability.
2. Search and rank bounded feasible combinations, then run the independent feasibility check before selecting the best verified result found. If a bounded search returns a verified incumbent, disclose the search limit.
3. If a verified result is available, commit one starting timetable, calculate its total credits and individual alternative cards, and generate explanations from validated facts. Use evidence only where available. The explanations should remain useful on official course data alone.
4. If planning fails, preserve distinctions among confirmed infeasibility within the supported data, a required course needing clarification, incomplete catalog data, and timeout without a verified result. Explain checked constraints or limitations and offer user-controlled changes. The empty result view can still show eligible individual course details when reliable data exists.

## Manual course edit

1. Use the same endpoint for dragging or clicking an individual alternative. Resolve the course and package in the active term snapshot. The student may choose another supported section package if one is available.
2. Evaluate one incoming course and at most one outgoing course. For compatible additions, show the proposed meeting blocks and credit total. For conflicts, show the affected courses and whether a compatible section or valid one-for-one replacement is available. Where several removals are required, mark the edit blocked and explain the conflicts. A preview cannot remove a pinned course while the pin remains in effect.
3. Record the proposal by preview ID, base session revision and snapshot ID. Conflict cards use subdued styling plus text, and remain selectable for inspection. The preview describes the exact change, applicable constraints, affected sections and resulting credits.
4. Canceling only clears the matching preview. Confirming uses the stored payload, rechecks it against the current profile and snapshot, and applies the edit under a revision and preview guard. A stale or invalid preview leaves the active timetable unchanged. After a successful commit, return the new timetable, credits, and refreshed alternatives. A retry of a consumed preview must not reapply the edit.

## Conversational revision

1. Interpret the follow-up against the confirmed profile. Show a draft patch and ask about ambiguous or hard-constraint changes before applying the profile update.
2. For a soft preference change, keep the hand-edited timetable active and use it as input to the new search. If a different verified result is found, show the proposed changes and allow the student to accept or dismiss them. Accepting uses the same revision-checked confirmation path as a manual edit.
3. If a confirmed hard-constraint change invalidates the previous timetable, clearly distinguish it as a previous plan and show an updated result or explanation. Display a timetable as active only after validation against the latest constraints.
4. Any active timetable, confirmed profile, or catalog snapshot change advances the planning revision and invalidates prior previews. Late results cannot replace a newer plan or proposal. A newer pending proposal for the same revision supersedes an older one through its preview ID.

## Course details and fallback

Both course entry points resolve the same selected-term course ID and details. Show official facts, recommendation reasons, permitted candidate-specific reviews and separate workload or difficulty estimates with source and coverage. Instructor research is context. If evidence cannot support a claim, show the official facts, mark the gap, and explain the basis of any remaining comparison.

When the model or live roster is unavailable, follow the configured and visibly labeled demo path using a mock model or a compatible local snapshot or fixture. Read-only official details may still be available from a compatible snapshot. Keep live, cached, fixture and mock provenance distinguishable. If configuration or data cannot support the requested scenario, show a recovery outcome without claiming that the schedule was verified. Keep retries and search work bounded. Detailed budgets and source compatibility rules will be verified in Construction.

## Construction verification targets

| Flow | Observable target |
| --- | --- |
| Recommendation | One prefilled timetable and individual cards. All displayed meetings reflect verified section packages. |
| Manual edits | Add and swap previews agree across drag and click. Cancel, a stale confirmation and a blocked pin leave the timetable intact. An accepted valid edit refreshes the alternatives. |
| Chat revision | A soft update leaves active choices in place while the new proposal waits. A confirmed hard change cannot leave an invalid timetable active. |
| Source degradation | The system distinguishes unavailable reviews, limited roster coverage, search timeout and proven infeasibility. Demo provenance is visible. |
| Local privacy | Mock provider inputs and captured logs exclude raw transcript content and personal files. Only explicitly saved soft preferences survive the planning session. |
