# Subscriptions API Contract

Base path: `/api`

The API is local and unauthenticated for the single-user MVP. It does not contact subscription
addresses or return feed items.

## `GET /subscriptions`

Returns the subscriptions in insertion order.

- **Success**: `200 OK`
- **Content type**: `application/json`
- **Response**: Array of subscription objects. An empty collection is `[]`.

Example:

```json
[
  { "url": "https://example.test/feed.xml" }
]
```

## `POST /subscriptions`

Adds one subscription to the in-memory collection.

- **Request content type**: `application/json`
- **Request body**:

```json
{
  "url": "https://example.test/feed.xml"
}
```

- **Success**: `201 Created`
- **Response content type**: `application/json`
- **Response body**: The created subscription object.
- **Blank input**: `400 Bad Request`; no subscription is added.

The API rejects a null, empty, or whitespace-only `url`. For every other string, it stores and
returns the value as submitted without format validation or network access. Repeated values create
separate entries. No item identifier or item-specific route is defined.
