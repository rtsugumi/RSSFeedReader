# Implementation Plan: MVP RSS Reader

**Branch**: `main` | **Date**: 2026-10-17 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/001-mvp-rss-reader/spec.md`

## Summary

Deliver the single-user MVP for adding and viewing feed subscription addresses. Use the
stakeholder-selected ASP.NET Core Web API and Blazor WebAssembly UI, with an in-memory collection
and a small JSON contract for listing and adding entries. Reject only blank input; do not validate
URL format or contact the address. Feed retrieval, parsing, persistence, and removal are excluded.

## Technical Context

**Language/Version**: C# / .NET 10 (selected baseline; .NET 10 SDK 10.0.402 is installed)

**Primary Dependencies**: ASP.NET Core Web API; Blazor WebAssembly; .NET built-in JSON and
validation support; xUnit and ASP.NET Core integration-test support for automated tests

**Storage**: Process-local in-memory collection; no database or persistence

**Testing**: xUnit unit tests for blank-input and collection behavior; HTTP integration tests for
the add/list contract; browser acceptance checks for the primary user flow

**Target Platform**: Local desktop browser and ASP.NET Core process on Windows, macOS, or Linux

**Project Type**: Web application (separate API and browser UI)

**Performance Goals**: Add and display a subscription before the user's next action; complete the
primary manual add-and-confirm flow in under 30 seconds

**Constraints**: Single local user; accept any non-whitespace address without format validation;
no feed HTTP requests or parsing; duplicate entries are retained; in-memory state resets on
restart; local API/UI origins must be configured consistently

**Scale/Scope**: One subscription-management screen, one collection resource with list/add
operations, and one local user's current application run

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **Security at Trust Boundaries**: Pass. Treat the submitted text as data, reject blank input,
  render it as text, and make no outbound requests. No feed content is ingested in this MVP.
- **MVP Scope and Simplicity**: Pass. Only add/list and process-local memory are included.
- **Maintainable Separation of Responsibilities**: Pass. The UI owns interaction and display; the
  API owns the subscription collection and its HTTP contract.
- **Testable Quality**: Pass. Requirements have acceptance cases; test the collection, HTTP
  boundary, and UI flow without adding unrelated features.
- **Explicit Failures and Safe Diagnostics**: Pass. Blank input and failed API calls receive
  explicit feedback; no exceptions are presented as successful additions.

No gate violations identified.

## Project Structure

### Documentation (this feature)

```text
specs/001-mvp-rss-reader/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── subscriptions-api.md
└── tasks.md
```

### Source Code (repository root)

```text
backend/
└── RSSFeedReader.Api/
    ├── Program.cs
    └── Services/
        └── SubscriptionStore.cs

frontend/
└── RSSFeedReader.UI/
    ├── Pages/
    │   └── Subscriptions.razor
    └── wwwroot/
        └── appsettings.json

tests/
└── RSSFeedReader.Api.Tests/
```

These are the target locations documented by `StakeholderDocuments/TechStack.md`; the current
repository has no implementation projects yet. Establish only the API, UI, and focused test
projects required for this MVP. Do not add persistence, feed-processing, or production deployment
subsystems. Validate the single UI journey with the documented browser acceptance checks rather
than introducing a separate UI automation framework for this MVP.

**Structure Decision**: Use the documented backend/frontend split to honor the selected
ASP.NET Core API and Blazor WebAssembly architecture. Keep the in-memory collection behind a
small API-owned service and test API behavior through HTTP; keep the UI focused on the one
subscription workflow.

## Constitution Check (Post-Design)

- **Security at Trust Boundaries**: Pass. The URL is never dereferenced; the UI displays the
  submitted string as text. Blank-only rejection is not feed URL format validation.
- **MVP Scope and Simplicity**: Pass. The contract exposes only collection GET and add POST.
- **Maintainable Separation of Responsibilities**: Pass. The UI calls the API; the API owns
  collection state.
- **Testable Quality**: Pass. Unit, HTTP integration, and manual UI validation correspond to the
  feature acceptance criteria.
- **Explicit Failures and Safe Diagnostics**: Pass. Invalid blank input and request failures are
  described as unsuccessful; diagnostics must not include unnecessary sensitive data.

No design-stage gate violations identified.

## Complexity Tracking

No constitution violations requiring justification.
