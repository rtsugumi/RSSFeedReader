# Research: MVP RSS Reader

## Runtime and project baseline

- **Decision**: Target .NET 10 for the new API and Blazor WebAssembly projects.
- **Rationale**: .NET 10 is the current supported .NET release in the planning context, its SDK
  (10.0.402) is installed, and the stakeholder documents select ASP.NET Core and Blazor. Use the
  smallest project setup that supports those choices.
- **Alternatives considered**: Earlier .NET target frameworks would require choosing an older
  baseline without repository compatibility requirements or installed-SDK evidence. No existing
  project files constrain the target.

## API shape and HTTP semantics

- **Decision**: Expose `GET /api/subscriptions` and `POST /api/subscriptions`. Return `200 OK` with
  a JSON array for GET, including `[]` when empty. Accept a JSON object with a `url` string for
  POST; return `201 Created` with the created subscription representation. Do not add item IDs,
  item routes, or feed operations.
- **Rationale**: A small resource-oriented Minimal API matches a new API with only two operations.
  GET is safe and repeatable; POST creates an entry. The collection URL is sufficient for this
  MVP, which does not need a stable item identity.
- **Alternatives considered**: Controllers are supported but add ceremony for this small surface.
  A `GET /{id}` route and stable identifiers would enable a per-item `Location` but exceed the
  user-facing requirements. HTTP references: [RFC 9110 GET](https://www.rfc-editor.org/rfc/rfc9110.html#section-9.3.1),
  [RFC 9110 POST](https://www.rfc-editor.org/rfc/rfc9110.html#section-9.3.3),
  [ASP.NET Core APIs overview](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/apis?view=aspnetcore-10.0).

## Validation and client feedback

- **Decision**: Reject null, empty, or whitespace-only values at the API boundary and in the UI.
  Preserve any non-whitespace value as entered; do not check URL syntax, scheme, reachability, or
  feed validity. Show an explicit prompt for blank input.
- **Rationale**: This satisfies FR-004/FR-005 and avoids unnecessary network behavior while
  enforcing the minimal input invariant. Server-side enforcement prevents bypassing client
  feedback.
- **Alternatives considered**: Absolute HTTP/HTTPS URI validation and trimming/canonicalization
  would change the explicitly permissive MVP behavior. Blazor's `EditForm` with required
  validation is documented as a straightforward UI option; an explicit API blank check is
  sufficient for the minimal endpoint.
- **References**: [Blazor form validation](https://learn.microsoft.com/en-us/aspnet/core/blazor/forms/validation?view=aspnetcore-10.0),
  [ASP.NET Core validation](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/validation?view=aspnetcore-10.0#register-validation-services).

## UI/API integration and CORS

- **Decision**: Configure the client API base address from application configuration. Since the
  stakeholder setup runs the browser UI and API on separate localhost ports, configure a named
  CORS policy for the exact local UI origins and only the methods/headers needed by this
  unauthenticated workflow. After a successful POST, reload the collection from the API; show
  failure feedback and do not clear the form on an unsuccessful request.
- **Rationale**: Explicit configuration avoids inconsistent hard-coded URLs; narrow CORS permits
  the intended local browser request without allowing arbitrary origins.
- **Alternatives considered**: Same-origin hosting or a development proxy can remove the need for
  cross-origin requests but are not the documented local topology. `AllowAnyOrigin` and
  credentials are unnecessary.
- **References**: [Blazor call a web API](https://learn.microsoft.com/en-us/aspnet/core/blazor/call-web-api?view=aspnetcore-10.0),
  [ASP.NET Core CORS](https://learn.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-10.0#cors-with-named-policy-and-middleware).

## State and testing

- **Decision**: Keep subscription state in a process-local collection owned by the API. Test
  collection behavior with focused unit tests and verify the HTTP add/list sequence with an
  ASP.NET Core integration test; check the complete UI flow manually in a browser.
- **Rationale**: In-memory state is specified and avoids introducing storage dependencies.
  Integration tests cover routing, JSON binding, and response semantics across the API boundary.
- **Alternatives considered**: Database persistence, browser-local storage, and feed fetching
  contradict the current MVP boundary. Testing every pipeline permutation is unnecessary; focus
  on empty list, successful add/list, blank input, and request failure.
- **Reference**: [ASP.NET Core integration tests](https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-10.0).
