# Tasks: MVP RSS Reader

**Input**: Design documents from `specs/001-mvp-rss-reader/`

**Prerequisites**: [plan.md](plan.md), [spec.md](spec.md), [research.md](research.md),
[data-model.md](data-model.md), [contracts/](contracts/)

**Tests**: Automated API/store tests are included because the project constitution requires
automated coverage for changed behavior. The specification does not require a test-first workflow.

**Organization**: Tasks are grouped by the sole prioritized user story to enable independent
implementation and validation of its complete subscription workflow.

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Initialize the smallest solution structure required by the plan.

- [ ] T001 Create a .NET 10 solution with ASP.NET Core API project at `backend/RSSFeedReader.Api/RSSFeedReader.Api.csproj`, Blazor WebAssembly project at `frontend/RSSFeedReader.UI/RSSFeedReader.UI.csproj`, and xUnit API test project at `tests/RSSFeedReader.Api.Tests/RSSFeedReader.Api.Tests.csproj`; add all projects to the solution, reference the API from its test project, and add `Microsoft.AspNetCore.Mvc.Testing` for HTTP integration tests.

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: No shared implementation foundation beyond solution/project setup is needed; the API and UI work belongs to the single user story.

**Checkpoint**: Project structure is ready for User Story 1.

---

## Phase 3: User Story 1 - Add and view feed subscriptions (Priority: P1) 🎯 MVP

**Goal**: Let the local user submit a non-empty feed address and immediately see it in the
subscription list, while blank submissions fail visibly and no feed requests are made.

**Independent Test**: Start the API and UI with an empty in-memory collection, add
`https://example.test/feed.xml`, verify it appears immediately, add a second address and verify
both remain visible; submit whitespace and verify the list is unchanged and the user is prompted;
confirm no request is sent to either feed address.

### Implementation for User Story 1

- [ ] T002 [US1] Define the `Subscription` representation with its required `url` string field in `backend/RSSFeedReader.Api/Models/Subscription.cs`; apply the data-model rule verbatim at the API boundary: “Must contain at least one non-whitespace character. Preserve and display the submitted value. No syntax, scheme, or reachability validation is performed.”
- [ ] T003 [US1] Implement an insertion-ordered, process-local subscription store that retains duplicates and safely supports concurrent API requests in `backend/RSSFeedReader.Api/Services/SubscriptionStore.cs`.
- [ ] T004 [US1] Implement `GET /api/subscriptions` returning `200 OK` and an empty JSON array when empty, plus `POST /api/subscriptions` returning `201 Created` and the new subscription; reject null, empty, and whitespace-only `url` values with `400 Bad Request`, preserve all other values unchanged, register the store for the application lifetime, configure CORS only for `http://localhost:5213` and `https://localhost:7025` in `backend/RSSFeedReader.Api/Program.cs`, set the API HTTP launch URL to `http://localhost:5151` in `backend/RSSFeedReader.Api/Properties/launchSettings.json`, and expose `Program` for `WebApplicationFactory` in `backend/RSSFeedReader.Api/Program.cs`.
- [ ] T005 [US1] Set the UI API base address to `http://localhost:5151/api/` in `frontend/RSSFeedReader.UI/wwwroot/appsettings.json` and configure the Blazor launch profiles to use `http://localhost:5213` and `https://localhost:7025` in `frontend/RSSFeedReader.UI/Properties/launchSettings.json`, matching the API's allowed CORS origins.
- [ ] T006 [US1] Implement the subscription screen in `frontend/RSSFeedReader.UI/Pages/Subscriptions.razor` with an empty-list state, URL entry and submit action, blank-input prompt, immediate list display after a successful add, preservation of the entered value and explicit feedback on API failure, and text-only rendering; call only the subscriptions API and never the submitted feed address.

### Automated Tests for User Story 1

- [ ] T007 [P] [US1] Add xUnit unit tests in `tests/RSSFeedReader.Api.Tests/SubscriptionStoreTests.cs` verifying an initially empty store, insertion order, and preservation of duplicate values.
- [ ] T008 [P] [US1] Add ASP.NET Core HTTP integration tests in `tests/RSSFeedReader.Api.Tests/SubscriptionsApiTests.cs` verifying empty-list `200`, successful add `201` and subsequent list, blank and whitespace-only `400` without state changes, unchanged acceptance of malformed non-empty values, and duplicate entries.

### Story Validation

- [ ] T009 [US1] Run `dotnet build` and `dotnet test`, then follow `specs/001-mvp-rss-reader/quickstart.md` to verify the browser add/list flow, blank-input feedback, duplicate behavior, process-restart data loss, and absence of feed-address requests.

**Checkpoint**: User Story 1 is complete when the API tests pass and the browser flow meets the
acceptance criteria without fetching or parsing any feed.

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: No additional features are in scope. Keep any final changes within the MVP boundary.

- [ ] T010 Confirm the final project structure and local API/UI origins are consistent with `specs/001-mvp-rss-reader/plan.md` and `StakeholderDocuments/TechStack.md`; update `specs/001-mvp-rss-reader/quickstart.md` if implemented project launch profiles require different runnable commands.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies; creates the solution and projects.
- **Foundational (Phase 2)**: No extra tasks; the setup output is sufficient to begin the story.
- **User Story 1 (Phase 3)**: Depends on T001. Within the story, T003 depends on T002; T004
  depends on T002 and T003; T005 depends on the API port and CORS origin established by T004; T006
  depends on the API contract (T004) and client configuration (T005); T007 depends on T003; T008
  depends on T004; T009 depends on T005-T008.
- **Polish (Phase 4)**: T010 depends on T009.

### User Story Dependencies

- **User Story 1 (P1)**: Depends only on setup; no other user stories exist.

### Parallel Opportunities

- T007 and T008 can run in parallel after the store/API implementation is complete because they
  target separate test files and validate different boundaries.
- No user-story phases can run in parallel because the feature has only one user story.

## Parallel Example: User Story 1

```text
After T004 is complete:
Task: Add store unit tests in tests/RSSFeedReader.Api.Tests/SubscriptionStoreTests.cs (T007)
Task: Add HTTP integration tests in tests/RSSFeedReader.Api.Tests/SubscriptionsApiTests.cs (T008)
```

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete T001 to establish the .NET solution and focused projects.
2. Complete T002-T006 to deliver the process-local store, API contract, local client setup, and
   subscription screen.
3. Complete T007-T008 and T009 to validate collection behavior, HTTP responses, and the browser
   journey.
4. Stop after User Story 1; feed retrieval, parsing, persistence, and removal remain out of scope.

### Incremental Delivery

The MVP consists solely of User Story 1. Do not add an additional user story or production
capability without an approved specification amendment.

## Notes

- Every task uses the required checkbox, sequential task ID, optional parallel/story labels, and
  an explicit file path.
- `[P]` marks only independent tasks in separate files.
- Tests are included to satisfy the constitution's automated quality requirement; they are grouped
  after the implementation tasks because test-first development was not requested.
- `url` value constraints are quoted from the data model in T002 and T004.
- Build and test commands become runnable after T001 creates the solution and projects.
