
# Raven System Overview

The Raven System provides tools to extract the content of online discussions and store them in a dual-database back-end, from which conversations can be reconstructed and analysed.

The system consists of six projects spanning data extraction, storage, maintenance, and analysis. Maintenance operations (M-series) run as standalone jobs via Raven-Jobs. Research operations (R-series) are served through Raven-Jakarta-Analyzer, a JSF web application that allows users to query and visualize the data. Both operation types are backed by shared business and query logic in Raven-Processor.

Raven-Analyzer revolves around using both databases together to reconstruct conversations and find patterns of interest — queries that can't be done with typical tools because of the structural difference between MariaDB (relational, upload/topic metadata) and MongoDB (document store, post content).

---

## Project Structure

| Project                | Framework     | Deployed As          | Purpose                     |
| ---------------------- | ------------- | -------------------- | --------------------------- |
| OPP-Userscripts        | TamperMonkey  | userscript           | Extractor userscript        |
| Raven-Processor        | Java Core     | library (jar)        | Shared business/query logic |
| Raven-Jobs             | Java Core     | standalone (jar)     | Maintenance operations      |
| Raven-Jakarta-JAXRS    | Jakarta JAXRS | Raven-JAXRS (war)    | Inbound data web service    |
| Raven-Jakarta-Web      | Jakarta JSF   | Raven-Web (war)      | Display application         |
| Raven-Jakarta-Analyzer | Jakarta JSF   | Raven-Analyzer (war) | Research query UI           |

### Architectural Notes

- Business and query logic lives in Raven-Processor, keeping UI tiers thin
- Raven-Jobs is a plain Java standalone JAR — no Jakarta dependency needed at the business or data tier
- Raven-Jakarta-Analyzer is a JSF web application running alongside Raven-Jakarta-Web on Wildfly

---

## Database Schema

**MariaDB — `uploads` table**

| Column      | Notes                                          |
| ----------- | ---------------------------------------------- |
| upload_id   | Primary key. Shared key with MongoDB.          |
| upload_time | Timestamp of upload                            |
| topic_id    | Identifies the topic                           |
| topic_title | Human-readable topic title                     |
| report_type | Not currently used                             |
| post_count  | Number of posts in upload                      |
| pruned      | TINYINT(1) NOT NULL DEFAULT 0 — tombstone flag |
| pruned_at   | DATETIME NULL — timestamp of tombstone         |

**MongoDB — `posts` collection**

| Field     | Notes                                                          |
| --------- | -------------------------------------------------------------- |
| _id       | MongoDB-generated ObjectId                                     |
| post_id   | Sequential number assigned by OPP. Natural chronological key. |
| author    | Post author. Exactly matches author references in html field.  |
| head      | Post heading                                                   |
| link      | Post link                                                      |
| text      | Post text content                                              |
| html      | Post HTML content. Contains "wrote:" divs for quote-replies.  |
| topic_id  | Identifies the topic                                           |
| upload_id | Foreign key — shared key with MariaDB                         |

---

## Operations

Operations are identified by a prefix and a sequential number:

- **M** — Maintenance operations (Raven-Jobs / Raven-Processor)
- **R** — Research operations (Raven-Jakarta-Analyzer / Raven-Processor)

New operations can be added to either list as they are identified.

---

### Maintenance Operations (Raven-Jobs)

#### M1: Remove Duplicates

On OPP, topics grow over time as people add more posts. So an upload today might only be a subset of an upload of the same topic tomorrow. The fact that we can upload a single topic multiple times means that we can end up with a LOT of redundant data. M1 rectifies that by retaining only the latest upload for each topic and pruning all previous versions.

**Design Notes**

- Pruning is driven from the MariaDB side, using `topic_id` to group uploads and `upload_time` to identify the latest
- `upload_id` is the shared key used to cascade deletes to MongoDB
- A tombstone pattern is used to ensure recoverability across both databases

**Two-Phase Pruning Process**

_Phase 1 — Mark (MariaDB transaction)_

```sql
UPDATE uploads
SET pruned = 1, pruned_at = NOW()
WHERE pruned = 0
AND upload_id NOT IN (
    SELECT upload_id FROM uploads u2
    WHERE u2.topic_id = uploads.topic_id
    ORDER BY upload_time DESC
    LIMIT 1
)
```

_Phase 2 — Sweep_

```
For each upload_id WHERE pruned = 1:
    deleteMany from MongoDB WHERE upload_id = ?
    if MongoDB confirms deletion:
        DELETE from MariaDB WHERE upload_id = ?
    else:
        log failure, leave tombstone in place for next run
```

**Recovery**

Phase 2 is idempotent. Any run will pick up tombstoned records left behind by a previous failed run. The tombstone record serves as both a work queue entry and an audit log entry.

See [[Operation M1]] for full implementation detail.

#### M2: Removals

Removes an entire upload based on `upload_id`. See [[Work/Challenges#Operation M2 Removals]] for detail.

---

### Research Operations (Raven-Jakarta-Analyzer)

#### R1: Topics by upload count
Identify how many times each topic has been uploaded.

#### R2: Topics by post count
Rank topics by size — largest topics by number of posts.

#### R3: Upload activity over time
Count uploads per time period to identify activity patterns.

#### R4: Most prolific authors overall
Rank authors by total post count across all topics.

#### R5: Most prolific authors per topic
Rank authors by post count within each topic.

#### R6: Authors appearing across the most topics
Identify authors with the broadest participation across topics.

#### R7: Conversation Linking
Reconstructs threaded conversations from a single upload's posts. Parses quote-reply HTML structure using Jsoup CSS selectors, builds a parent-child reply tree, and traverses it recursively to produce root-to-leaf conversation paths.

See [[Operation R7]] for full implementation detail.

#### R8: Dominant conversationalists vs isolated contributors per topic
Classify authors within a topic by their participation in conversation threads vs standalone posting.

#### R9: Topics by conversational density
Rank topics by ratio of reply posts to standalone posts.

#### R10: Author co-occurrence across topics
Identify authors who frequently appear together across multiple topics.

#### R11: Thread depth analysis per topic
Measure the depth of conversation trees per topic — identifying topics with deep back-and-forth exchanges.


## Script Testing

The scripts can be tested from the project directories before deploying to /opt/raven-jobs. But the RAVEN_JAR_PATH variable needs to be set 
```
ndeans@vortex:scripts ( WP5-WP9 ) $ RAVEN_JAR_PATH=/home/ndeans/Projects/java_projects/Raven-Jobs/target/raven-jobs-1.0-SNAPSHOT.jar ./operation-m3.sh
`

