# Quickstart: MVP RSS Reader Validation

This guide validates the subscription add/list flow. The repository currently contains planning
documents but no implementation solution or projects; run these checks once the implementation
projects exist at the paths described in [plan.md](plan.md).

## Prerequisites

- .NET 10 SDK
- A browser
- The API and UI projects from the implementation

## Build and automated tests

From the repository root:

```powershell
dotnet build
dotnet test
```

Expected result: the API, UI, and test projects build, and all tests pass.

## Run and verify the user flow

1. Start the API and UI using their project launch profiles. Confirm the UI's configured API base
   address matches the API's local address and the API allows the UI's exact local origin.
2. Open the UI in a browser. The subscription list is initially empty.
3. Enter `https://example.test/feed.xml` and submit it.
4. Confirm the address appears immediately. Add a second address and confirm both entries remain
   visible.
5. Submit an empty or whitespace-only value. Confirm the UI prompts for an address and the list
   does not change.
6. Submit a non-empty malformed address such as `not a feed URL`. Confirm it is displayed without
   any request to that address.
7. Restart the API and confirm entries are gone, as expected for process-local storage.

## HTTP contract smoke check

With the API running at `http://localhost:5151`, use PowerShell:

```powershell
Invoke-RestMethod -Method Get -Uri 'http://localhost:5151/api/subscriptions'
Invoke-RestMethod -Method Post `
  -Uri 'http://localhost:5151/api/subscriptions' `
  -ContentType 'application/json' `
  -Body '{"url":"https://example.test/feed.xml"}'
Invoke-RestMethod -Method Get -Uri 'http://localhost:5151/api/subscriptions'
```

Expected result: the first GET returns an empty array, POST succeeds with the created
subscription, and the final GET includes the submitted URL. A blank `url` request must return
`400 Bad Request` without changing the list. A valid add must not trigger an HTTP request to the
feed URL.

The exact launch addresses can be adjusted to the project profiles; port numbers do not change
the contract.
