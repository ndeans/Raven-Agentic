## WP-1 : To Larry - Operation M2
Hi Larry, 
Please read ..Raven/docs/Official/Operation M2 and let me know if you understand it. 

## WP-2 : To Larry - Cleanup and System Testing
Hi Larry, 

Now that we have Operations R7 and M2 implemented, we need to turn our attention to some housekeeping and system testing before we move on to more development.
### 1. Pushes and Pull Requests 
Operation R7 and Operation M2 each have their own feature branch in two separate repositories, Raven-Processor and Raven-Jobs. We need to merge these feature branches to the main/master branches in these repositories. An instance of Larry already started this process but I accidentally clobbered the session, so this step will need to be preceded by a general check on where things stand.

1. From each of the local project directories make sure all valid changes have been pushed to GitHub.
2. For every push, please issue a pull request. You should be able to use gh commands for this as gh is linked to he SSH keys on this machine for accessing GitHub.
### 2. Check other Projects for Unmerged Changes
I believe there are also unmerged changes in Raven-Jakarta-Web and Raven-Jakarta-JAXRS. Since these applications both depend on Raven-Processor, it means we need to rebuild them as well to incorporate the changes. So we need to clean these repositories up too. Following the same process described in step #1, but applied to the Jakarta projects.
### 3. Approvals and Merges
After I approve the pull requests, I will either merge the branches or ask you to do it.
### 4. Component Rebuilds
After all the changes have been merged to the main branches of each project we will be ready to rebuild the components, IN THIS ORDER...

1. Raven-Processor -> JAR
2. Raven-Jobs -> JAR
3. Raven-Jakarta-JAXRX -> WAR
4. Raven-Jakarta-Web -> WAR
### 5. Deployment
After building the components we need to deploy them. 

Raven-Processor -> should be installed into the local .m2 directory so it can be imported into the other projects.

Jakarta projects -> Both Jakarta projects generate WAR files which need to be deployed to the Wildfly server.

Raven-Jobs -> This one may take a little more engineering. The JAR file needs to installed to a local directory that local bash scripts can reach. Then we need the local bash scripts themselves.  

1. operation-M1.sh (no inputs) - We should be able to run this from the command line OR schedule it in the crontab.
2. operation-M2.sh (accepts a list of upload ids) - runs from the command line. 
3. operation-R7-TEST.sh (accepts one upload id) - runs from the command line.

### 6. System Testing
After all these components are in place, the war files, the jar files and the bash scripts. We should be ready to test.
#### Regression Test #1: Raven-Jakarta-JAXRS:
There is no change to Raven-Jakarta-JAXRS but it uses Raven-Processor so we need to run a regression test after we deploy it, which will be a simple matter of me using the OPP user script to scrape data from OPP and send it to Raven. The verifying the upload.
#### Regression Test #2: Raven-Jakarta-Web:
There is no change to Raven-Jakarta-Web either, but this component also uses the Raven-Processor library so we need to run a regression test here too, which will be a simple matter of being up the application in a browser and making sure it lists the uploads.
#### Regression Test #3: Operation M1:
Run the Operation-M1.sh script.
#### Test #4: Operation M2:
Run the Operation-M2 script - I will supply a list of upload ids.
#### Test #5: Operation R7:
Run the Operation-R7 script - I will provide a upload id.

## WP-3 : To Larry - Refactor M1.
Hi Larry, 
I noticed that the Operation M1 is basically handled by Java class called PruneJob. I'm expecting there to eventually be a large number of these jobs so I don't want go down the path of arbitrary names. I would like to refactor the code and rename PruneJobs to OperationM1.

## WP-4 : To Pierre - Operation R7 to UI Gap.
Hi Pierre,
Please read Notes#R7 to UI Gap
I'd like to get your thoughts.

## WP-5 : To Larry - Raven-Jobs Deployment Changes
Hi Larry,

Pierre and I have been reviewing the logging situation and the scattered state of the bash launcher scripts. Currently `run_m1.sh` exists in two places (the old `Raven/bin` and `Raven-Agentic/bin`), neither matches the ndjson format that the Java side is already producing, and the separate Raven-DevOps idea adds unnecessary cross-repo coordination for what is fundamentally one tightly-coupled unit.

We'd like you to fold the operational scripts and deployment packaging into the Raven-Jobs project itself. Here's the scope:

### 1. Move Scripts into Raven-Jobs
Create `src/main/scripts/` in Raven-Jobs and place the launcher scripts there:
- `operation-m1.sh`
- `operation-m2.sh`
- Any other operation scripts that currently exist (e.g., R7 test runner)

These replace any copies currently living in `Raven/bin` or `Raven-Agentic/bin`.

### 2. Fix the Bash Logging
The Java side is already emitting proper ndjson via logstash-logback-encoder with MDC fields (`app`, `operation`, `environment`, `run_id`). The bash scripts need to match. Specifically:

1. **Bash generates the `run_id`** (timestamp-based, e.g., `$(date -u +%Y%m%dT%H%M%SZ)`) and passes it to Java via `-Drun.id=`.
2. **Java reads `-Drun.id`** from system properties and puts it into MDC (instead of generating its own). Confirm this is a small change — check how `run_id` is currently being set in `App.java`.
3. **Bash bookend lines** ("Starting...", "Complete...") are emitted as JSON matching the ndjson format, including the shared `run_id`. No more plain-text `echo` lines in the log file.

The result: every line in a log file is valid JSON with a common `run_id` for correlation.

### 3. Resolve the logback.xml Discrepancy
The `logback.xml` checked into the source tree (`src/main/resources/logback.xml`) still shows a plain-text pattern encoder, but the running application is clearly using logstash-logback-encoder with JSON output. Please reconcile — make sure the committed config matches what's actually producing the ndjson output. Also verify that `logstash-logback-encoder` is declared as a dependency in `pom.xml` (it isn't currently visible there either).

### 4. Package as a Distributable Archive
Use Maven's assembly plugin (or shade plugin — your call) to produce a single distributable artifact (zip or tar.gz) containing:
- The fat JAR
- The launcher scripts
- A `logs/` directory placeholder

The scripts should reference the JAR via relative path so the whole thing is self-contained. Unpack to the target directory, point crontab at the scripts, done.

### 5. Retire the Old Locations
Once this is working, the following should be cleaned up:
- `Raven-Agentic/bin/run_m1.sh` — remove
- `Raven/bin/run_m1.sh` — remove (and anything else in `Raven/bin`)
- The Raven-DevOps project on Vortex can be archived/abandoned

Let me know if you have questions. The goal is a clean, self-contained deployment for the release baseline.

#### Comment:
We still need Raven-Agentic for the agentic communications. ;)

## WP-6 : To Larry - R7 Conversation View in Raven-Jakarta-Web

Hi Larry,

Pierre and I have worked through the design for bringing R7's conversation output into the Web UI. We're adding this to Raven-Jakarta-Web rather than starting the Analyzer project — Web already has the upload listing and JSF plumbing in place, and we want to keep things consolidated for the release baseline.

The goal is a three-step drill-down that mirrors the existing navigation pattern:

```
view-uploads.xhtml              → pick an upload
    ↓ "Conversations" link
view-conversations.xhtml?jid=   → see the conversation index (summary list)
    ↓ click a conversation
view-conversation.xhtml?jid=&c= → see the full rendered post sequence for that conversation
```
### Overview of Existing Structure (for reference)

The current flow in Raven-Jakarta-Web is:

```
console.xhtml → (redirect) → view-uploads.xhtml → view-upload.xhtml?jid=<upload_id>
```

Backed by:
- `ConsoleBean.java` — @RequestScoped, loads upload metadata from Maria_DAO
- `UploadBean.java` — @ViewScoped, loads posts for a given upload_id via OppCurator

R7 classes in Raven-Processor (`us.deans.raven.processor`):
- `R7Service.java` — orchestrator, `execute(String uploadId)` returns `List<R7Conversation>`
- `R7Conversation.java` — contains `List<R7Post>` (one root-to-leaf path)
- `R7Post.java` — contains `post_id`, `author`, `head`, `html`, `link`, `width`, `uplink_post_id`

### 1. New Backing Bean — ConversationBean.java

**Package:** `us.deans.raven`
**Scope:** `@SessionScoped`

Session scope is deliberate here. R7 execution is the expensive step (MongoDB query + HTML parsing + tree traversal). We want to run it once when the user selects an upload, then hold the results in session so the user can drill into individual conversations and navigate back to the index without re-executing R7 each time.

| Field               | Type                   | Notes                                          |
| ------------------- | ---------------------- | ---------------------------------------------- |
| `upload_id`         | String                 | Received via view param `jid`                  |
| `conversations`     | List\<R7Conversation\> | Populated by `loadConversations()`             |
| `selectedIndex`     | int                    | Index of the currently selected conversation   |

**`loadConversations()`** — called via `<f:viewAction>` on `view-conversations.xhtml`. Instantiates `R7Service` and calls `execute(upload_id)`. Stores the result in `conversations`. If `upload_id` matches the already-loaded value, skip re-execution (guard against redundant calls on back-navigation). Log entry/exit with SLF4J, same pattern as UploadBean.

**`getSelectedConversation()`** — convenience getter, returns `conversations.get(selectedIndex)`. Used by `view-conversation.xhtml` to render a single conversation.

### 2. View 1 — view-conversations.xhtml (Conversation Index)

**Purpose:** Lightweight summary listing. Shows the conversation index — one row per conversation with post count, root author, and root post heading. No heavy HTML rendering on this page.

**Entry point:** Receives `upload_id` via view param `jid`.

```xhtml
<f:metadata>
    <f:viewParam name="jid" value="#{conversationBean.upload_id}"/>
    <f:viewAction action="#{conversationBean.loadConversations}"/>
</f:metadata>
```

**Layout:**

```xhtml
<h1>Raven Console (Jakarta/JSF4) : Conversations</h1>
<h2>Upload: #{conversationBean.upload_id}</h2>
<h3>#{conversationBean.conversations.size()} conversations found</h3>

<h:dataTable value="#{conversationBean.conversations}" var="conv"
             varStatus="status" styleClass="conversation-index">
    <h:column>
        <f:facet name="header">#</f:facet>
        <h:link value="#{status.index + 1}"
                outcome="view-conversation.xhtml">
            <f:param name="jid" value="#{conversationBean.upload_id}"/>
            <f:param name="c" value="#{status.index}"/>
        </h:link>
    </h:column>
    <h:column>
        <f:facet name="header">Posts</f:facet>
        #{conv.posts.size()}
    </h:column>
    <h:column>
        <f:facet name="header">Root Author</f:facet>
        #{conv.posts[0].author}
    </h:column>
    <h:column>
        <f:facet name="header">Root Post</f:facet>
        [#{conv.posts[0].post_id}] #{conv.posts[0].head}
    </h:column>
</h:dataTable>
```

This mirrors what the R7 test runner currently shows in the console (the screenshot output), but as a clickable table in the browser.

### 3. View 2 — view-conversation.xhtml (Single Conversation Detail)

**Purpose:** Full rendered view of one conversation's posts in sequence. This is where the heavy HTML rendering happens — but only for the posts in one thread.

**Entry point:** Receives `upload_id` via `jid` and conversation index via `c`.

```xhtml
<f:metadata>
    <f:viewParam name="jid" value="#{conversationBean.upload_id}"/>
    <f:viewParam name="c" value="#{conversationBean.selectedIndex}"/>
</f:metadata>
```

**Layout:**

```xhtml
<h1>Raven Console (Jakarta/JSF4) : Conversation Detail</h1>
<h2>Upload: #{conversationBean.upload_id}
    — Conversation #{conversationBean.selectedIndex + 1}
    (#{conversationBean.selectedConversation.posts.size()} posts)</h2>

<h:link value="← Back to conversation list" outcome="view-conversations.xhtml">
    <f:param name="jid" value="#{conversationBean.upload_id}"/>
</h:link>

<h:panelGroup layout="block" styleClass="conversation-panel">

    <ui:repeat value="#{conversationBean.selectedConversation.posts}" var="post">
        <h:panelGroup layout="block" styleClass="post-card">

            <h:panelGroup layout="block" styleClass="post-header">
                <h:outputText value="#{post.author}" styleClass="post-author"/>
                <h:outputText value=" [#{post.post_id}]" styleClass="post-id"/>
                <h:outputLink value="#{post.link}" target="_blank"
                              styleClass="post-link">OPP ↗</h:outputLink>
            </h:panelGroup>

            <h:panelGroup layout="block" styleClass="post-content">
                <h:outputText value="#{post.html}" escape="false"/>
            </h:panelGroup>

        </h:panelGroup>
    </ui:repeat>

</h:panelGroup>
```

**Important:** `escape="false"` on the `h:outputText` renders the raw HTML from MongoDB. This is necessary to display the original post content including OPP's quote-reply div structure (`div.smalltext`, `div.quote_colors`).

**Namespace declarations** — Both new views need `xmlns:ui="http://xmlns.jcp.org/jsf/facelets"` in addition to the existing `h:` and `f:` namespaces already used in the other views.

### 4. Link from Upload Listing

In `view-uploads.xhtml`, add a "Conversations" link alongside the existing upload_id link. The existing upload_id link goes to `view-upload.xhtml?jid=`. Add a second column that links to:

```
view-conversations.xhtml?jid=#{upload.upload_id}
```

This gives the user two paths from the listing: "view posts" (existing) and "view conversations" (new).

### 5. CSS Additions to style1.css

Add the following styles to support the conversation views. These should complement, not replace, the existing table styles.

```css
/* Conversation index table */
.conversation-index {
    width: 100%;
}

/* Conversation detail view */
.conversation-panel {
    max-height: 600px;
    overflow-y: auto;
    border: 1px solid #444;
    padding: 1em;
    margin-top: 1em;
    background-color: #1a1a1a;
}

.post-card {
    border: 1px solid #333;
    margin-bottom: 1em;
    padding: 0.75em;
    background-color: #222;
}

.post-header {
    font-family: "Noto Sans", sans-serif;
    font-size: 14px;
    padding-bottom: 0.5em;
    border-bottom: 1px solid #333;
    margin-bottom: 0.5em;
}

.post-author {
    font-weight: bold;
    color: #e0c060;
}

.post-id {
    color: #888;
    margin-left: 0.5em;
}

.post-link {
    float: right;
    color: #6699cc;
    text-decoration: none;
}

.post-content {
    font-family: "Noto Sans", sans-serif;
    font-size: 14px;
    line-height: 1.5;
}

/* Style OPP's existing quote-reply markup */
.post-content div.smalltext {
    color: #aaa;
    font-size: 13px;
    margin-bottom: 4px;
}

.post-content div.quote_colors {
    border-left: 3px solid #555;
    padding-left: 0.75em;
    margin-bottom: 0.5em;
    color: #999;
    font-style: italic;
}
```

Note: I've based the colour palette on the dark theme already in use (visible in the R7 test output screenshot). Adjust to match whatever your current Web deployment looks like.

### 6. No Changes to Raven-Processor

`R7Service.execute()` already returns `List<R7Conversation>` containing full `R7Post` objects with `html` and `link` fields populated. No Processor changes needed — the data is already there.

### Summary of Changes

| File                        | Action                           | Project           |
| --------------------------- | -------------------------------- | ----------------- |
| `ConversationBean.java`     | NEW                              | Raven-Jakarta-Web |
| `view-conversations.xhtml`  | NEW — conversation index         | Raven-Jakarta-Web |
| `view-conversation.xhtml`   | NEW — single conversation detail | Raven-Jakarta-Web |
| `view-uploads.xhtml`        | MODIFY — add conversations link  | Raven-Jakarta-Web |
| `style1.css`                | MODIFY — add conversation styles | Raven-Jakarta-Web |

### Navigation Map (updated)

```
console.xhtml → (redirect) → view-uploads.xhtml
                                  ├── view-upload.xhtml?jid=         (existing: post listing)
                                  └── view-conversations.xhtml?jid=  (new: conversation index)
                                          └── view-conversation.xhtml?jid=&c=  (new: conversation detail)
```

Let me know if you have questions.

## WP-7 : To Larry - Jakarta Logging & Wildfly JSON Output

Hi Larry,

Pierre has assessed the logging situation across all Raven components. The goal is to get the Jakarta WAR logs into JSON format so they can be monitored alongside the Raven-Jobs ndjson logs. Here's what needs to be done — it's a small scope, two tasks.

---

### 1. Configure Wildfly JSON Logging (`standalone.xml`)

Wildfly's built-in JSON formatter is the right mechanism for WAR-deployed applications. Adding a JSON file handler to the logging subsystem in `standalone.xml` will capture all output from both `Raven-JAXRS` and `Raven-Web` in a single JSON log file — no WAR changes, no classloader risk.

In `standalone.xml`, locate the `<subsystem xmlns="urn:jboss:domain:logging:...">` section and make three additions:

**Add a JSON formatter** (alongside the existing `PATTERN` formatter):
```xml
<json-formatter name="JSON"/>
```

**Add a JSON file handler** (alongside the existing `FILE` handler):
```xml
<file-handler name="JSON_FILE" autoflush="true">
    <formatter>
        <named-formatter name="JSON"/>
    </formatter>
    <file relative-to="jboss.server.log.dir" path="raven.json.log"/>
</file-handler>
```

**Add the handler to the root logger**:
```xml
<root-logger>
    <level name="INFO"/>
    <handlers>
        <handler name="CONSOLE"/>
        <handler name="FILE"/>
        <handler name="JSON_FILE"/>  <!-- add this -->
    </handlers>
</root-logger>
```

Result: `/opt/wildfly/standalone/log/raven.json.log` — a live JSON log covering both WARs, taileable with lnav.

> [!NOTE]
> A Wildfly restart is required for this change to take effect. Use the standard stop/start procedure from the Run Book.

---

### 2. Declare `slf4j-api` in Raven-Jakarta-Web (`pom.xml`)

`Raven-Jakarta-Web` uses SLF4J (`Logger`, `LoggerFactory`) in `ConsoleBean.java` and `UploadBean.java` but does not declare `slf4j-api` as a dependency in `pom.xml`. It currently compiles only because the dependency arrives transitively through `Raven-Processor`. This is fragile.

Add the explicit provided-scope declaration to `pom.xml`:

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.13</version>
    <scope>provided</scope>
</dependency>
```

Match the version already used in `Raven-Processor` and `Raven-Jakarta-JAXRS` (`2.0.13`). Rebuild and redeploy `Raven-Web.war` after making this change.

---

### What Does NOT Need Changing

- **Raven-Processor** — `slf4j-api` as `provided` is correct library design. No changes.
- **Raven-Jobs** — Already producing ndjson via logstash-logback-encoder. No changes.
- **Raven-Jakarta-JAXRS** — Already declares `slf4j-api` as `provided` correctly. No changes.

Do not bundle Logback or logstash-logback-encoder inside the WARs. Wildfly's logging subsystem is the right implementation provider for deployed applications — the JSON formatter configured above is sufficient.

---

### Field Naming Note (FYI)

Wildfly's JSON formatter uses slightly different field names than logstash-logback-encoder (e.g., `timestamp` vs `@timestamp`). This is an acceptable inconsistency for now — lnav handles both without issue. If field normalization becomes a priority later (e.g., for Grafana Loki ingestion), it can be addressed at the pipeline layer without touching the application code.

Let me know if you have questions.

## WP-8 : To Pete - Assess OPP-Extractor.iser.js 

This is a script that is currently working and it doesn't have any issues at the moment, but I'm looking for two things... I would like a full report on how it works. (I'm the original author so I already kinda know but I'm looking for an outside perspective.) Added to that assessment I would like you include a list of any flaws or bugs that you might see that I missed and any recommendations for improvement in terms of performance, maintainability and also portability. 

That third item deserves a little more explanation so here it is... By portability I mean, how difficult/easy is it to create a new version of the script that targets a different BBS system?

Please ad your report to the Work/Review folder.

## WP-9 : To Larry - Add KDEConnect notifications to Raven-Jobs scripts.

We need to add some action to the bash-scripts that are used to call Raven-Jobs. When the run script is writing it's ending bookend log entry - after Raven-Jobs returns a 0, I'd like to send a notification to my phone using KDEConnect but ONLY if the connection is active. 

So, something like this...

check if connection is active...
```
$ kdeconnect-cli -list-available
```
That should provide the id for the connected device. If no such device exists, then exit. If a device DOES show up and we can extract the id to use in the following command, great otherwise, we can use a config item look it up somewhere, or if all else fails we can hard-code it.  

For now we can hard it just to see if it works...
	device id = 2adf1fdef97446eab62372b7b0fabd19

The commend to send the notification should be...
```
$ kdeconnect-cli -d [device id] --ping-msg "[message]"
```
The message would be the, timestamp, app, operation, run_id, event and status elements that went into the log.

 (WP ## WP-10 : Operation R7:  Verification

I am not seeing the results I was expecting for Operation R7...

Please read Work/Challenges 
[[Challenges#Weird Readings from Operation R7]]

And let me know what you think.