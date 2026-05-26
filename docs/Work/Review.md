## Review / Walk through

### Project Exploration Baseline (2026-03-16)

I have completed the initial exploration of the Raven project suite.



### Accomplishments
1. **Directory Structure Verified**: Confirmed existence of `Raven/docs`, `java_projects/Raven-Processor`, and `java_projects/Raven-Jobs`.
2. **Build Success**: 
    - `Raven-Processor`: Built and installed to local repository (`mvn clean install`).
    - `Raven-Jobs`: Compiled successfully against local processor dependency.
3. **Core Logic Review**: Analyzed `Maria_DAO`, `MongoDao`, and `PruneJob` implementation for the M1 operation. Conformed implementation matches design in `Operation M1.md`.

### M1 Operation Fix (2026-03-16)

I fixed the M1 (Remove Duplicates) operation which was failing due to missing MariaDB columns.

1. **Root Cause**: `SQLSyntaxErrorException: Unknown column 'pruned'` in `uploads` table.
2. **Fix**: Applied schema update adding `pruned` and `pruned_at`.
3. **Verification**: 
   - Re-ran M1 via `Raven-Jobs`. 
   - **Result**: Successfully marked and swept **167** redundant uploads across MariaDB and MongoDB.
   - Logs captured in `java_projects/Raven-Jobs/run_v2.log`.
### Wildfly Deployment Resolution (2026-03-16)

The Wildfly server is now fully operational with the Raven suite deployed.

1.  **Conflict Cleanup**: Cleared redundant WAR artifacts from `standalone/deployments/`.
2.  **Permission Escalation**: Updated `/opt/wildfly` ownership to `ndeans`, eliminating `root` access requirements for standard operations.
3.  **Network Setup**: Successfully bound server to `192.168.12.132:8080`.
4.  **Verification**: 
    - `curl -I http://192.168.12.132:8080/Raven/` -> **200 OK**.
    - All modules (`Raven-JAXRS`, `Raven-Jakarta`, etc.) are deployed.

### E2E Regression Test: Upload Functionality (2026-03-17)

Successfully verified the end-to-end upload pipeline using a simulated payload.

1. **Endpoint Verified**: `http://192.168.12.132:8080/Raven/api/upload`
2. **Payload Execution**: POST request with 2 post records for `topic_id: 9999`.
3. **Backend Persistence**:
   - **MariaDB**: Confirmed `upload_id: 338` created in `uploads` table.
   - **MongoDB**: Confirmed "2 post records inserted" via `server.log` (MongoDao verification).
4. **Conclusion**: Upload functionality is stable and ready for production data.

---

## Operation R7 - Review (2026-05-16)

R7 is complete and deployed end-to-end across Raven-Processor, Raven-Jobs, and Raven-Jakarta-Web.

### What Was Built

**Raven-Processor — core algorithm (R7Service, R7Post, R7Conversation)**

R7Service runs in five phases:
1. Load all posts for an `upload_id` from MongoDB via `MongoDao.getPostsByUploadId()`
2. Parse each post's HTML with Jsoup; match each quoted post to its parent using a 20-word content fingerprint (own content, with nested quote div stripped before fingerprinting). Handles both OPP author-name renderings: plain text (`Author wrote:`) and bold-wrapped (`<strong>Author</strong> wrote:`). The `<strong>` case was a post-implementation bug fix (WP-11): OPP wraps the author in `<strong>` when the quoted author is the account logged in during the scrape. The original `ownText()` call returned only `" wrote:"` in that case, silently dropping the uplink. Fixed in `extractQuotedAuthor()` by checking for a `<strong>` child first before falling back to `ownText()`
3. Find root posts: no uplink, but at least one reply (width > 0)
4. Recursively build conversations — each root-to-leaf path through the reply tree becomes one R7Conversation
5. Resolve a context post for each root: the most recent post by the quoted author with a lower post_id, providing entry-point context for conversations that begin mid-thread

Ambiguous or unresolved matches are logged as warnings rather than hard failures. Obsolete uploads (pre-HTML collection, html field null) are detected and rejected with a clear user message.

**Raven-Jobs — R7TestRunner**

Console test runner for development verification. Accepts an `upload_id`, runs R7Service, and prints conversation summaries to stdout.

**Raven-Jakarta-Web — ConversationBean + JSF pages**

`ConversationBean` is a `@SessionScoped` bean that wires R7Service into the web layer. Conversations are lazy-loaded on first access and reset automatically when `upload_id` changes.

Two JSF pages:
- `view-conversations.xhtml` — index view: lists all conversations for an upload with root author and root post
- `view-conversation.xhtml` — detail view: renders the full thread as a sequence of post cards, with the context post (if resolved) displayed above the root in a distinct style

### Status

Implemented, tested via R7TestRunner against live data, and accessible through the Raven-Web UI. No known issues.

---

## WP-13 : UI Modifications (2026-05-25)

All changes are in `Raven-Jakarta-Web`, branch `WP13_UI_Mods`.

### What Was Built

**Scrollable Tables**

All data tables are wrapped in a `<div class="table-scroll">` container (`overflow-y: auto; max-height: 80vh`). Column headers use `position: sticky; top: 0` so they remain visible while scrolling. Applied to `view-uploads.xhtml`, `view-upload.xhtml`, and `view-conversations.xhtml`.

**Hamburger Menu**

A fixed-position menu appears in the top-right corner of every view page. It contains a link to `about.xhtml` and the dark mode toggle. Implemented as a plain HTML `<div class="menu-container">` with a `<button class="hamburger">` triggering a CSS `hidden` toggle via `toggleMenu()`. The menu must be placed inside `<f:view>` on pages that use that wrapper — content placed outside `<f:view>` is silently dropped by Facelets.

**Dark Mode**

Color mode is stored in `localStorage` under the key `ravenColorMode`. On each page load, an inline script at the top of `<h:body>` reads the key and adds the `dark` class to `<body>` before any content renders, preventing a flash of unstyled content. The toggle in the hamburger menu calls `toggleColorMode()` which flips the class and persists the new value.

CSS dark mode rules cover:
- `body.dark` — page background `#2f3533`
- `body.dark h1, h2, h3` — `cornsilk`
- `body.dark td` — text `#ffdf5d`, background `#2f3533`
- `body.dark a` — `#99ccff` (pale blue, replacing the default dark blue which is unreadable on dark backgrounds)
- `th` — static across both modes: `color: #ffdf5d; background-color: grey`

**CSS Consolidation**

The stylesheet had accumulated duplicate selectors (`h1`, `td`, `th`, `.btn-upload`, `.btn-dialogs` each defined two or three times) from incremental additions. All rules were collapsed into single canonical blocks, which also cleared IntelliJ's pre-commit inspection errors.

**Deployment — `Raven-Web` Context Root**

Deployments now use jboss-cli `deploy --force --name=Raven-Web.war` with an absolute path. The `--name` flag sets the runtime name in Wildfly, making the context root `/Raven-Web` regardless of which project built the WAR. This implements the intended swap architecture — `Raven-Jakarta-Web` and a future `Raven-Spring-Web` can be deployed interchangeably under the same canonical name. The old file-copy deployment under `/Raven-Jakarta-Web` has been removed. Procedure is documented in `_runbook.md` section 6.

### Screenshots

_(to be added)_

### Status

Complete and deployed at `http://192.168.12.132:8080/Raven-Web/`. No known issues.

---

## WP-12 : Road Map Use Case Analysis (2026-05-16)

Mapping of five candidate R-series features to four potential implementation technologies.

### Recommendations

| Feature | Technology |
|---|---|
| Nonsense Filter/Capture | Java (in JAR) |
| Subject Classification | Python |
| Tone Detection | Local LLM (Ollama) |
| Assertion Targets | Prolog (Python preprocessing → Prolog rules) |
| Fallacy Detection | Local LLM (Ollama) |

### Rationale

**Nonsense Filter/Capture → Java**

The description gives the primary heuristic directly: low word count, no discussion value. This maps cleanly to simple logic against the `text` field — word count threshold, token entropy, stopword ratio. No ML needed, no external process. Belongs in the JAR.

**Subject Classification → Python**

A classic NLP classification task. Python's ecosystem (spaCy, scikit-learn, or a small fine-tuned transformer like DistilBERT) gives a fast, reproducible batch classifier that can be tuned against actual OPP topic categories. A 3B LLM could do zero-shot classification but would be slower and less consistent for batch runs. Python wins on throughput and reproducibility.

**Tone Detection → Local LLM (Ollama)**

Tone on a political discussion site — sarcasm, aggression, irony — is exactly where rule-based systems and small classifiers routinely fail. A language model's contextual understanding is the right tool here. Even a 3B model handles tone better than any word-list or heuristic approach. Constrain the output to a known taxonomy (aggressive / civil / sarcastic / neutral) for consistency.

**Assertion Targets → Prolog**

This is the genuine Prolog use case. Assertion extraction is fundamentally about pattern-matching on argument structure — *subject makes a strong claim about target* — which maps naturally to Prolog rules. The practical pipeline: Python/spaCy does the NLP heavy lifting (dependency parsing, POS tagging, NER) and outputs structured facts; Prolog reasons over those facts with rules that define what counts as a strong assertion. Example:

```prolog
assertion(PostId, Target) :-
    subject_verb_object(PostId, _, Verb, Target),
    strong_claim_verb(Verb).
```

This is not a forced fit — structured claim extraction is a genuine strength of logic programming.

**Fallacy Detection → Local LLM (Ollama)**

Detecting fallacies (ad hominem, straw man, false dichotomy, appeal to authority) requires understanding argument structure, pragmatics, and context. Prolog could define rules for these patterns, but getting from raw post text to the structured representation Prolog needs is the hard part — and it would require LLM or deep NLP preprocessing anyway. The LLM handles this end-to-end. Constrain output to a known fallacy taxonomy for usable results.

### Notes

- The LLM carries two tasks (Tone Detection and Fallacy Detection) because both genuinely require semantic understanding that the other options cannot match at reasonable effort.
- The Prolog assignment (Assertion Targets) satisfies the "find at least one use case" requirement and is a genuine architectural fit, not a forced one.
- The Python/Prolog pipeline for Assertion Targets implies swi-prolog installed on the server and a small interop layer; JPL (Java-Prolog bridge) is an option if direct Java integration is preferred over a subprocess call.

---

## WP-14: Pasted Image Recovery (2026-05-18)

**Agent: Pete** | **Script version: 0.9.11 → 0.9.12**

### Background

A subset of OPP users post meme images by pasting them directly rather than using `[img][/img]` markup. The OPP platform appends these as `<img>` tags inside a centered `<div>` at the end of the post. For some posters the image *is* the entire message — without it, the collected post is effectively empty, leaving gaps in conversation reconstructions.

### Design Decision

Two options were considered: a new `images` field on the post record, or appending the image HTML to the existing `html` field. The `html` field was chosen because:
- The images are part of the same post on the OPP site and are already expressed as HTML
- No backend schema change required (`posts` collection in MongoDB unchanged)
- The image content is available to R7 and any other consumer of the `html` field without additional handling

### Implementation

**File:** `JScript/OPP-UserScripts/OPP-Extractor.user.js`

In `processPage()`, after the post body div (`div[2]`) is extracted as normal, the script now walks the direct children of each `contentlook` div and appends any that are centered image containers:

```javascript
Array.from(post_collection[j].children).forEach(function(child) {
    if (child.style.textAlign === 'center' && child.querySelector('img[style*="max-width"]')) {
        post_content += child.outerHTML;
    }
});
```

The two-part filter keeps this targeted:
- `text-align:center` — identifies pasted image divs (direct siblings of the body div), excludes the author header, spacer, and signature divs
- `img[style*="max-width"]` — matches pasted content images (`max-width:100%; max-height:850px;`) and excludes avatar images (`float:left; margin-right:1%`)

### OPP contentlook DOM structure (for reference)

| Index (getElementsByTagName) | Element | Notes |
|---|---|---|
| div[0] | Author header | Contains avatar img and profile link |
| div[1] | Spacer | `class="smalltext"` |
| div[2] | Post body | Captured as before — text and quote-reply divs |
| div[3+] | Pasted image div(s) | `style="text-align:center"` — **newly captured** |
| last | Signature | `class="postsigtext"` — ignored |

### Test Suite Fixes

The existing test suite had several pre-existing failures that were resolved in this session:

| Issue | Fix |
|---|---|
| `getTopicNumber` / `getCurrentPage` not exported | Added getter functions inside the IIFE and included them in `module.exports` |
| `innerText` returns empty in JSDOM (no layout engine) | Created `jest.setup.js` polyfilling `innerText` → `textContent`; wired via `setupFilesAfterEnv` in `package.json` |
| `OPP-KeyCapture-prototype-01.user.js` missing | Created the source file implementing `initKeyCapture({doc, logger})` to match the existing test contract |
| Post extraction test HTML mismatched the real OPP DOM | Fixed to use three sibling divs inside `contentlook` (author / spacer / body) so `div[2]` resolves correctly |

Two new tests added covering the image extraction feature:
1. Pasted image div HTML is appended to `post.html`
2. Avatar images and non-image centered divs are not included

**Result: 12/12 tests passing.**

### Verification

Tested end-to-end via the `OPP-Extractor-DEV` bridge script on Typhoon (Win-11/Chrome/TamperMonkey), which uses a `@require` pointing to the working file:
```
@require file:///C:/Users/ndean/Projects/software/JScript/OPP-UserScripts/OPP-Extractor.user.js
```
Uploads confirmed working with pasted images included in the `html` field.

