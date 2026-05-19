# Operation M2: Selective Removal

**Objective:** [[Work/Challenges#Operation M2 Selective Removals]]

---

## Overview

Operation M2 removes one or more uploads on demand. Given a list of one or more `upload_id` values, it cascades the deletion through both databases — removing all post records from MongoDB and the upload record from MariaDB for each entry in the list.

The list is populated by the user via checkbox selection in the Raven-Jakarta-Web upload listing. The same operation is also accessible from Raven-Jobs for command-line use during testing or maintenance.

There is no tombstone pattern. M2 is user-triggered and interactive — per-upload result reporting to the UI serves as the safety net. The user sees exactly what succeeded and what failed, and retries any failures manually.

---

## Proposed Changes

### Raven-Processor

The bulk of the deletion logic already exists from M1. The primary change is extracting it into a shared, reusable method so both M1 and M2 call the same code.

---

#### [MODIFY] Maria_DAO.java

The `deleteUpload(long uploadId)` method already exists from M1. No change needed — M2 will call it directly.

#### [MODIFY] MongoDao.java

The `deletePosts(long uploadId)` method already exists from M1. No change needed — M2 will call it directly.

#### [NEW] M2Service.java

A new service class that accepts a list of `upload_id` values and processes each one in sequence. Returns a per-upload result list to the caller.

**Public entry point:**
```
public List<M2Result> execute(List<Long> uploadIds)
```

**M2Result** — a simple result object per upload:

| Field | Type | Notes |
|-------|------|-------|
| `uploadId` | long | The upload processed |
| `postsDeleted` | long | Count of MongoDB documents deleted |
| `success` | boolean | True if both deletes completed |
| `message` | String | Error detail if success is false |

**Logic per upload_id:**
```
For each uploadId in the list:

    try:
        postsDeleted = MongoDao.deletePosts(uploadId)
        Maria_DAO.deleteUpload(uploadId)
        results.add(M2Result(uploadId, postsDeleted, success=true))

    catch exception:
        log error (uploadId, exception message)
        results.add(M2Result(uploadId, 0, success=false, message=exception))
        continue to next uploadId
```

A failure on one upload does not abort the rest of the list.

---

### Raven-Jakarta-Web

This is the primary trigger point for M2 in normal use.

---

#### [MODIFY] ConsoleBean.java

`ConsoleBean` already holds the upload list. Add the following:

- A `List<Long> selectedUploadIds` field — bound to the checkboxes in the DataTable, populated when the user makes selections
- A `List<M2Result> removalResults` field — populated after `M2Service.execute()` returns, used to drive the result display
- A `removeSelected()` action method:

```
public void removeSelected():
    if selectedUploadIds is empty: return
    removalResults = M2Service.execute(selectedUploadIds)
    selectedUploadIds.clear()
    reload the upload list
```

---

#### [MODIFY] uploads listing page (JSF — the ConsoleBean view)

**Toolbar — add above the DataTable:**

```
[All]  [None]       [Remove]
```

- **[All]** — JavaScript only, no server round-trip:
```javascript
document.querySelectorAll('input[type=checkbox]').forEach(cb => cb.checked = true);
```

- **[None]** — JavaScript only:
```javascript
document.querySelectorAll('input[type=checkbox]').forEach(cb => cb.checked = false);
```

- **[Remove]** — JSF command button, triggers `ConsoleBean.removeSelected()`. Includes an inline confirmation guard:
```
onclick="return confirm('Remove selected uploads? This cannot be undone.')"
```
The visual gap between [None] and [Remove] is intentional — [All]/[None] are harmless selection helpers; [Remove] is destructive. Keep them separated.

**Checkboxes — wire to `ConsoleBean.selectedUploadIds`:**

Each row's checkbox should add/remove that row's `upload_id` from `selectedUploadIds` when toggled.

**Result display — render below the toolbar after removal:**

Show a summary table driven by `ConsoleBean.removalResults`. Render only when `removalResults` is non-empty:

| Upload ID | Posts Removed | Status |
|-----------|---------------|--------|
| 301 | 482 | ✓ Removed |
| 302 | 0 | ✗ Failed — [message] |

The DataTable below refreshes automatically (upload list reloaded in `removeSelected()`) so removed entries disappear from the listing.

---

### Raven-Jobs

For command-line access — consistent with the M1 pattern and useful for testing before the UI is wired.

---

#### [MODIFY] App.java

Add an `M2` option that accepts one or more `upload_id` values as arguments and invokes `M2Service.execute()`.

Invocation:
```bash
# Single upload
mvn exec:java -Dexec.mainClass="us.deans.raven.App" -Dexec.args="M2 301"

# Multiple uploads
mvn exec:java -Dexec.mainClass="us.deans.raven.App" -Dexec.args="M2 301 302 303"
```

Console output should mirror the per-upload result structure — one line per upload showing upload_id, posts deleted, and success/failure.

---

## Key Rules Summary

| Rule | Behaviour |
|------|-----------|
| List size | Minimum one upload_id. No upper limit. |
| Deletion order | MongoDB first, then MariaDB — consistent with M1 sweep |
| Partial failure | Log and report. Continue processing remaining uploads in the list. |
| No tombstone | M2 is interactive — UI result reporting replaces the tombstone safety net |
| Retry | User re-selects and resubmits any failed uploads manually |

---

## Verification Plan

### Automated Tests (JUnit 5 / Mockito — already in project)

- Mock `MongoDao.deletePosts()` and `Maria_DAO.deleteUpload()` to simulate success
- Assert `M2Result` contains correct `postsDeleted` count and `success = true`
- Simulate `MongoDao.deletePosts()` throwing an exception — assert `success = false`, error message captured, remaining uploads in list still processed
- Simulate a single-item list — assert behaves identically to multi-item list

### Manual Verification (via Raven-Jobs, before UI is wired)

1. Identify one or more test `upload_id` values to remove
2. Run: `mvn exec:java -Dexec.mainClass="us.deans.raven.App" -Dexec.args="M2 [upload_id]"`
3. Confirm console output shows expected post count deleted
4. Verify in MariaDB: `SELECT * FROM uploads WHERE upload_id = ?` returns no rows
5. Verify in MongoDB: `db.posts.find({upload_id: "?"})` returns no documents

### Manual Verification (via Raven-Jakarta-Web, after UI is wired)

1. Open upload listing in Raven-Jakarta-Web
2. Select one or more uploads via checkboxes
3. Click "Remove Selected" and confirm
4. Verify result table shows correct counts and success status
5. Verify upload listing refreshes and removed entries are gone
