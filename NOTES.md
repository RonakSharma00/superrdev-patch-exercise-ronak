# Patch Notes

## 1. Fix task search filter precedence

**Issue:** Search and status filters could return incorrect results because the SQL `AND`/`OR` conditions were not grouped correctly.

**How found:** Tested task search with a status filter and reviewed the repository query.

**What changed:** Grouped the title/description search conditions and applied the archived and status filters to the complete search condition.

**Why:** Ensures archived tasks and status filtering are consistently respected.

## 2. Remove artificial search delay

**Issue:** Every search request introduced an artificial delay based on query length.

**How found:** Reviewed the request handling path and found a `Thread.sleep()` tied to a calculated query complexity.

**What changed:** Removed the artificial delay and unused complexity calculation.

**Why:** Search requests should not intentionally block based on query length.

## 3. Validate pagination parameters

**Issue:** Invalid page and page-size values could produce invalid pagination behavior.

**How found:** Tested the API with invalid pagination parameters.

**What changed:** Added validation requiring `page >= 1` and `pageSize` between 1 and 100.

**Why:** Prevents invalid requests from reaching pagination logic.

## 4. Handle invalid status values

**Issue:** An unsupported status value could cause `TaskStatus.valueOf()` to throw an exception and result in a server error.

**How found:** Tested the API with an invalid status value.

**What changed:** Added validation around status parsing and return HTTP 400 for invalid values.

**Why:** Invalid client input should receive a clear client error instead of an unexpected server error.

## 5. Reset pagination when filters change

**Issue:** Changing the search query or status while viewing a later page could request a page that does not exist for the new filtered result set.

**How found:** On page 2, searching for a term with fewer than 10 matching tasks returned no tasks even though matching tasks existed.

**What changed:** Reset the current page to page 1 whenever the search query or status filter changes.

**Why:** Filter changes can reduce the result set, so pagination must restart from the first page.

## Assumptions

- The existing API response format was preserved.
- The existing page size of 10 was preserved.
- No unrelated feature requests or architectural changes were introduced.
- Existing dependency and project configuration were left unchanged.
