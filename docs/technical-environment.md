# Technical Environment Document — CourseCompass

**Status:** Original baseline approved at e45ec09. The timetable interaction revision was approved with Workflow Planning at f36a8ed.
**AI-DLC:** AWS AI-DLC v1.0.1, using OpenAI Codex with versioned rules.  
**Deployment:** Local-only web application for course demo; hosted LLM API is allowed.
**Local data:** Only the current user’s explicitly saved preferences inform future recommendations. Completed-course identifiers are confirmed by the user.

## 1. Language, Version, and Package Manager

| Category | Choice | Rationale |
| --- | --- | --- |
| Required language | Python 3.12 | Single language across API, optimizer, and UI |
| Package manager | `uv` | Reproducible lockfile; commit `uv.lock` and `pyproject.toml` |
| Configuration | Pydantic v2 + `pydantic-settings` | Typed and validated request/environment configuration |
| Formatting/linting | Ruff | Consistent Python style |
| Optional markup | Markdown | Project and AI-DLC input documents |
| Prohibited | JavaScript/TypeScript application framework in MVP | Avoid separate frontend build complexity; use Streamlit |

## 2. Frameworks and Libraries

| Component | Planned technology | Purpose |
| --- | --- | --- |
| Web UI | Streamlit | Chat, preference controls, a prefilled timetable, individual alternative cards, and a course-detail panel. Validate drag and click interaction support during design and Construction. |
| Local API | FastAPI + Uvicorn | Typed endpoints for planning and preference updates |
| Data validation | Pydantic v2 | Validate LLM output and normalized Cornell records |
| Cornell retrieval | `httpx` | HTTP client with timeouts/retries and user-agent |
| Optimization | OR-Tools CP-SAT | Feasible course/section combination search |
| Data processing | Python standard library; `pandas` only if needed | Normalize records and display tables |
| Storage | SQLite via standard-library `sqlite3` | Local cache of course records and approved evidence, no account needed |
| LLM inside product | OpenAI `gpt-4.1-mini` via official Python SDK | Preference parsing, clarification, and evidence-based explanations. Integrate during Construction, with budget and owner confirmed before the first paid call. |
| Agent orchestration | Small custom finite-state workflow + validated tool calls | Predictable behavior and testability; no need for heavyweight agent platform |
| Test framework | `pytest`, `pytest-cov`, FastAPI `TestClient` | Unit and integration coverage |

The planned inference service is OpenAI gpt-4.1-mini. Construction will verify runtime access and confirm the budget and owner before any paid call.

## 3. Cloud Provider and Deployment Model

**No cloud infrastructure or hosting is required.** The app runs on a team's laptop. Start the API using Uvicorn on `127.0.0.1:8000`, then the Streamlit UI on `127.0.0.1:8501`. SQLite data and curated fixtures live locally. The app can call a hosted OpenAI model through HTTPS using a user-supplied environment variable. No student Cornell account or university sign-in is needed.

**Allowed external services:** Cornell Class Roster published data (subject to API rate limits and terms), OpenAI API for model inference, and publicly accessible official Cornell program and course pages where use is permitted. **Disallowed:** unapproved web scraping, external telemetry, cloud databases, automatic enrollment APIs, and remote upload of student transcripts.

## 4. Proposed Project Structure

```text
src/coursecompass/
  api/             # FastAPI endpoints and request schemas
  agent/           # conversation state, extraction, explanation, tool dispatcher
  data/            # Cornell adapter, normalizer, SQLite cache, fixture loader
  domain/          # course, section, preferences, schedule models
  scoring/         # relevance and user-weight normalization
  optimizer/       # CP-SAT constraints, recommendation, and swap validation
  security/        # input, prompt and provenance guards
  ui/              # Streamlit app
tests/            # unit, integration, and synthetic scheduling tests
fixtures/         # synthetic/attributed permitted demo data
docs/             # vision and technical environment inputs
aidlc-docs/       # generated and managed ONLY by AI-DLC
AGENTS.md         # official AI-DLC v1.0.1 Codex rule
.aidlc-rule-details/  # official AI-DLC v1.0.1 details
```

Actual Python package files will be created during **Construction**, not before the Nov 24 milestone. The example snippets below are input-document guidance, not runnable code files in the repository yet.

## 5. Architecture and Boundaries

**Request path:** Streamlit → FastAPI → validated conversation preference profile → Cornell adapter/cache → course candidate scoring → OR-Tools scheduler → structured plan response → Streamlit. The LLM may call only exposed tools (`search_courses`, `get_course_details`, `update_preferences`, `optimize_schedule`) with validated arguments. The optimizer, not the LLM, is the source of truth for feasibility.

The response carries one recommended timetable when a feasible result is available, plus individual alternative courses. A course edit previews the affected section packages and checks the resulting timetable before an explicit commit. The click action supports the same edit workflow as dragging.

**Primary internal entities:** `Course` (id, subject, title, credits, instructor, official source); `SectionPackage` (all required meeting components, times); `StudentProfile` (term, credits, mandatory/excluded courses, time blocks, self-reported eligibility, normalized weights); `Evidence` (source URL/type/date/coverage/text); `Schedule` (selected packages, credits, score, constraint-validation status); and `ConversationState` (confirmed constraints, optional preferences, bounded history).

**Scoring:** Nonnegative editable weights for career/research fit, interest, teaching evidence, workload fit, difficulty fit, and convenience. Normalize their sum to one. Never create unsupported numeric ratings from missing evidence; renormalize over available criteria and surface uncertainty. Optimize whole schedules and penalize excessive combined workload or gaps only when those estimates are grounded. Avoid a raw sum of course scores that unconditionally rewards extra credits: use fixed credit targets or an appropriate normalization.

**Constraint invariants:** total credits within user bounds; no overlapping required meetings; no duplicate course; user-pinned courses treated as mandatory within the supported scope; unmet or unverified eligibility must be surfaced for clarification or a no-solution response, never handled by silently dropping a pinned course; excluded courses absent; chosen sections correspond to the selected semester; mandatory components and unavailable blocks honored. If any information needed to verify feasibility is absent, exclude that candidate or clearly mark it unverified rather than asserting it is feasible.

## 6. API Conventions

- JSON HTTP endpoints under `/api/v1/`; explicit Pydantic request and response models.
- Endpoint examples: `POST /api/v1/preferences/parse`, `POST /api/v1/schedules/recommend`, `GET /api/v1/courses/{course_id}`.
- Separate outbound adapters from deterministic business rules; all external calls have timeouts, bounded retries, and cache where appropriate.
- Preserve source URL, retrieved timestamp, data kind (official vs review), and unknown fields in the evidence layer.
- Avoid sending complete conversation histories or personal schedules to third-party tools unless necessary for requested inference.

## 7. Security Basics

**Authentication:** None for the local, single-user MVP; bind both services to loopback (`127.0.0.1`). Never expose a publicly reachable server or store institutional credentials.  
**Secret management:** Read `OPENAI_API_KEY` from a local `.env` or process environment; `.env`, local SQLite caches containing personal details, and any secrets must be excluded from Git. Provide `.env.example` with variable names only.  
**Input validation:** Pydantic constrains message length, credit range, course count, subject/term values, and normalized weights; reject unknown tool names/parameters.  
**Prompt injection:** Treat course descriptions, websites, and reviews as untrusted data; never obey embedded instructions, never allow LLM-generated shell commands, and use allowlisted tool functions only.  
**Transport/privacy:** Hosted model calls use HTTPS. Do not require student identity, NetID or Cornell login. Provide manual completed-course entry and local text-based PDF import. Extract only course identifiers needed for eligibility and duplicate checks and ask the user to verify them. Keep original PDFs and raw transcript text out of model calls, persistent storage, audit logs and Git. Offer manual entry if extraction fails or the PDF is scanned. Do not log API keys or full conversations. Provide a clear warning before text is sent to a hosted provider.
**Risk baseline:** Follow relevant OWASP ASVS input-validation and secret-handling guidance and OWASP LLM prompt-injection principles, scoped to a local course project.

## 8. Prohibited Libraries and Patterns

| Prohibited | Reason | Alternative |
| --- | --- | --- |
| `eval`, `exec`, arbitrary LLM-generated Python/shell execution | Code execution/security risk | Explicit allowlisted tool methods and validated structured parameters |
| Selenium / Playwright-based unauthorized site scraping | Terms and fragility | Cornell official APIs and approved/manual source collection |
| Unofficial Rate My Professors scraping packages | Permission/reliability risk | Permissioned or manually curated attributed evidence; no-review fallback |
| Flask / Django for backend | Avoid parallel server frameworks | FastAPI |
| React / Next.js in MVP | Avoid separate frontend stack | Streamlit |
| LangChain / LangGraph in MVP | Unnecessary dependency and abstraction overhead for focused agent | Custom bounded workflow with OpenAI SDK + Pydantic |
| Cloud DB (Firebase, Supabase, DynamoDB) | Not required for local-only MVP | SQLite / checked-in synthetic fixtures |
| `pickle` for untrusted cached data | Unsafe deserialization | JSON / validated Pydantic records |
| Direct LLM scheduling without independent validation | Hallucinated or conflicting results | OR-Tools plus deterministic post-solve verification |
| Hard-coded credentials or committed `.env` | Secret leakage | Environment variables and gitignore |

## 9. Testing and Quality Gates

| Type | Tool | Required checks |
| --- | --- | --- |
| Unit tests | pytest | Credit total, overlap, prerequisite flags, weight normalization, missing reviews |
| Property-based tests | Hypothesis with pytest | Partial AI-DLC PBT coverage for pure functions and serialization round-trips. Use domain generators, shrinking and reproducible seeds. |
| Optimizer verification | pytest + exhaustive enumeration | For small instances, match optimal objective and never violate hard constraints |
| API contract tests | FastAPI TestClient | Valid requests, malformed input, no-solution response, stable JSON schema |
| LLM agent tests | Mock client + labeled user utterances | JSON extraction, clarification, revisions retaining prior hard constraints |
| Data adapter tests | Stored Cornell-style fixtures | Handle field omissions, multiple section components, stale source data |
| Safety tests | pytest | Reject malicious tool names, oversize input, injection-like source instructions |
| Manual demo | Scripted walkthrough | Initial timetable with individual alternatives, confirmed and rejected swaps, conversational preference change, infeasible case |

**Targets (for review):** All emitted schedules must pass supported hard-constraint checks; output schemas and claimed course facts must be checked; conversational edits must preserve unaffected constraints. Numeric coverage/accuracy and dataset-size targets from the earlier proposal are not reported as team-approved or achieved. Ruff and pytest are proposed Construction checks, not tests already run for this milestone.

## 10. Example Code — Typical Endpoint

```python
from fastapi import APIRouter
from pydantic import BaseModel, Field

router = APIRouter(prefix="/api/v1")

class RecommendRequest(BaseModel):
    min_credits: int = Field(ge=1, le=25)
    max_credits: int = Field(ge=1, le=25)

class RecommendResponse(BaseModel):
    recommended_schedule: dict | None = None
    alternative_courses: list[dict] = Field(default_factory=list)

@router.post("/schedules/recommend", response_model=RecommendResponse)
def recommend(request: RecommendRequest) -> RecommendResponse:
    if request.min_credits > request.max_credits:
        from fastapi import HTTPException
        raise HTTPException(status_code=422, detail="Invalid credit interval")
    # Replace with optimizer service during Construction.
    return RecommendResponse()
```

## 11. Example Code — Typical Deterministic Function

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Meeting:
    day: str
    start_minute: int
    end_minute: int

def meetings_overlap(a: Meeting, b: Meeting) -> bool:
    """Half-open intervals allow back-to-back meetings on the same day."""
    return a.day == b.day and max(a.start_minute, b.start_minute) < min(a.end_minute, b.end_minute)
```

## 12. Example Code — Typical Test

```python
from coursecompass.domain.meetings import Meeting, meetings_overlap

def test_overlapping_and_back_to_back_meetings() -> None:
    first = Meeting("MON", 600, 660)
    overlapping = Meeting("MON", 650, 700)
    back_to_back = Meeting("MON", 660, 720)
    different_day = Meeting("TUE", 600, 660)
    assert meetings_overlap(first, overlapping)
    assert not meetings_overlap(first, back_to_back)
    assert not meetings_overlap(first, different_day)
```

## 13. Source and Model Configuration Decisions

- [Cornell Class Roster](https://classes.cornell.edu/) provides course descriptions, published offerings, and section details for the selected term.
- [Cornell Programs of Study and Graduate Fields](https://catalog.cornell.edu/programs/) and corresponding official program pages provide published degree and program requirements. Use them as references for requirements the student confirms. The app checks the supported scheduling constraints for the semester.
- Coverage targets all accessible subjects in published Cornell terms. Each request uses one chosen term. Historical terms may provide context. Verify actual data coverage during Construction.
- Preferred inference provider: OpenAI API and model `gpt-4.1-mini`, requiring a team-controlled key; mock all inference for CI/tests.
- Restrict review data to demonstrably permitted sources; label any manually created illustrative cases as synthetic.
- Always record source provenance, and do not represent estimated course difficulty or quality as verified Cornell information.

## 14. AI-DLC Workflow Boundary

These files are **team-created input documents**, not generated Inception artifacts. Once team members review and agree to them, start AI-DLC v1.0.1 and allow the agent to create `aidlc-docs/` artifacts and log questions/approvals in `audit.md`. Complete Requirements, User Stories, Workflow Planning, Application Design, and Units Generation, then stop before Construction.
