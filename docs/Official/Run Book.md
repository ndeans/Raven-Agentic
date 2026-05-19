# Raven System — Run Book

Operational reference for the Raven system. Intended for dev-ops and maintenance personnel.

**Host**: `192.168.12.132`
**Owner**: `ndeans`
**Java**: OpenJDK 17 (Temurin 17.0.12)

---

## 1. Wildfly Application Server

Wildfly is **not** managed by systemd. It is started and stopped manually.

**Installation**: `/opt/wildfly/`
**Bind address**: `192.168.12.132:8080`

> [!NOTE]
> The public interface defaults to `127.0.0.1` in `standalone.xml`. Use `-b` to bind to the network interface at start time.

### Start
```bash
/opt/wildfly/bin/standalone.sh -b 192.168.12.132 &

alternate: include the Admin Console

/opt/wildfly/bin/standalone.sh -b 192.168.12.132 -bmanagement 192.168.12.132 &

```
To confirm it's up:
```bash
curl -I http://192.168.12.132:8080/Raven/
# Expected: HTTP/1.1 200 OK
```

### Stop
```bash
/opt/wildfly/bin/jboss-cli.sh --connect --controller=192.168.12.132:9990 command=:shutdown

alternate ?:

/opt/wildfly/bin/jboss-cli.sh --connect command=:shutdown
```
 
### Check deployed modules
```bash
ls /opt/wildfly/standalone/deployments/
```
Expected modules: `Raven-JAXRS.war`, `Raven-Web.war`, `Raven-Analyzer.war`

---

## 2. MariaDB

Managed by **systemd**. Version: MariaDB 10.5

### Start / Stop / Status
```bash
sudo systemctl start  mariadb
sudo systemctl stop   mariadb
sudo systemctl status mariadb
```
### Logs
```
/var/lib/mysql/
```
### Raven Database
- **Key table**: `uploads` — contains `upload_id`, `topic_id`, `upload_time`, `pruned`, `pruned_at`

### Useful queries
```sql
-- Check total uploads
SELECT COUNT(*) FROM uploads;

-- Check for pending tombstones (M1 incomplete runs)
SELECT * FROM uploads WHERE pruned = 1;

-- View uploads by topic
SELECT topic_id, COUNT(*) AS uploads FROM uploads GROUP BY topic_id ORDER BY topic_id;
```

---

## 3. MongoDB

Managed by **systemd**. Version: 7.0.12

### Start / Stop / Status
```bash
sudo systemctl start  mongod
sudo systemctl stop   mongod
sudo systemctl status mongod
```

### Raven Collection
- **Key collection**: `posts` — contains `upload_id`, `post_id`, `author`, `topic_id`, `html`

### Useful queries
```javascript
// Total post count
db.posts.countDocuments()

// Posts by upload_id
db.posts.find({ upload_id: 338 }).count()
```

---

## 4. Scheduled Maintenance (Cron)

### Cron Schedule

Both scheduled operations run at **6:00 AM** daily:

```
0 6 * * * /opt/raven-jobs/bin/operation-m1.sh
0 6 * * * /opt/raven-jobs/bin/operation-m3.sh
```

Manage the schedule:
```bash
crontab -l    # view
crontab -e    # edit
```

-### M1 — Remove Duplicates

Runs automatically every day at **6:00 AM**.

To trigger manually:
```bash
/opt/raven-jobs/bin/operation-m1.sh
```

Logs are written to:
```
/opt/raven-jobs/logs/operation-m1_runner.ndjson
/opt/raven-jobs/logs/operation-m1_<RUN_ID>.ndjson
```

---

### M3 — Daily Verification

Runs automatically every day at **6:00 AM**. Compares `SUM(post_count)` from MariaDB against `countDocuments()` from MongoDB. On mismatch: creates a GitHub issue and sends email to `ncdeans@gmail.com`.

> [!IMPORTANT]
> Requires `gh auth status` to be valid on Vortex, and `sendmail` (esmtp) to be configured with a working relay. See [[Official/Operation M3]] — User Review Required.

To trigger manually:
```bash
/opt/raven-jobs/bin/operation-m3.sh
```

Logs are written to:
```
/opt/raven-jobs/logs/operation-m3_runner.ndjson
/opt/raven-jobs/logs/operation-m3_<RUN_ID>.ndjson
```

---

## 5. Filesystem Reference

| Path | Description |
|---|---|
| `/opt/wildfly/` | Wildfly installation root |
| `/opt/wildfly/bin/standalone.sh` | Wildfly start script |
| `/opt/wildfly/bin/jboss-cli.sh` | Wildfly CLI (stop/admin) |
| `/opt/wildfly/standalone/deployments/` | Deployed WAR files |
| `/opt/wildfly/standalone/log/server.log` | Wildfly server log |
| `/home/ndeans/Projects/Raven/docs/` | Project documentation |
| `/opt/raven-jobs/` | Deployed operation runtime (unpacked from dist ZIP) |
| `/opt/raven-jobs/bin/operation-m1.sh` | M1 launcher (Remove Duplicates) |
| `/opt/raven-jobs/bin/operation-m2.sh` | M2 launcher (Selective Removal) |
| `/opt/raven-jobs/bin/operation-m3.sh` | M3 launcher (Daily Verification) |
| `/opt/raven-jobs/bin/operation-r7-test.sh` | R7 test runner |
| `/opt/raven-jobs/lib/raven-jobs-1.0-SNAPSHOT.jar` | Fat JAR (all ops bundled, ~11MB) |
| `/opt/raven-jobs/logs/` | ndjson runner and application logs |
| `/home/ndeans/Projects/java_projects/Raven-Processor/` | Shared library source |
| `/home/ndeans/Projects/java_projects/Raven-Jobs/` | Raven-Jobs source (M1, M2, M3, R7) |
| `/home/ndeans/Projects/java_projects/Raven-Jobs/src/main/scripts/` | Launcher script sources |

---
## 6. Logs and Monitors

Raven currently writes to several logging systems depending on the component. 
#### Initial State:
Raven-Jakarta-JAXRS was initiated from a copy of rvn-Jakarta which was itself initiated on 9/15/2024, from the Eclipse Foundation Starter for Jakarta EE and contains a dependency on SL4J. 

Raven-Jakarta-Web was also initiated from the Eclipse Foundation Starter for Jakarta EE (Template: Web Application) on 10/3/2024 and doesn't include any direct dependencies for logging in the project file but it does include a dependency on jakartaee-web-api: 9.1.0 (So maybe the logging dependency is in there)

Raven-Processor was initiated from a Maven Archetype (probably quickstart) and includes SLF4J

Raven-Jobs was initiated from a new starter-java project that I created on GitHub and includes SLF4J + Logback. 

Raven-Jobs now also includes the script files to call the jobs and they also log to files.

| component                      | logging system  |
| ------------------------------ | --------------- |
| Raven-Jakarta-JAXRS            | SLF4J           |
| Raven-Jakarta-Web              |                 |
| Raven_Processor                | SLF4J           |
| starter-java                   | SLF4J + Logback |
| Raven-Jobs (from starter-java) | SLF4J + Logback |

#### NDJSON Initiative
All logging should ultimately use the NDJSON format going forward because we want the logs to be consumable by monitoring systems like Splunk.

#### LNAV
Currently using LNAV to view the NDJSON compliant logs.
```
lnav /opt/raven-jobs /logs/    # tail all log files, live
```


# Development Environment — Run Book 

## 6. Rebuild & Redeploy (after code changes)

### Raven-Processor (shared library — rebuild first)
```bash
cd /home/ndeans/Projects/java_projects/Raven-Processor
mvn clean install
```

### Raven-Jobs (all operations — M1, M2, M3, R7)
```bash
cd /home/ndeans/Projects/java_projects/Raven-Jobs
mvn clean verify
# Produces: target/raven-jobs-1.0-SNAPSHOT-dist.zip
```

To deploy the updated ZIP to Vortex:
```bash
sudo rm -rf /opt/raven-jobs/lib /opt/raven-jobs/bin
sudo unzip target/raven-jobs-1.0-SNAPSHOT-dist.zip -d /tmp/raven-jobs-update
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/bin /opt/raven-jobs/
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/lib /opt/raven-jobs/
# Note: logs/ is preserved — do not overwrite
```


### WAR deployments (web modules)
See **Deployment Procedures** section below. Use the CLI managed deployment approach — do not copy WARs directly to the deployments directory.


## Initiating Agents

~/bin/start-xxxxx.sh

## Agentic Repository

The Agentic Repository is basically git repository, called "Raven-Agentic" that contains a collection of markdown files.  This repository is cloned to a shared drive on the Tornado system running Windows 10 and shared on the local network. Agents on Typhoon and Vortex can access this shared drive to read and write to/from the files without having to check in and check out every time there is a change.

From Vortex (Fedora 36): 
Agents running on the CLI can access the Agentic Repository by navigating to 
```
/home/ndeans/Projects/agentic_projects/Raven-Agentic
```
This path relies on a FRosted FlakTyphoon


## Back-end Queries

db.posts.find({},{topic_id: 1, upload_id: 1, author: 1, text: 1 })

db.posts.find({upload_id: "334"},{topic_id: 1, upload_id: 1, author: 1, text: 1 })

db.posts.find({author: "emurphy831", },{topic_id: 1, upload_id: 1, author: 1, text: 1 })
db.posts.find({author: "AuntiE", },{topic_id: 1, upload_id: 1, author: 1, text: 1 })

db.posts.find({upload_id: "337"})
db.posts.find({upload_id: "337"},{author: 1, html: 1})

db.posts.find({
    author: "AuntiE",
},{
    topic_id: 1, 
    upload_id: 1, 
    author: 1, 
    text: 1 
})


db.posts.find({
  author: "emurphy831",
  text: { $regex: /climate/, $options: "i" }
}, {
  topic_id: 1, 
  upload_id: 1, 
  author: 1, 
  text: 1
})


collection.find(new Document("upload_id", upload_id)).iterator()

db.posts.find(new Document("upload_id", upload_id)).iterator()

db.posts.find({upload_id: "7"})

db.posts.find({text: /stupid/})


db.posts.aggregate([
   {
        $match: {
            text: { $regex: /stupid/, $options: "i" }
        }    
   },
   {
        $group: {
            _id: "$author",
            count: {$sum: 1}
        }       
   },
   {
        $project: {
            _id: 0,
            name: "$_id",
            count: 1
        }
   },
   {
       $sort: {count: -1}
   }
])


-- 04/19/2025 : RVN-011
db.posts.distinct("upload_id")


// Assessment

// get a count of all documents
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments()

// count documents with no html
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments({
    $or: [
      { html: null },
      { html: "" },
      { html: { $exists: false } }
    ]
  })

  // Or more concisely:
  db.getSiblingDB("Raven-1").getCollection("posts").countDocuments({
    html: { $exists: true, $ne: null, $ne: "" }
  })


  // Oh. wait - that's right... No, here's what I need... I need a list of upload_ids extracted from a collection of all the documents
  //  without html. That list will be the inpout to Raven's Operation M2, which will delete all the records in bothe systems, related to each
  //  upload_id in the list.
  db.getSiblingDB("Raven-1").getCollection("posts").distinct("upload_id", {
    $or: [
      { html: null },
      { html: "" },
      { html: { $exists: false } }
    ]
  })

------------------------------------------------------------------------------------------------------------------------------------------------


db.posts.find();
db.posts.find({upload_id: "7"})
db.posts.find({},{topic_id: 1, upload_id: 1, author: 1, text: 1 })

db.posts.distinct("upload_id",{})
db.posts.distinct("upload_id",{$or:[{html: null},{html: ""}]})

db.posts.countDocuments({})


db.posts.find({
    $or: [
      { html: null },
      { html: "" },
      { html: { $exists: false } }
    ]
  })
  
db.posts.countDocuments({
  html: { $exists: false }
})

//
db.posts.deleteMany({});

-- These two give me the same results. -----------------------
db.posts.find({
    author: "JFlorio",
},{
    topic_id: 1, 
    upload_id: 1, 
    author: 1, 
    text: 1 
})

db.posts.find({author: "JFlorio"})

---------------------------------------------------------------
db.posts.aggregate([
   {
        $match: {
            text: { $regex: /stupid/, $options: "i" }
        }    
   },
   {
        $group: {
            _id: "$author",
            count: {$sum: 1}
        }       
   },
   {
        $project: {
            _id: 0,
            name: "$_id",
            count: 1
        }
   },
   {
       $sort: {count: -1}
   }
])


### Be Careful With These!!!
```
// on Maria...
delete from uploads;
```
```
// on Mongo... 
db.posts.deleteMany({});

```
   

# Deployment Procedures — Run Book 

## WAR Deployments (Jakarta Web Modules)

Raven uses **managed deployments** via the Wildfly CLI. This registers the WAR in `standalone.xml` and stores its content under `standalone/data/content/`. 

> [!IMPORTANT]
> Do **not** copy WARs directly to `/opt/wildfly/standalone/deployments/`. That is the auto-scanner path and is not how Raven WARs are managed. Deployments dropped there will conflict with managed ones and may leave `.failed` markers on restart.

> [!NOTE]
> The management interface must be bound at start time (`-bmanagement 192.168.12.132`) for CLI connections from this host to work.

### Deploy or redeploy a WAR
```bash
/opt/wildfly/bin/jboss-cli.sh --controller=192.168.12.132:9990 --connect \
  --command="deploy --force /path/to/YourApp.war"
```
`--force` performs a redeployment if the name already exists. Omit it for a first-time deployment.

### Verify deployment status
```bash
/opt/wildfly/bin/jboss-cli.sh --controller=192.168.12.132:9990 --connect \
  --command="deployment-info"
```
Expected output shows `ENABLED=true` and `STATUS=OK` for each active WAR.

### Current managed deployments

| WAR | Status | Notes |
|---|---|---|
| `Raven-JAXRS.war` | enabled | Inbound data web service |
| `Raven-Jakarta-Web.war` | enabled | Upload listing and conversation UI |
| `Raven-JSF-4EW.war` | enabled | Legacy web app (predecessor to Raven-Jakarta-Web) |
| `Raven-Jakarta.war` | disabled | Old — superseded |
| `Raven.war` | disabled | Old — superseded |

---

## Raven-Jobs Installation (Fat JAR)

Raven-Jobs is deployed differently from the Jakarta web modules. It is a **self-contained fat JAR** — all dependencies are bundled into a single file. It runs standalone via the `java` command with no application server involved. The launcher scripts and JAR are packaged together into a distributable zip by Maven.

### How it differs from WAR deployment

| | Raven-Jobs | Jakarta WARs |
|---|---|---|
| Runtime | Standalone JVM process | Wildfly app server |
| Packaging | Fat JAR + scripts in a zip | WAR file |
| Deployment tool | `unzip` to `/opt/raven-jobs/` | Wildfly CLI (`deploy --force`) |
| Hot reload | No — replace files, run next invocation | Yes — Wildfly scanner picks it up |
| Logs | `/opt/raven-jobs/logs/` (ndjson) | `/opt/wildfly/standalone/log/` |

### First-time installation

```bash
# 1. Build the distributable zip from source
cd /home/ndeans/Projects/java_projects/Raven-Jobs
mvn clean verify
# Produces: target/raven-jobs-1.0-SNAPSHOT-dist.zip

# 2. Create the installation directory
sudo mkdir -p /opt/raven-jobs

# 3. Unpack
sudo unzip target/raven-jobs-1.0-SNAPSHOT-dist.zip -d /tmp/raven-jobs-install
sudo cp -r /tmp/raven-jobs-install/raven-jobs-1.0-SNAPSHOT/. /opt/raven-jobs/
sudo rm -rf /tmp/raven-jobs-install

# 4. Set ownership
sudo chown -R ndeans:ndeans /opt/raven-jobs
```

After installation the layout is:
```
/opt/raven-jobs/
  bin/    ← launcher scripts (operation-m1.sh, m2, m3, r7-test)
  lib/    ← raven-jobs-1.0-SNAPSHOT.jar  (~11MB, all ops bundled)
  logs/   ← ndjson runner and application logs (preserved across updates)
```

### Verify installation

```bash
/opt/raven-jobs/bin/operation-m1.sh
# Check exit code ($?) and:
tail -1 /opt/raven-jobs/logs/operation-m1_runner.ndjson
```

### Update procedure (after code changes)

Only `bin/` and `lib/` are replaced — `logs/` is left untouched.

```bash
cd /home/ndeans/Projects/java_projects/Raven-Jobs
mvn clean verify
sudo rm -rf /opt/raven-jobs/lib /opt/raven-jobs/bin
sudo unzip target/raven-jobs-1.0-SNAPSHOT-dist.zip -d /tmp/raven-jobs-update
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/bin /opt/raven-jobs/
sudo cp -r /tmp/raven-jobs-update/raven-jobs-1.0-SNAPSHOT/lib /opt/raven-jobs/
sudo rm -rf /tmp/raven-jobs-update
```

> [!NOTE]
> The cron jobs reference scripts by absolute path (`/opt/raven-jobs/bin/`). No crontab changes are needed after an update.

---

## Wildfly Logging Configuration

Wildfly **rewrites `standalone.xml` on every graceful shutdown** from its in-memory state. Manual edits to `standalone.xml` while the server is running will be lost. All logging configuration changes must be made through the CLI.

### Current logging setup
- `server.log` — plain-text rotating log (all components)
- `raven.json.log` — JSON format log (all components, for lnav/monitoring)

### Add or modify logging config via CLI
```bash
/opt/wildfly/bin/jboss-cli.sh --controller=192.168.12.132:9990 --connect
```
Example — the commands used to set up `raven.json.log`:
```
/subsystem=logging/json-formatter=JSON:add()
/subsystem=logging/file-handler=JSON_FILE:add(named-formatter=JSON, file={relative-to="jboss.server.log.dir", path="raven.json.log"}, autoflush=true, append=true)
/subsystem=logging/root-logger=ROOT:add-handler(name=JSON_FILE)
```
Changes applied via CLI take effect immediately and are persisted to `standalone.xml` automatically.

### Log locations
| File | Format | Description |
|---|---|---|
| `/opt/wildfly/standalone/log/server.log` | plain text | Rotating daily log, all components |
| `/opt/wildfly/standalone/log/raven.json.log` | JSON (one record per line) | JSON log for lnav/monitoring |

