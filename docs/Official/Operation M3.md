# Operation M3: Daily Verification

This operation verifies that the MariaDB `uploads` table and the MongoDB `posts` collection are in sync. It compares the total post count across both databases — `SUM(post_count)` from MariaDB against `countDocuments()` from MongoDB — and reports a match or mismatch. A mismatch indicates that a previous maintenance operation (M1 or M2) did not complete cleanly, or that data was written or deleted through an uncontrolled path.

On mismatch, M3 exits with code 1, which triggers the runner script to create a GitHub issue and send an email notification.

---

## User Review Required

> [!IMPORTANT]
> Before mismatch alerting will work, two prerequisites must be verified on **Vortex** (the machine running the cron job):
>
> 1. **gh CLI authentication**: `gh` is configured to use SSL on Tornado, but Vortex has not been confirmed. Run `gh auth status` on Vortex before the first scheduled run. If not authenticated, run `gh auth login`. Without a valid token, GitHub issue creation will fail silently on mismatch.
>
> 2. **sendmail / esmtp configuration**: `sendmail` is available on this system via `esmtp`. Before the first cron run, verify that esmtp is configured with a working relay and that a test message reaches `ncdeans@gmail.com`. Without a working relay, email notification will fail silently on mismatch.

---

## Proposed Changes

### Raven-Processor

The shared library provides the data access layer and orchestration logic for M3.

---

#### [NEW] M3Result.java

```
Path: src/main/java/us/deans/raven/processor/M3Result.java
Package: us.deans.raven.processor
```

Immutable result DTO. Follows M2Result pattern: all-args constructor, getters only, no setters.

| Field | Type | Notes |
|-------|------|-------|
| `mariaDbCount` | long | Total post count from MariaDB (`SUM(post_count)` from `uploads`) |
| `mongoDbCount` | long | Total document count from MongoDB `posts` collection |
| `match` | boolean | True if `mariaDbCount == mongoDbCount` |
| `message` | String | Structured summary for logging |

---

#### [MODIFY] Maria_DAO.java

```
Path: src/main/java/us/deans/raven/processor/Maria_DAO.java
```

Add one method:

```java
public long getPostCountSum() throws SQLException
```

- SQL: `SELECT COALESCE(SUM(post_count), 0) FROM uploads`
- Pattern: `openConnection()` → `PreparedStatement` → `ResultSet` → close
- On error: `logger.error(...)` then rethrow (matches newer DAO style)

---

#### [MODIFY] MongoDao.java

```
Path: src/main/java/us/deans/raven/processor/MongoDao.java
```

Add one method:

```java
public long getPostDocumentCount()
```

- MongoDB: `collection.countDocuments(new Document())`
- On error: log and rethrow (consistent with existing style)

---

#### [NEW] M3Service.java

```
Path: src/main/java/us/deans/raven/processor/M3Service.java
Package: us.deans.raven.processor
```

Orchestration service. Follows M2Service pattern exactly.

Constructor:
```java
public M3Service(Maria_DAO mariaDao, MongoDao mongoDao)
```
Constructor injection supports Mockito testing.

Logger:
```java
private static final Logger log = LoggerFactory.getLogger(M3Service.class);
```

Entry point:
```java
public M3Result execute()
```

Logic:
1. `long mariaCount = mariaDao.getPostCountSum()`
2. `long mongoCount = mongoDao.getPostDocumentCount()`
3. `boolean match = (mariaCount == mongoCount)`
4. Log at `INFO` if match, `ERROR` if mismatch — include both counts
5. Return `new M3Result(mariaCount, mongoCount, match, message)`

---

### Raven-Jobs

#### [NEW] OperationM3.java

```
Path: src/main/java/us/deans/raven/jobs/OperationM3.java
Package: us.deans.raven.jobs
```

Job orchestrator. Naming per user request (`OperationM3`, not `M3Job`). Follows `PruneJob` pattern.

Constructor: instantiates `Maria_DAO` and `MongoDao` directly.

Logger:
```java
private static final Logger log = LoggerFactory.getLogger(OperationM3.class);
```

Entry point:
```java
public M3Result run()
```

Logic:
- Instantiates `M3Service(mariaDao, mongoDao)`
- Calls `service.execute()`
- Logs result at `INFO` (match) or `ERROR` (mismatch)
- Calls `mongoDao.close()` in `finally` block (matches PruneJob)
- Returns `M3Result`

---

#### [MODIFY] App.java

```
Path: src/main/java/us/deans/raven/App.java
```

Add to switch expression:

```java
case "M3" -> {
    M3Result result = new OperationM3().run();
    if (!result.isMatch()) System.exit(1);
}
```

`System.exit(1)` on mismatch allows the bash runner to detect and act.

---

### Raven-Jobs (scripts)

#### [NEW] operation-m3.sh

```
Path: src/main/scripts/operation-m3.sh
```

Launcher script bundled inside Raven-Jobs. Follows the same ndjson structure as `operation-m1.sh`. M3-specific addition: on **exit code 1** (mismatch), the script fires two alerts:

```bash
# Create GitHub issue
gh issue create \
  --repo ndeans/Raven-Jobs \
  --title "Daily Verification: Mismatch $(date +%Y-%m-%d)" \
  --body "Mismatch detected. See logs: $LOG_DIR/$LOG_BASENAME.ndjson"

# Send email notification
printf "Subject: Daily Verification: Mismatch %s\n\nMismatch detected. See logs: %s/%s.ndjson\n" \
  "$(date +%Y-%m-%d)" "$LOG_DIR" "$LOG_BASENAME" \
  | sendmail ncdeans@gmail.com
```

Full counts are in the ndjson application log. The issue body and email intentionally use "see logs" — no parsing needed.

No wrapper script is needed. The launcher is deployed directly via the distributable ZIP and cron points to it directly.

---

# Walkthrough: Operation M3 — Daily Verification

## Changes Made

### Raven-Processor
- **`M3Result.java`** — new immutable result DTO (all-args constructor, getters only)
- **`Maria_DAO.java`** — added `getPostCountSum()`: `SELECT COALESCE(SUM(post_count), 0) FROM uploads`
- **`MongoDao.java`** — added `getPostDocumentCount()`: `collection.countDocuments(new Document())`
- **`M3Service.java`** — new orchestration service with constructor-injected DAOs; logs match at INFO, mismatch at ERROR

### Raven-Jobs
- **`OperationM3.java`** — new job orchestrator; follows PruneJob pattern; calls `mongoDao.close()` in finally block
- **`App.java`** — M3 case added to switch; exits with code 1 on mismatch

### Deployment (WP-5)
Scripts were consolidated into Raven-Jobs as part of WP-5:
- **`src/main/scripts/operation-m3.sh`** — launcher with mismatch alerting (gh issue + sendmail)
- **`pom.xml`** — Maven Assembly plugin added; `mvn verify` produces `target/raven-jobs-1.0-SNAPSHOT-dist.zip`
- Old locations retired: `Raven/bin/`, `Raven-Agentic/bin/`
- `raven-ops` project on Vortex archived (superseded by ZIP distribution)

---

## Verification Results

### First Run — 2026-04-23

```
M3: mariaDb=19840 mongoDb=19840 match=true
```

Both databases in sync. Exit code 0 — no alerts fired.

**Post-run fix:** The `logs/` directory was extracted as `root:root` (ZIP unpacked with `sudo`). Runner log writes failed with permission denied until ownership was corrected:
```bash
sudo chown -R ndeans:ndeans /opt/raven-jobs/logs
```
After the fix, both log files write correctly:
- `operation-m3_runner.ndjson` — bash bookend events
- `operation-m3_<RUN_ID>.ndjson` — Java application log

---

## How to Trigger

```bash
/opt/raven-jobs/bin/operation-m3.sh
```

To inspect the runner log after a run:
```bash
tail -20 /opt/raven-jobs/logs/operation-m3_runner.ndjson
```

To inspect the application log (structured output from M3Service):
```bash
ls -lt /opt/raven-jobs/logs/operation-m3_*.ndjson | head -3
```

---

# Deployment: Distributable ZIP & Cron Schedule

## Overview

M1, M2, M3, and the R7 test runner are all bundled in a single self-contained ZIP produced by Raven-Jobs. Scripts and JAR are co-located; no wrapper scripts or external deployment coordination is needed.

## Build

```bash
# 1. Build Raven-Processor first (shared library)
cd /home/ndeans/Projects/java_projects/Raven-Processor
mvn clean install

# 2. Build Raven-Jobs — produces both fat JAR and distributable ZIP
cd /home/ndeans/Projects/java_projects/Raven-Jobs
mvn clean verify
```

Output: `target/raven-jobs-1.0-SNAPSHOT-dist.zip`

## Deploy to Vortex

```bash
# Unpack ZIP to /opt/raven-jobs (standard Linux location for self-contained app bundles)
sudo unzip target/raven-jobs-1.0-SNAPSHOT-dist.zip -d /opt/
# The ZIP extracts to raven-jobs-1.0-SNAPSHOT/ — rename for a stable path:
sudo mv /opt/raven-jobs-1.0-SNAPSHOT /opt/raven-jobs
# Fix log directory ownership — ZIP extracts as root, scripts run as ndeans
sudo chown -R ndeans:ndeans /opt/raven-jobs/logs
```

To redeploy after a code change:
```bash
sudo rm -rf /opt/raven-jobs/lib /opt/raven-jobs/bin
sudo unzip target/raven-jobs-1.0-SNAPSHOT-dist.zip -d /tmp/raven-jobs-update
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/bin /opt/raven-jobs/
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/lib /opt/raven-jobs/
```

> [!NOTE]
> The `logs/` directory is preserved on redeploy — do not overwrite it.

## Deployment Layout

```
/opt/raven-jobs/
  ├── bin/
  │   ├── operation-m1.sh        # M1 — Remove Duplicates
  │   ├── operation-m2.sh        # M2 — Selective Removal
  │   ├── operation-m3.sh        # M3 — Daily Verification
  │   └── operation-r7-test.sh   # R7 — Conversation test runner
  ├── lib/
  │   └── raven-jobs-1.0-SNAPSHOT.jar   # Fat JAR (all ops bundled, ~11MB)
  └── logs/
      ├── operation-m3_runner.ndjson      # Bash bookend events
      └── operation-m3_<RUN_ID>.ndjson   # Java application log (M3Service output)
```

## Cron Schedule

M3 runs automatically every day at **6:00 AM** (same schedule as M1):

```
0 6 * * * /opt/raven-jobs/bin/operation-m3.sh
```

Add via:
```bash
crontab -e
```

> [!NOTE]
> Before adding the cron entry, complete the **User Review Required** prerequisites above — verify `gh auth status` and test `sendmail` on Vortex. A misconfigured notifier means a mismatch can go undetected.

Manage the schedule:
```bash
crontab -l    # view
crontab -e    # edit
```

To trigger manually (e.g. for initial verification):
```bash
/opt/raven-jobs/bin/operation-m3.sh
```
