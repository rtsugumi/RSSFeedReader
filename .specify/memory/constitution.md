<!--
Sync Impact Report
Version change: unversioned scaffold -> 1.0.0 (initial project-specific constitution)
Modified principles: unresolved scaffold -> I. Security at Trust Boundaries; II. MVP Scope and Simplicity; III. Maintainable Separation of Responsibilities; IV. Testable Quality; V. Explicit Failures and Safe Diagnostics
Added sections: Project Constraints; Development and Review Workflow
Removed sections: None
Follow-up TODOs: Confirm the original ratification date.
-->

# RSS Feed Reader Constitution

## Core Principles

### I. Security at Trust Boundaries
Treat user-supplied feed URLs and remotely supplied feed data as untrusted whenever those
capabilities are introduced. Validate inputs at the receiving boundary, restrict feed requests to
permitted network destinations, and render feed content as text unless it has been safely
sanitized. Never expose credentials, sensitive configuration, or private data in user-visible
errors or logs. For the MVP, which performs no feed requests, do not add network behavior just to
apply controls for a future feature.

### II. MVP Scope and Simplicity
The MVP MUST let a user add a feed URL and see the subscription list; it MUST NOT fetch, parse,
persist, or remove subscriptions unless the approved feature scope is amended. Keep the
implementation proportionate to this proof of concept, prefer the simplest design that satisfies
accepted requirements, and defer future capabilities rather than introducing speculative
abstractions. This keeps the demonstration focused and its behavior verifiable.

### III. Maintainable Separation of Responsibilities
Keep subscription and feed operations in the ASP.NET Core API and user interaction in the Blazor
WebAssembly UI. Define clear contracts between them, keep each component responsible for its own
behavior, and avoid duplicated business rules. Add an abstraction only when it clarifies a current
requirement or enables an approved extension; this supports change without burdening the MVP.

### IV. Testable Quality
Every user-visible behavior MUST have verifiable acceptance criteria. Changes MUST build
successfully and have automated tests for the behavior they add or alter; test API/UI integration
when a change crosses that boundary. Tests and reviews MUST cover relevant normal, invalid, and
failure cases, including security boundaries when network fetching or rich content is introduced.
This makes correctness and regressions observable rather than assumed.

### V. Explicit Failures and Safe Diagnostics
When an operation can fail, report a clear, actionable outcome to the user and record useful
diagnostics at the responsible boundary without leaking sensitive values. Do not swallow
unexpected errors or present a failed operation as successful. Keep responses and messages
proportionate to the MVP; handle network and feed errors when those operations enter scope.

## Project Constraints

The supported product is a single-user, locally run RSS/Atom subscription-management proof of
concept, developed and tested on Windows, macOS, and Linux. The project uses an ASP.NET Core Web
API and a Blazor WebAssembly UI. For the MVP, subscriptions may be held in memory and lost when
the application stops; feed fetching, parsing, URL validation, persistence, and subscription
removal are out of scope. Future changes such as manual feed refresh and title/link item display
require explicit feature scope and must retain the security and quality principles above.

## Development and Review Workflow

Changes MUST be scoped to an approved requirement and preserve the documented MVP boundary.
Before completion, verify the affected projects build, run the relevant automated tests, and
exercise the user-visible flow across the API/UI boundary when applicable. Review changes for
principle compliance, input and output safety, clarity, and unnecessary complexity. Record any
known limitation that prevents a required check from passing; do not imply that an unrun check
passed.

## Governance

This constitution governs project decisions and takes precedence over conflicting local
development conventions. Amend it through a documented change that explains the motivation and
impact; review amendments against existing feature requirements and update related guidance when
the change affects it. A change that adds or materially expands principles or requirements
increments the MINOR version; a backward-incompatible removal or redefinition increments MAJOR;
clarifications and non-semantic wording changes increment PATCH. Record the amendment date and
version in this document.

Every change review MUST consider the applicable principles and workflow checks. If a principle
cannot be met, document the concrete reason and the narrowest acceptable exception before
proceeding; security controls cannot be waived where user input, remote content, or sensitive data
is exposed.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): original adoption date is not documented | **Last Amended**: 2026-10-17
