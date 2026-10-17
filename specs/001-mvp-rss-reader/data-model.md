# Data Model: MVP RSS Reader

## Subscription

Represents one feed address entered by the local user and displayed in the current subscription
list.

| Field | Type | Required | Rules |
|---|---|---:|---|
| `url` | String | Yes | Must contain at least one non-whitespace character. Preserve and display the submitted value. No syntax, scheme, or reachability validation is performed. |

### Relationships and lifecycle

- The current run contains an ordered list of zero or more subscriptions.
- Duplicate values are allowed; each successful add creates another list entry.
- Entries are held only for the current process and are discarded when it stops.
- No stable identifier is required because the MVP exposes no per-subscription operations.

### State transitions

`Submitted non-empty value` → `Added to current list` → `Returned by list operation`

An empty or whitespace-only submission is rejected and does not change the list.
