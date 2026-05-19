## Formatting of docs in underscore-files.

I noticed that the output from Larry (Antigravity agent) looks much nicer in the Antigravity application than it does in the markdown files included in the project. 

This is not a critical issue but it's kind of an irritant. The following is an example... After onboarding Larry at the start of a session, I requested an analysis of the cost of that onboarding process. Larry complied by offering the stats within Antigravity, that looks like this...

### Original rendered in Antigravity.
---

![alt text](image.png)

---
I responded with this...

*Please Add that cost analysis as you formatted to section ### Larry - Onboarding Cost Analysis 2026-03-17 in the _review.md file.*

### First write to _review.md file
---
The onboarding process was quite efficient. Here is a breakdown of the "cost" in terms of operations and estimated data processed during my initialization as Larry:

Operational Summary
Total Tool Calls: 20
Directory/File Discovery: 6 calls (list_dir, find_by_name)
Documentation & Code Reads: 5 files (Totaling ~515 lines of code/markdown)
Task/Artifact Management: 8 calls (Setting up and updating 
task.md
 and UI state)
Communication: 1 call (notify_user)
Data & Token Estimates
While I don't have access to the exact sub-cent token billing, I can estimate the volume of data retrieved:

Activity	Details	Estimated Volume
Documentation Reads	
Initiator.md
, 
_review.md
~10.5 KB (~3k tokens)
Code Analysis	
Maria_DAO
, 
MongoDao
, 
PruneJob
~10.8 KB (~3k tokens)
Context Overhead	System prompts, tool definitions, directory listings	~15-20k tokens*
Total Input Processed	Includes full conversation history	~25k - 30k tokens
*Context overhead is common for agentic workflows to ensure I understand the tools and project constraints perfectly.

Files Analyzed
Raven Project Initiator.md
 (195 lines)
_review.md
 (35 lines)
Maria_DAO.java
 (127 lines)
MongoDao.java
 (100 lines)
PruneJob.java
 (58 lines)
I am now fully cached and context-aware, ready to jump into implementation!

---
My response... "*hmm... OK, that works but the formatting in your initial analysis is better. I like the icons. :)*"

Larry responded with...

*I've updated _review.md to use the initial, more stylized formatting with bolded headers and specific bullet points. I agree—the extra formatting makes the summary much easier to scan! 
View the reformatted _review.md

But when I did that, this is what I found...

### second write to _review.md
---
#### **Operational Summary**

* **Total Tool Calls**: 20

* **Directory/File Discovery**: 6 calls (`list_dir`, `find_by_name`)

* **Documentation & Code Reads**: 5 files (Totaling ~515 lines of code/markdown)

* **Task/Artifact Management**: 8 calls (Setting up and updating `task.md` and UI state)

* **Communication**: 1 call (`notify_user`)
  
#### **Data & Token Estimates**

| Activity | Details | Estimated Volume |

| :--- | :--- | :--- |

| **Documentation Reads** | `Initiator.md`, `_review.md` | ~10.5 KB (~3k tokens) |

| **Code Analysis** | `Maria_DAO`, `MongoDao`, `PruneJob` | ~10.8 KB (~3k tokens) |

| **Context Overhead** | System prompts, tool definitions, directory listings | ~15-20k tokens* |

| **Total Input Processed** | Includes full conversation history | **~25k - 30k tokens** |

  
*\*Context overhead is common for agentic workflows to ensure I understand the tools and project constraints perfectly.*

#### **Files Analyzed**

1. **[Raven Project Initiator.md](file:///home/ndeans/Projects/Raven/docs/Raven%20Project%20Initiator.md)** (195 lines)

2. **[_review.md](file:///home/ndeans/Projects/Raven/docs/_review.md)** (35 lines)

3. **[Maria_DAO.java](file:///home/ndeans/Projects/java_projects/Raven-Processor/src/main/java/us/deans/raven/processor/Maria_DAO.java)** (127 lines)

4. **[MongoDao.java](file:///home/ndeans/Projects/java_projects/Raven-Processor/src/main/java/us/deans/raven/processor/MongoDao.java)** (100 lines)

5. **[PruneJob.java](file:///home/ndeans/Projects/java_projects/Raven-Jobs/src/main/java/us/deans/raven/jobs/PruneJob.java)** (58 lines)

---
Definitely better but...

Larry explained that for tables he is using GFM (GitHub Flavored Markdown) and that it might not be rendered the same way depending on the theme Obsidian is using.

I'm going to call this a side_issue for now...
***


## More Notes

● So deployments are managed via standalone.xml. The actual content Wildfly uses is typically stored in standalone/data/content/. The safest way to deploy   
  and revert is via the Wildfly CLI. Let me check where the current WAR content lives and confirm the CLI is available.    
                                                   
● Good — CLI is available. Here's the plan:                                                                                                                                                            
  1. Back up the current Raven-JAXRS.war from standalone/tmp/ to a safe location                                                                             
  2. Build the new WAR via mvn clean package                                                                                                                 
  3. Deploy using the Wildfly CLI with --force                                                                                                               
  4. If anything goes wrong, redeploy from backup                                                                                                                  
● Bash(cp -r 
/opt/wildfly/standalone/tmp/Raven-JAXRS.war 
/home/ndeans/Projects/java_projects/Raven-Jakarta-JAXRS/Raven-JAXRS.war.bak 
&& echo "Backup created")                                                                    
⎿  Backup created                                                                                                                                       
● Backup saved to Raven-Jakarta-JAXRS/Raven-JAXRS.war.bak. Now building the new WAR.    

● Bash(
cd /home/ndeans/Projects/java_projects/Raven-Jakarta-JAXRS 
&& mvn clean package -q 
&& echo "Build successful" 
&& ls -lh target/*.war)     
  ⎿  Build successful                                                                                                     
     -rw-r--r--. 1 ndeans ndeans 9.2M Apr 14 13:52 target/Raven-JAXRS.war                               
● Build successful — Raven-JAXRS.war (9.2M) ready in target/. Now deploying via the Wildfly CLI.

● Bash(
/opt/wildfly/bin/jboss-cli.sh --connect --command="deploy --force /home/ndeans/Projects/java_projects/Raven-Jakarta-JAXRS/target/Raven-JAXRS.war" 
&&  echo "Deplo…)                                                                                                                                          
  ⎿  Interrupted · What should Claude do instead?  

[Yeah, 'cause on a terminal, CTRL+C = interrupt the session and clear the buffer!]


## First Results from Operation R7

353 - 380931 - No Kings Hoax - 475 posts.

$ ~/Projects/devops_projects/raven-ops/bin/**operation-R7-TEST.sh 353**

### R7 Results for upload_id 353  (long list)
=== R7 Results for upload_id: 353 ===
Total conversations found: 169

Conversation 1 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696164] Hoosierdaddy — Mar 31, 2026 23:16:55
  [post_id: 5696519] Common Tator — Apr 1, 2026 11:46:44

Conversation 2 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696323] Arlene-29546 — Apr 1, 2026 08:08:29
  [post_id: 5703319] saltwind 78 — Apr 8, 2026 15:51:17
  [post_id: 5703386] straightUp — Apr 8, 2026 17:15:33

Conversation 3 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696323] Arlene-29546 — Apr 1, 2026 08:08:29
  [post_id: 5704782] Rigby5 — Apr 10, 2026 01:40:23

Conversation 4 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696508] WEBCO — Apr 1, 2026 11:35:30
  [post_id: 5703334] saltwind 78 — Apr 8, 2026 16:09:41

Conversation 5 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696518] jennypaterson — Apr 1, 2026 11:46:41
  [post_id: 5696802] Common Tator — Apr 1, 2026 18:30:57

Conversation 6 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696518] jennypaterson — Apr 1, 2026 11:46:41
  [post_id: 5698582] billw96 — Apr 3, 2026 16:47:00
  [post_id: 5698613] Mikeyavelli — Apr 3, 2026 17:09:24

Conversation 7 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696518] jennypaterson — Apr 1, 2026 11:46:41
  [post_id: 5698582] billw96 — Apr 3, 2026 16:47:00
  [post_id: 5698771] Mogon — Apr 3, 2026 19:30:02

Conversation 8 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5696518] jennypaterson — Apr 1, 2026 11:46:41
  [post_id: 5699259] Common Tator — Apr 4, 2026 11:44:52

Conversation 9 (8 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697919] Rigby5 — Apr 2, 2026 22:18:47

Conversation 10 (11 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697926] convicted mattoid — Apr 2, 2026 22:23:57
  [post_id: 5697949] RandyBrian — Apr 2, 2026 23:04:07
  [post_id: 5697964] jim.lupino51 — Apr 2, 2026 23:22:32
  [post_id: 5698063] RandyBrian — Apr 3, 2026 05:05:43

Conversation 11 (11 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697926] convicted mattoid — Apr 2, 2026 22:23:57
  [post_id: 5697949] RandyBrian — Apr 2, 2026 23:04:07
  [post_id: 5697964] jim.lupino51 — Apr 2, 2026 23:22:32
  [post_id: 5698067] RandyBrian — Apr 3, 2026 05:18:45

Conversation 12 (11 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697926] convicted mattoid — Apr 2, 2026 22:23:57
  [post_id: 5697949] RandyBrian — Apr 2, 2026 23:04:07
  [post_id: 5697993] Rigby5 — Apr 3, 2026 01:29:29
  [post_id: 5698066] RandyBrian — Apr 3, 2026 05:13:51

Conversation 13 (11 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697926] convicted mattoid — Apr 2, 2026 22:23:57
  [post_id: 5697949] RandyBrian — Apr 2, 2026 23:04:07
  [post_id: 5697993] Rigby5 — Apr 3, 2026 01:29:29
  [post_id: 5698414] RandyBrian — Apr 3, 2026 13:23:01

Conversation 14 (10 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697904] convicted mattoid — Apr 2, 2026 21:58:10
  [post_id: 5697915] RandyBrian — Apr 2, 2026 22:15:38
  [post_id: 5697926] convicted mattoid — Apr 2, 2026 22:23:57
  [post_id: 5697949] RandyBrian — Apr 2, 2026 23:04:07
  [post_id: 5698901] convicted mattoid — Apr 3, 2026 22:33:32

Conversation 15 (7 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697906] Rigby5 — Apr 2, 2026 22:00:17
  [post_id: 5697920] RandyBrian — Apr 2, 2026 22:19:20

Conversation 16 (7 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697522] Rigby5 — Apr 2, 2026 15:30:46
  [post_id: 5697903] RandyBrian — Apr 2, 2026 21:57:09
  [post_id: 5697906] Rigby5 — Apr 2, 2026 22:00:17
  [post_id: 5698134] Mikeyavelli — Apr 3, 2026 07:48:21

Conversation 17 (9 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697552] jim.lupino51 — Apr 2, 2026 16:12:14
  [post_id: 5697907] RandyBrian — Apr 2, 2026 22:00:38
  [post_id: 5697910] Rigby5 — Apr 2, 2026 22:11:23
  [post_id: 5697922] RandyBrian — Apr 2, 2026 22:20:48
  [post_id: 5697931] Rigby5 — Apr 2, 2026 22:31:28
  [post_id: 5697937] jim.lupino51 — Apr 2, 2026 22:41:13

Conversation 18 (10 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5697469] microphor — Apr 2, 2026 14:18:53
  [post_id: 5697742] Rigby5 — Apr 2, 2026 18:52:19
  [post_id: 5697909] RandyBrian — Apr 2, 2026 22:06:50
  [post_id: 5697913] Rigby5 — Apr 2, 2026 22:14:14
  [post_id: 5697925] RandyBrian — Apr 2, 2026 22:23:54
  [post_id: 5697934] Rigby5 — Apr 2, 2026 22:36:00
  [post_id: 5697951] RandyBrian — Apr 2, 2026 23:05:25
  [post_id: 5697994] Rigby5 — Apr 3, 2026 01:30:15

Conversation 19 (7 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5700230] womankindconnection — Apr 5, 2026 12:35:00
  [post_id: 5700296] JR-57 — Apr 5, 2026 14:10:00
  [post_id: 5700371] Rigby5 — Apr 5, 2026 15:16:20
  [post_id: 5700387] RandyBrian — Apr 5, 2026 15:29:04
  [post_id: 5700390] Rigby5 — Apr 5, 2026 15:32:38

Conversation 20 (6 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5700230] womankindconnection — Apr 5, 2026 12:35:00
  [post_id: 5700296] JR-57 — Apr 5, 2026 14:10:00
  [post_id: 5700371] Rigby5 — Apr 5, 2026 15:16:20
  [post_id: 5700786] microphor — Apr 6, 2026 06:31:07

Conversation 21 (6 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5700230] womankindconnection — Apr 5, 2026 12:35:00
  [post_id: 5700296] JR-57 — Apr 5, 2026 14:10:00
  [post_id: 5700371] Rigby5 — Apr 5, 2026 15:16:20
  [post_id: 5700787] microphor — Apr 6, 2026 06:32:13

Conversation 22 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5700230] womankindconnection — Apr 5, 2026 12:35:00
  [post_id: 5700296] JR-57 — Apr 5, 2026 14:10:00
  [post_id: 5700383] RandyBrian — Apr 5, 2026 15:26:03

Conversation 23 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5700230] womankindconnection — Apr 5, 2026 12:35:00
  [post_id: 5700409] Mikeyavelli — Apr 5, 2026 16:03:33
  [post_id: 5700415] Rigby5 — Apr 5, 2026 16:09:47

Conversation 24 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696151] Rigby5 — Mar 31, 2026 22:45:10
  [post_id: 5702379] saltwind 78 — Apr 7, 2026 17:09:54

Conversation 25 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696175] RandyBrian — Mar 31, 2026 23:53:47
  [post_id: 5698054] LindaK — Apr 3, 2026 04:13:02

Conversation 26 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696175] RandyBrian — Mar 31, 2026 23:53:47
  [post_id: 5698057] LindaK — Apr 3, 2026 04:23:18

Conversation 27 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696178] Rigby5 — Apr 1, 2026 00:06:14
  [post_id: 5696291] AmericanEagle — Apr 1, 2026 07:37:30
  [post_id: 5697525] sghfuller — Apr 2, 2026 15:39:58
  [post_id: 5701429] convicted mattoid — Apr 6, 2026 17:59:47

Conversation 28 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696178] Rigby5 — Apr 1, 2026 00:06:14
  [post_id: 5696291] AmericanEagle — Apr 1, 2026 07:37:30
  [post_id: 5698058] LindaK — Apr 3, 2026 04:27:10

Conversation 29 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696178] Rigby5 — Apr 1, 2026 00:06:14
  [post_id: 5696291] AmericanEagle — Apr 1, 2026 07:37:30
  [post_id: 5699702] Rigby5 — Apr 4, 2026 21:08:46
  [post_id: 5699878] JR-57 — Apr 5, 2026 05:54:39

Conversation 30 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696210] nonalien1 — Apr 1, 2026 03:26:41
  [post_id: 5698056] LindaK — Apr 3, 2026 04:21:41

Conversation 31 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696238] JR-57 — Apr 1, 2026 06:16:34

Conversation 32 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696300] Mogon — Apr 1, 2026 07:42:28

Conversation 33 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696302] Mikeyavelli — Apr 1, 2026 07:44:24

Conversation 34 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696318] Arlene-29546 — Apr 1, 2026 08:03:07

Conversation 35 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696695] F.D.R. — Apr 1, 2026 16:06:56

Conversation 36 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696797] dawnnotdon55 — Apr 1, 2026 18:28:35

Conversation 37 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5696842] JBasilT — Apr 1, 2026 19:14:01
  [post_id: 5696893] Mikeyavelli — Apr 1, 2026 20:00:54

Conversation 38 (6 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697021] jmtrout — Apr 2, 2026 00:18:44
  [post_id: 5697028] Hoosierdaddy — Apr 2, 2026 00:42:08
  [post_id: 5697040] jmtrout — Apr 2, 2026 01:21:32
  [post_id: 5697048] RandyBrian — Apr 2, 2026 01:44:03
  [post_id: 5698065] LindaK — Apr 3, 2026 05:13:01

Conversation 39 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697051] eastueromutt — Apr 2, 2026 01:48:39

Conversation 40 (12 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697386] jim.lupino51 — Apr 2, 2026 12:52:50
  [post_id: 5697413] RandyBrian — Apr 2, 2026 13:10:36
  [post_id: 5697498] jim.lupino51 — Apr 2, 2026 14:55:46
  [post_id: 5697533] microphor — Apr 2, 2026 15:47:47
  [post_id: 5697537] Rigby5 — Apr 2, 2026 15:51:34
  [post_id: 5698074] microphor — Apr 3, 2026 05:40:40
  [post_id: 5698123] EmilyD — Apr 3, 2026 07:35:21
  [post_id: 5698150] microphor — Apr 3, 2026 08:13:58
  [post_id: 5698155] EmilyD — Apr 3, 2026 08:18:39
  [post_id: 5698169] microphor — Apr 3, 2026 08:29:35
  [post_id: 5698188] EmilyD — Apr 3, 2026 08:40:57

Conversation 41 (10 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697386] jim.lupino51 — Apr 2, 2026 12:52:50
  [post_id: 5697413] RandyBrian — Apr 2, 2026 13:10:36
  [post_id: 5697498] jim.lupino51 — Apr 2, 2026 14:55:46
  [post_id: 5697533] microphor — Apr 2, 2026 15:47:47
  [post_id: 5697537] Rigby5 — Apr 2, 2026 15:51:34
  [post_id: 5698074] microphor — Apr 3, 2026 05:40:40
  [post_id: 5698123] EmilyD — Apr 3, 2026 07:35:21
  [post_id: 5698150] microphor — Apr 3, 2026 08:13:58
  [post_id: 5698197] Hadenough — Apr 3, 2026 08:55:16

Conversation 42 (6 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697386] jim.lupino51 — Apr 2, 2026 12:52:50
  [post_id: 5697413] RandyBrian — Apr 2, 2026 13:10:36
  [post_id: 5697498] jim.lupino51 — Apr 2, 2026 14:55:46
  [post_id: 5697533] microphor — Apr 2, 2026 15:47:47
  [post_id: 5697540] jim.lupino51 — Apr 2, 2026 15:56:03

Conversation 43 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697386] jim.lupino51 — Apr 2, 2026 12:52:50
  [post_id: 5697413] RandyBrian — Apr 2, 2026 13:10:36
  [post_id: 5697498] jim.lupino51 — Apr 2, 2026 14:55:46
  [post_id: 5697899] RandyBrian — Apr 2, 2026 21:56:12

Conversation 44 (3 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697386] jim.lupino51 — Apr 2, 2026 12:52:50
  [post_id: 5697619] Mikeyavelli — Apr 2, 2026 17:04:43

Conversation 45 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5697468] microphor — Apr 2, 2026 14:17:46

Conversation 46 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5698053] LindaK — Apr 3, 2026 04:08:14

Conversation 47 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5698913] stephanie wilson — Apr 3, 2026 22:56:27
  [post_id: 5699015] EmilyD — Apr 4, 2026 05:32:33
  [post_id: 5699039] stephanie wilson — Apr 4, 2026 06:25:58

Conversation 48 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5699251] jeromehartwig — Apr 4, 2026 11:32:57
  [post_id: 5699336] microphor — Apr 4, 2026 13:11:39
  [post_id: 5699399] Hadenough — Apr 4, 2026 14:43:55
  [post_id: 5699745] RandyBrian — Apr 4, 2026 22:30:01

Conversation 49 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5699251] jeromehartwig — Apr 4, 2026 11:32:57
  [post_id: 5699336] microphor — Apr 4, 2026 13:11:39
  [post_id: 5699399] Hadenough — Apr 4, 2026 14:43:55
  [post_id: 5699953] microphor — Apr 5, 2026 08:15:13

Conversation 50 (5 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5699251] jeromehartwig — Apr 4, 2026 11:32:57
  [post_id: 5699336] microphor — Apr 4, 2026 13:11:39
  [post_id: 5699547] billw96 — Apr 4, 2026 18:13:53
  [post_id: 5699747] RandyBrian — Apr 4, 2026 22:39:55

Conversation 51 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5699251] jeromehartwig — Apr 4, 2026 11:32:57
  [post_id: 5699744] RandyBrian — Apr 4, 2026 22:28:30
  [post_id: 5701432] convicted mattoid — Apr 6, 2026 18:04:03

Conversation 52 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5699620] freelarrymondelo — Apr 4, 2026 19:26:12

Conversation 53 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5701399] straightUp — Apr 6, 2026 17:27:07

Conversation 54 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5701489] 1ProudAmerican — Apr 6, 2026 19:04:10

Conversation 55 (2 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5701493] sugardaddy8729 — Apr 6, 2026 19:09:10

Conversation 56 (4 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5706632] LindaK — Apr 12, 2026 00:40:14
  [post_id: 5706638] Rigby5 — Apr 12, 2026 00:47:19
  [post_id: 5706654] RandyBrian — Apr 12, 2026 03:15:30

Conversation 57 (7 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5706632] LindaK — Apr 12, 2026 00:40:14
  [post_id: 5706638] Rigby5 — Apr 12, 2026 00:47:19
  [post_id: 5706683] microphor — Apr 12, 2026 06:17:48
  [post_id: 5707967] Rigby5 — Apr 13, 2026 13:19:59
  [post_id: 5708091] RandyBrian — Apr 13, 2026 15:16:34
  [post_id: 5708424] Rigby5 — Apr 13, 2026 20:40:54

Conversation 58 (11 posts):
  [post_id: 5696143] AmericanEagle — Mar 31, 2026 22:38:22
  [post_id: 5706632] LindaK — Apr 12, 2026 00:40:14
  [post_id: 5706638] Rigby5 — Apr 12, 2026 00:47:19
  [post_id: 5706683] microphor — Apr 12, 2026 06:17:48
  [post_id: 5707967] Rigby5 — Apr 13, 2026 13:19:59
  [post_id: 5708183] microphor — Apr 13, 2026 16:37:21
  [post_id: 5708196] Rigby5 — Apr 13, 2026 16:47:12
  [post_id: 5708657] microphor — Apr 14, 2026 06:13:48
  [post_id: 5708890] Rigby5 — Apr 14, 2026 12:15:49
  [post_id: 5709064] microphor — Apr 14, 2026 14:59:36
  [post_id: 5709191] Rigby5 — Apr 14, 2026 17:05:59

Conversation 59 (2 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5696522] WEBCO — Apr 1, 2026 11:48:38

Conversation 60 (2 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5696531] Common Tator — Apr 1, 2026 11:59:39

Conversation 61 (3 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702365] lindynduff1 — Apr 7, 2026 16:52:45
  [post_id: 5702602] RandyBrian — Apr 7, 2026 22:18:07

Conversation 62 (3 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702365] lindynduff1 — Apr 7, 2026 16:52:45
  [post_id: 5702764] microphor — Apr 8, 2026 06:44:47

Conversation 63 (11 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702915] saltwind 78 — Apr 8, 2026 09:55:12
  [post_id: 5703007] RandyBrian — Apr 8, 2026 11:23:34
  [post_id: 5703152] Rigby5 — Apr 8, 2026 13:12:31
  [post_id: 5703219] RandyBrian — Apr 8, 2026 14:15:19
  [post_id: 5703272] Rigby5 — Apr 8, 2026 15:05:29
  [post_id: 5703757] RandyBrian — Apr 9, 2026 00:10:43
  [post_id: 5704358] Rigby5 — Apr 9, 2026 15:50:33
  [post_id: 5704395] microphor — Apr 9, 2026 16:24:37
  [post_id: 5704422] Rigby5 — Apr 9, 2026 16:40:47
  [post_id: 5704721] RandyBrian — Apr 9, 2026 23:26:08

Conversation 64 (10 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702915] saltwind 78 — Apr 8, 2026 09:55:12
  [post_id: 5703007] RandyBrian — Apr 8, 2026 11:23:34
  [post_id: 5703152] Rigby5 — Apr 8, 2026 13:12:31
  [post_id: 5703219] RandyBrian — Apr 8, 2026 14:15:19
  [post_id: 5703272] Rigby5 — Apr 8, 2026 15:05:29
  [post_id: 5703757] RandyBrian — Apr 9, 2026 00:10:43
  [post_id: 5704358] Rigby5 — Apr 9, 2026 15:50:33
  [post_id: 5704395] microphor — Apr 9, 2026 16:24:37
  [post_id: 5704717] RandyBrian — Apr 9, 2026 23:23:16

Conversation 65 (10 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702915] saltwind 78 — Apr 8, 2026 09:55:12
  [post_id: 5703007] RandyBrian — Apr 8, 2026 11:23:34
  [post_id: 5703152] Rigby5 — Apr 8, 2026 13:12:31
  [post_id: 5703219] RandyBrian — Apr 8, 2026 14:15:19
  [post_id: 5703272] Rigby5 — Apr 8, 2026 15:05:29
  [post_id: 5703757] RandyBrian — Apr 9, 2026 00:10:43
  [post_id: 5704358] Rigby5 — Apr 9, 2026 15:50:33
  [post_id: 5704715] RandyBrian — Apr 9, 2026 23:19:52
  [post_id: 5705223] Rigby5 — Apr 10, 2026 14:20:30

Conversation 66 (3 posts):
  [post_id: 5696166] Grumbledog-35758 — Mar 31, 2026 23:18:28
  [post_id: 5702915] saltwind 78 — Apr 8, 2026 09:55:12
  [post_id: 5703633] Mogon — Apr 8, 2026 21:12:12

Conversation 67 (2 posts):
  [post_id: 5696904] hummerbob722 — Apr 1, 2026 20:12:45
  [post_id: 5696914] RandyBrian — Apr 1, 2026 20:30:16

Conversation 68 (5 posts):
  [post_id: 5696904] hummerbob722 — Apr 1, 2026 20:12:45
  [post_id: 5696986] Mikeyavelli — Apr 1, 2026 22:05:05
  [post_id: 5696995] RandyBrian — Apr 1, 2026 22:19:05
  [post_id: 5697182] Mikeyavelli — Apr 2, 2026 08:06:47
  [post_id: 5697297] RandyBrian — Apr 2, 2026 10:32:23

Conversation 69 (2 posts):
  [post_id: 5696904] hummerbob722 — Apr 1, 2026 20:12:45
  [post_id: 5698920] stephanie wilson — Apr 3, 2026 23:08:08

Conversation 70 (2 posts):
  [post_id: 5697067] eastueromutt — Apr 2, 2026 03:19:43
  [post_id: 5697071] RandyBrian — Apr 2, 2026 03:23:47

Conversation 71 (4 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697531] jim.lupino51 — Apr 2, 2026 15:45:15
  [post_id: 5697745] WEBCO — Apr 2, 2026 18:56:23

Conversation 72 (4 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697531] jim.lupino51 — Apr 2, 2026 15:45:15
  [post_id: 5697905] RandyBrian — Apr 2, 2026 21:58:36

Conversation 73 (8 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697536] Rigby5 — Apr 2, 2026 15:48:32
  [post_id: 5697674] Hadenough — Apr 2, 2026 18:00:59
  [post_id: 5697696] jim.lupino51 — Apr 2, 2026 18:18:58
  [post_id: 5697709] Hadenough — Apr 2, 2026 18:27:04
  [post_id: 5697759] Rigby5 — Apr 2, 2026 19:12:04
  [post_id: 5697769] jim.lupino51 — Apr 2, 2026 19:20:09

Conversation 74 (8 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697536] Rigby5 — Apr 2, 2026 15:48:32
  [post_id: 5697674] Hadenough — Apr 2, 2026 18:00:59
  [post_id: 5697737] Rigby5 — Apr 2, 2026 18:50:18
  [post_id: 5697744] Hadenough — Apr 2, 2026 18:54:32
  [post_id: 5697756] Rigby5 — Apr 2, 2026 19:07:54
  [post_id: 5697760] jim.lupino51 — Apr 2, 2026 19:12:26

Conversation 75 (9 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697536] Rigby5 — Apr 2, 2026 15:48:32
  [post_id: 5697674] Hadenough — Apr 2, 2026 18:00:59
  [post_id: 5697737] Rigby5 — Apr 2, 2026 18:50:18
  [post_id: 5697744] Hadenough — Apr 2, 2026 18:54:32
  [post_id: 5697756] Rigby5 — Apr 2, 2026 19:07:54
  [post_id: 5698000] WEBCO — Apr 3, 2026 01:57:43
  [post_id: 5699639] Rigby5 — Apr 4, 2026 19:44:42

Conversation 76 (7 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697536] Rigby5 — Apr 2, 2026 15:48:32
  [post_id: 5697674] Hadenough — Apr 2, 2026 18:00:59
  [post_id: 5697737] Rigby5 — Apr 2, 2026 18:50:18
  [post_id: 5697744] Hadenough — Apr 2, 2026 18:54:32
  [post_id: 5697849] Rigby5 — Apr 2, 2026 20:59:33

Conversation 77 (4 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697536] Rigby5 — Apr 2, 2026 15:48:32
  [post_id: 5698073] microphor — Apr 3, 2026 05:38:32

Conversation 78 (3 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697513] WEBCO — Apr 2, 2026 15:13:48
  [post_id: 5697692] Hadenough — Apr 2, 2026 18:15:16

Conversation 79 (2 posts):
  [post_id: 5697436] jim.lupino51 — Apr 2, 2026 13:35:57
  [post_id: 5697895] RandyBrian — Apr 2, 2026 21:54:39

Conversation 80 (4 posts):
  [post_id: 5697724] jim.lupino51 — Apr 2, 2026 18:41:46
  [post_id: 5697740] Hadenough — Apr 2, 2026 18:51:28
  [post_id: 5697761] Rigby5 — Apr 2, 2026 19:14:31
  [post_id: 5697901] convicted mattoid — Apr 2, 2026 21:56:31

Conversation 81 (5 posts):
  [post_id: 5697732] hummerbob722 — Apr 2, 2026 18:46:54
  [post_id: 5697766] Hadenough — Apr 2, 2026 19:16:20
  [post_id: 5697778] Rigby5 — Apr 2, 2026 19:37:50
  [post_id: 5697845] jim.lupino51 — Apr 2, 2026 20:52:44
  [post_id: 5697857] Mikeyavelli — Apr 2, 2026 21:12:35

Conversation 82 (6 posts):
  [post_id: 5697732] hummerbob722 — Apr 2, 2026 18:46:54
  [post_id: 5697766] Hadenough — Apr 2, 2026 19:16:20
  [post_id: 5697778] Rigby5 — Apr 2, 2026 19:37:50
  [post_id: 5697848] eastueromutt — Apr 2, 2026 20:58:30
  [post_id: 5698711] jim.lupino51 — Apr 3, 2026 18:42:06
  [post_id: 5699014] JR-57 — Apr 4, 2026 05:29:00

Conversation 83 (5 posts):
  [post_id: 5697732] hummerbob722 — Apr 2, 2026 18:46:54
  [post_id: 5697766] Hadenough — Apr 2, 2026 19:16:20
  [post_id: 5697778] Rigby5 — Apr 2, 2026 19:37:50
  [post_id: 5697912] RandyBrian — Apr 2, 2026 22:12:03
  [post_id: 5697917] Rigby5 — Apr 2, 2026 22:17:29

Conversation 84 (2 posts):
  [post_id: 5697732] hummerbob722 — Apr 2, 2026 18:46:54
  [post_id: 5697908] RandyBrian — Apr 2, 2026 22:03:20

Conversation 85 (7 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699756] RandyBrian — Apr 4, 2026 22:54:01

Conversation 86 (11 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699758] RandyBrian — Apr 4, 2026 22:56:24
  [post_id: 5699850] LindaK — Apr 5, 2026 02:50:34
  [post_id: 5700293] RandyBrian — Apr 5, 2026 14:07:50
  [post_id: 5700417] Mikeyavelli — Apr 5, 2026 16:10:32
  [post_id: 5700433] RandyBrian — Apr 5, 2026 16:33:06

Conversation 87 (13 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699758] RandyBrian — Apr 4, 2026 22:56:24
  [post_id: 5699850] LindaK — Apr 5, 2026 02:50:34
  [post_id: 5700293] RandyBrian — Apr 5, 2026 14:07:50
  [post_id: 5700417] Mikeyavelli — Apr 5, 2026 16:10:32
  [post_id: 5701656] Rigby5 — Apr 6, 2026 23:31:35
  [post_id: 5701694] RandyBrian — Apr 7, 2026 01:42:57
  [post_id: 5701700] LindaK — Apr 7, 2026 01:57:57

Conversation 88 (10 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699758] RandyBrian — Apr 4, 2026 22:56:24
  [post_id: 5699850] LindaK — Apr 5, 2026 02:50:34
  [post_id: 5700293] RandyBrian — Apr 5, 2026 14:07:50
  [post_id: 5700784] microphor — Apr 6, 2026 06:26:52

Conversation 89 (13 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699758] RandyBrian — Apr 4, 2026 22:56:24
  [post_id: 5699850] LindaK — Apr 5, 2026 02:50:34
  [post_id: 5700293] RandyBrian — Apr 5, 2026 14:07:50
  [post_id: 5701699] LindaK — Apr 7, 2026 01:52:27
  [post_id: 5701716] RandyBrian — Apr 7, 2026 02:26:39
  [post_id: 5701746] LindaK — Apr 7, 2026 05:25:29
  [post_id: 5701747] RandyBrian — Apr 7, 2026 05:35:42

Conversation 90 (11 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700385] RandyBrian — Apr 5, 2026 15:27:11
  [post_id: 5700389] Rigby5 — Apr 5, 2026 15:30:46
  [post_id: 5700429] RandyBrian — Apr 5, 2026 16:26:47

Conversation 91 (13 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700385] RandyBrian — Apr 5, 2026 15:27:11
  [post_id: 5700389] Rigby5 — Apr 5, 2026 15:30:46
  [post_id: 5700788] microphor — Apr 6, 2026 06:35:54
  [post_id: 5701135] Rigby5 — Apr 6, 2026 13:04:18
  [post_id: 5701467] microphor — Apr 6, 2026 18:28:53

Conversation 92 (13 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701174] Rigby5 — Apr 6, 2026 13:41:50
  [post_id: 5701332] Mikeyavelli — Apr 6, 2026 16:23:57

Conversation 93 (14 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701174] Rigby5 — Apr 6, 2026 13:41:50
  [post_id: 5701375] convicted mattoid — Apr 6, 2026 17:05:46
  [post_id: 5701632] RandyBrian — Apr 6, 2026 22:37:42

Conversation 94 (15 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701174] Rigby5 — Apr 6, 2026 13:41:50
  [post_id: 5701631] RandyBrian — Apr 6, 2026 22:34:34
  [post_id: 5701638] Hadenough — Apr 6, 2026 22:48:25
  [post_id: 5701641] RandyBrian — Apr 6, 2026 22:56:55

Conversation 95 (17 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701174] Rigby5 — Apr 6, 2026 13:41:50
  [post_id: 5701631] RandyBrian — Apr 6, 2026 22:34:34
  [post_id: 5701659] Rigby5 — Apr 6, 2026 23:34:40
  [post_id: 5701695] RandyBrian — Apr 7, 2026 01:43:57
  [post_id: 5701696] convicted mattoid — Apr 7, 2026 01:45:23
  [post_id: 5701712] RandyBrian — Apr 7, 2026 02:17:02

Conversation 96 (14 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701174] Rigby5 — Apr 6, 2026 13:41:50
  [post_id: 5701631] RandyBrian — Apr 6, 2026 22:34:34
  [post_id: 5702224] Rigby5 — Apr 7, 2026 15:00:53

Conversation 97 (12 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701100] RandyBrian — Apr 6, 2026 12:40:50
  [post_id: 5701463] microphor — Apr 6, 2026 18:24:36

Conversation 98 (14 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5701907] RandyBrian — Apr 7, 2026 09:27:40

Conversation 99 (16 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702357] RandyBrian — Apr 7, 2026 16:44:00

Conversation 100 (22 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702358] RandyBrian — Apr 7, 2026 16:44:35
  [post_id: 5702364] Rigby5 — Apr 7, 2026 16:50:54
  [post_id: 5702601] RandyBrian — Apr 7, 2026 22:17:05
  [post_id: 5703176] Rigby5 — Apr 8, 2026 13:36:33
  [post_id: 5703239] RandyBrian — Apr 8, 2026 14:39:00
  [post_id: 5703278] Rigby5 — Apr 8, 2026 15:10:01
  [post_id: 5703760] RandyBrian — Apr 9, 2026 00:20:09

Conversation 101 (18 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702358] RandyBrian — Apr 7, 2026 16:44:35
  [post_id: 5702364] Rigby5 — Apr 7, 2026 16:50:54
  [post_id: 5702761] microphor — Apr 8, 2026 06:35:41

Conversation 102 (19 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702362] Rigby5 — Apr 7, 2026 16:50:11
  [post_id: 5702599] RandyBrian — Apr 7, 2026 22:09:20
  [post_id: 5702604] Rigby5 — Apr 7, 2026 22:19:59
  [post_id: 5702610] RandyBrian — Apr 7, 2026 22:28:26

Conversation 103 (18 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702362] Rigby5 — Apr 7, 2026 16:50:11
  [post_id: 5702759] microphor — Apr 8, 2026 06:27:13
  [post_id: 5702760] JR-57 — Apr 8, 2026 06:32:19

Conversation 104 (18 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5698916] RandyBrian — Apr 3, 2026 23:01:38
  [post_id: 5698930] convicted mattoid — Apr 3, 2026 23:23:16
  [post_id: 5698939] RandyBrian — Apr 3, 2026 23:45:43
  [post_id: 5699591] convicted mattoid — Apr 4, 2026 18:50:08
  [post_id: 5699848] LindaK — Apr 5, 2026 02:27:55
  [post_id: 5700369] Rigby5 — Apr 5, 2026 15:14:49
  [post_id: 5700785] microphor — Apr 6, 2026 06:29:20
  [post_id: 5701026] Rigby5 — Apr 6, 2026 11:44:05
  [post_id: 5701462] microphor — Apr 6, 2026 18:24:11
  [post_id: 5701588] Rigby5 — Apr 6, 2026 21:04:05
  [post_id: 5701868] microphor — Apr 7, 2026 08:38:34
  [post_id: 5702185] Rigby5 — Apr 7, 2026 14:20:54
  [post_id: 5702353] microphor — Apr 7, 2026 16:41:10
  [post_id: 5702362] Rigby5 — Apr 7, 2026 16:50:11
  [post_id: 5702759] microphor — Apr 8, 2026 06:27:13
  [post_id: 5702996] RandyBrian — Apr 8, 2026 11:15:32

Conversation 105 (10 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5699013] JR-57 — Apr 4, 2026 05:25:53
  [post_id: 5699595] convicted mattoid — Apr 4, 2026 18:53:38
  [post_id: 5699698] JR-57 — Apr 4, 2026 21:03:28
  [post_id: 5699700] convicted mattoid — Apr 4, 2026 21:06:03
  [post_id: 5699732] JR-57 — Apr 4, 2026 22:03:50
  [post_id: 5699738] convicted mattoid — Apr 4, 2026 22:13:55
  [post_id: 5699885] JR-57 — Apr 5, 2026 06:03:25
  [post_id: 5700013] Mikeyavelli — Apr 5, 2026 09:28:50

Conversation 106 (14 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5699013] JR-57 — Apr 4, 2026 05:25:53
  [post_id: 5699595] convicted mattoid — Apr 4, 2026 18:53:38
  [post_id: 5699698] JR-57 — Apr 4, 2026 21:03:28
  [post_id: 5699700] convicted mattoid — Apr 4, 2026 21:06:03
  [post_id: 5699732] JR-57 — Apr 4, 2026 22:03:50
  [post_id: 5699738] convicted mattoid — Apr 4, 2026 22:13:55
  [post_id: 5699885] JR-57 — Apr 5, 2026 06:03:25
  [post_id: 5700473] convicted mattoid — Apr 5, 2026 17:35:24
  [post_id: 5700487] RandyBrian — Apr 5, 2026 17:52:47
  [post_id: 5700603] Mikeyavelli — Apr 5, 2026 20:23:30
  [post_id: 5700609] JR-57 — Apr 5, 2026 20:26:37
  [post_id: 5700614] Mikeyavelli — Apr 5, 2026 20:28:36

Conversation 107 (12 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5699013] JR-57 — Apr 4, 2026 05:25:53
  [post_id: 5699595] convicted mattoid — Apr 4, 2026 18:53:38
  [post_id: 5699698] JR-57 — Apr 4, 2026 21:03:28
  [post_id: 5699700] convicted mattoid — Apr 4, 2026 21:06:03
  [post_id: 5699732] JR-57 — Apr 4, 2026 22:03:50
  [post_id: 5699738] convicted mattoid — Apr 4, 2026 22:13:55
  [post_id: 5699885] JR-57 — Apr 5, 2026 06:03:25
  [post_id: 5700473] convicted mattoid — Apr 5, 2026 17:35:24
  [post_id: 5700487] RandyBrian — Apr 5, 2026 17:52:47
  [post_id: 5701434] convicted mattoid — Apr 6, 2026 18:06:51

Conversation 108 (13 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5699013] JR-57 — Apr 4, 2026 05:25:53
  [post_id: 5699595] convicted mattoid — Apr 4, 2026 18:53:38
  [post_id: 5699698] JR-57 — Apr 4, 2026 21:03:28
  [post_id: 5699700] convicted mattoid — Apr 4, 2026 21:06:03
  [post_id: 5699732] JR-57 — Apr 4, 2026 22:03:50
  [post_id: 5699738] convicted mattoid — Apr 4, 2026 22:13:55
  [post_id: 5699885] JR-57 — Apr 5, 2026 06:03:25
  [post_id: 5700473] convicted mattoid — Apr 5, 2026 17:35:24
  [post_id: 5700487] RandyBrian — Apr 5, 2026 17:52:47
  [post_id: 5701671] Rigby5 — Apr 6, 2026 23:45:49
  [post_id: 5701697] RandyBrian — Apr 7, 2026 01:47:10

Conversation 109 (11 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698910] convicted mattoid — Apr 3, 2026 22:49:16
  [post_id: 5699013] JR-57 — Apr 4, 2026 05:25:53
  [post_id: 5699595] convicted mattoid — Apr 4, 2026 18:53:38
  [post_id: 5699698] JR-57 — Apr 4, 2026 21:03:28
  [post_id: 5699700] convicted mattoid — Apr 4, 2026 21:06:03
  [post_id: 5699732] JR-57 — Apr 4, 2026 22:03:50
  [post_id: 5699738] convicted mattoid — Apr 4, 2026 22:13:55
  [post_id: 5699885] JR-57 — Apr 5, 2026 06:03:25
  [post_id: 5700473] convicted mattoid — Apr 5, 2026 17:35:24
  [post_id: 5700547] JR-57 — Apr 5, 2026 18:59:57

Conversation 110 (3 posts):
  [post_id: 5698909] RandyBrian — Apr 3, 2026 22:46:28
  [post_id: 5698914] jim.lupino51 — Apr 3, 2026 22:58:17
  [post_id: 5698918] RandyBrian — Apr 3, 2026 23:03:21

Conversation 111 (3 posts):
  [post_id: 5699053] microphor — Apr 4, 2026 07:13:28
  [post_id: 5699531] Rigby5 — Apr 4, 2026 18:00:38
  [post_id: 5699958] microphor — Apr 5, 2026 08:17:19

Conversation 112 (8 posts):
  [post_id: 5701125] Rigby5 — Apr 6, 2026 12:59:09
  [post_id: 5701171] JR-57 — Apr 6, 2026 13:38:08
  [post_id: 5701177] Rigby5 — Apr 6, 2026 13:46:32
  [post_id: 5701182] JR-57 — Apr 6, 2026 13:54:00
  [post_id: 5701333] Rigby5 — Apr 6, 2026 16:25:03
  [post_id: 5701345] JR-57 — Apr 6, 2026 16:39:55
  [post_id: 5701354] Rigby5 — Apr 6, 2026 16:48:14
  [post_id: 5701363] JR-57 — Apr 6, 2026 16:57:07

Conversation 113 (3 posts):
  [post_id: 5701125] Rigby5 — Apr 6, 2026 12:59:09
  [post_id: 5701171] JR-57 — Apr 6, 2026 13:38:08
  [post_id: 5701364] Rigby5 — Apr 6, 2026 16:57:42

Conversation 114 (3 posts):
  [post_id: 5701125] Rigby5 — Apr 6, 2026 12:59:09
  [post_id: 5701464] microphor — Apr 6, 2026 18:26:23
  [post_id: 5701706] LindaK — Apr 7, 2026 02:03:56

Conversation 115 (2 posts):
  [post_id: 5701633] RandyBrian — Apr 6, 2026 22:39:19
  [post_id: 5701650] convicted mattoid — Apr 6, 2026 23:08:24

Conversation 116 (5 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702352] RandyBrian — Apr 7, 2026 16:40:07

Conversation 117 (7 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702363] JR-57 — Apr 7, 2026 16:50:31
  [post_id: 5702366] Rigby5 — Apr 7, 2026 16:54:30
  [post_id: 5702386] JR-57 — Apr 7, 2026 17:18:45

Conversation 118 (9 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702363] JR-57 — Apr 7, 2026 16:50:31
  [post_id: 5702366] Rigby5 — Apr 7, 2026 16:54:30
  [post_id: 5702605] RandyBrian — Apr 7, 2026 22:20:21
  [post_id: 5702628] Rigby5 — Apr 7, 2026 22:50:42
  [post_id: 5702632] RandyBrian — Apr 7, 2026 22:58:03

Conversation 119 (8 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702363] JR-57 — Apr 7, 2026 16:50:31
  [post_id: 5702366] Rigby5 — Apr 7, 2026 16:54:30
  [post_id: 5702605] RandyBrian — Apr 7, 2026 22:20:21
  [post_id: 5702652] Rigby5 — Apr 7, 2026 23:35:37

Conversation 120 (8 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702363] JR-57 — Apr 7, 2026 16:50:31
  [post_id: 5702366] Rigby5 — Apr 7, 2026 16:54:30
  [post_id: 5702605] RandyBrian — Apr 7, 2026 22:20:21
  [post_id: 5703173] Rigby5 — Apr 8, 2026 13:34:18

Conversation 121 (7 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702266] RandyBrian — Apr 7, 2026 15:33:18
  [post_id: 5702336] Rigby5 — Apr 7, 2026 16:29:52
  [post_id: 5702363] JR-57 — Apr 7, 2026 16:50:31
  [post_id: 5702366] Rigby5 — Apr 7, 2026 16:54:30
  [post_id: 5702765] microphor — Apr 8, 2026 06:47:03

Conversation 122 (3 posts):
  [post_id: 5701690] RandyBrian — Apr 7, 2026 01:32:43
  [post_id: 5702174] Rigby5 — Apr 7, 2026 14:10:54
  [post_id: 5702350] microphor — Apr 7, 2026 16:37:58

Conversation 123 (6 posts):
  [post_id: 5702495] JR-57 — Apr 7, 2026 19:18:21
  [post_id: 5702505] Rigby5 — Apr 7, 2026 19:33:43
  [post_id: 5702512] JR-57 — Apr 7, 2026 19:41:55
  [post_id: 5702578] Rigby5 — Apr 7, 2026 21:14:28
  [post_id: 5702603] JR-57 — Apr 7, 2026 22:19:09
  [post_id: 5702625] Rigby5 — Apr 7, 2026 22:47:12

Conversation 124 (4 posts):
  [post_id: 5702495] JR-57 — Apr 7, 2026 19:18:21
  [post_id: 5702505] Rigby5 — Apr 7, 2026 19:33:43
  [post_id: 5702512] JR-57 — Apr 7, 2026 19:41:55
  [post_id: 5703184] Rigby5 — Apr 8, 2026 13:41:31

Conversation 125 (5 posts):
  [post_id: 5702495] JR-57 — Apr 7, 2026 19:18:21
  [post_id: 5702505] Rigby5 — Apr 7, 2026 19:33:43
  [post_id: 5702608] RandyBrian — Apr 7, 2026 22:24:54
  [post_id: 5702629] Rigby5 — Apr 7, 2026 22:52:24
  [post_id: 5702633] RandyBrian — Apr 7, 2026 22:59:05

Conversation 126 (5 posts):
  [post_id: 5702495] JR-57 — Apr 7, 2026 19:18:21
  [post_id: 5702505] Rigby5 — Apr 7, 2026 19:33:43
  [post_id: 5702608] RandyBrian — Apr 7, 2026 22:24:54
  [post_id: 5702629] Rigby5 — Apr 7, 2026 22:52:24
  [post_id: 5703025] straightUp — Apr 8, 2026 11:40:10

Conversation 127 (4 posts):
  [post_id: 5702495] JR-57 — Apr 7, 2026 19:18:21
  [post_id: 5702505] Rigby5 — Apr 7, 2026 19:33:43
  [post_id: 5702608] RandyBrian — Apr 7, 2026 22:24:54
  [post_id: 5702964] straightUp — Apr 8, 2026 10:50:35

Conversation 128 (3 posts):
  [post_id: 5702746] JR-57 — Apr 8, 2026 05:56:32
  [post_id: 5703148] Rigby5 — Apr 8, 2026 13:09:53
  [post_id: 5703190] RandyBrian — Apr 8, 2026 13:49:47

Conversation 129 (5 posts):
  [post_id: 5702746] JR-57 — Apr 8, 2026 05:56:32
  [post_id: 5703148] Rigby5 — Apr 8, 2026 13:09:53
  [post_id: 5703517] JR-57 — Apr 8, 2026 19:23:05
  [post_id: 5703551] Rigby5 — Apr 8, 2026 19:52:15
  [post_id: 5703769] RandyBrian — Apr 9, 2026 01:08:33

Conversation 130 (2 posts):
  [post_id: 5702977] JR-57 — Apr 8, 2026 10:59:29
  [post_id: 5703034] RandyBrian — Apr 8, 2026 11:48:55

Conversation 131 (2 posts):
  [post_id: 5702977] JR-57 — Apr 8, 2026 10:59:29
  [post_id: 5703276] straightUp — Apr 8, 2026 15:09:00

Conversation 132 (3 posts):
  [post_id: 5702977] JR-57 — Apr 8, 2026 10:59:29
  [post_id: 5703295] Rigby5 — Apr 8, 2026 15:24:55
  [post_id: 5703345] straightUp — Apr 8, 2026 16:26:14

Conversation 133 (2 posts):
  [post_id: 5703016] RandyBrian — Apr 8, 2026 11:34:14
  [post_id: 5703086] straightUp — Apr 8, 2026 12:24:34

Conversation 134 (5 posts):
  [post_id: 5703016] RandyBrian — Apr 8, 2026 11:34:14
  [post_id: 5703159] Rigby5 — Apr 8, 2026 13:19:25
  [post_id: 5703194] RandyBrian — Apr 8, 2026 13:53:46
  [post_id: 5703212] Rigby5 — Apr 8, 2026 14:05:47
  [post_id: 5703235] RandyBrian — Apr 8, 2026 14:31:56

Conversation 135 (8 posts):
  [post_id: 5703016] RandyBrian — Apr 8, 2026 11:34:14
  [post_id: 5703159] Rigby5 — Apr 8, 2026 13:19:25
  [post_id: 5703194] RandyBrian — Apr 8, 2026 13:53:46
  [post_id: 5703212] Rigby5 — Apr 8, 2026 14:05:47
  [post_id: 5703248] RandyBrian — Apr 8, 2026 14:44:13
  [post_id: 5703280] Rigby5 — Apr 8, 2026 15:14:25
  [post_id: 5703761] RandyBrian — Apr 9, 2026 00:21:01
  [post_id: 5704393] Rigby5 — Apr 9, 2026 16:23:17

Conversation 136 (8 posts):
  [post_id: 5703031] RandyBrian — Apr 8, 2026 11:46:54
  [post_id: 5703167] Rigby5 — Apr 8, 2026 13:25:28
  [post_id: 5703200] RandyBrian — Apr 8, 2026 13:58:02
  [post_id: 5703216] Rigby5 — Apr 8, 2026 14:09:53
  [post_id: 5703236] RandyBrian — Apr 8, 2026 14:33:41
  [post_id: 5703274] Rigby5 — Apr 8, 2026 15:07:52
  [post_id: 5703759] RandyBrian — Apr 9, 2026 00:15:03
  [post_id: 5704390] Rigby5 — Apr 9, 2026 16:19:22

Conversation 137 (2 posts):
  [post_id: 5703031] RandyBrian — Apr 8, 2026 11:46:54
  [post_id: 5703241] straightUp — Apr 8, 2026 14:39:52

Conversation 138 (3 posts):
  [post_id: 5703186] RandyBrian — Apr 8, 2026 13:44:49
  [post_id: 5703203] Rigby5 — Apr 8, 2026 14:01:03
  [post_id: 5703234] RandyBrian — Apr 8, 2026 14:31:18

Conversation 139 (2 posts):
  [post_id: 5703186] RandyBrian — Apr 8, 2026 13:44:49
  [post_id: 5703312] straightUp — Apr 8, 2026 15:43:54

Conversation 140 (2 posts):
  [post_id: 5703355] Rigby5 — Apr 8, 2026 16:34:50
  [post_id: 5703398] straightUp — Apr 8, 2026 17:28:04

Conversation 141 (2 posts):
  [post_id: 5703755] RandyBrian — Apr 8, 2026 23:54:13
  [post_id: 5704789] straightUp — Apr 10, 2026 02:04:40

Conversation 142 (3 posts):
  [post_id: 5703766] RandyBrian — Apr 9, 2026 00:51:22
  [post_id: 5704405] Rigby5 — Apr 9, 2026 16:32:05
  [post_id: 5704719] RandyBrian — Apr 9, 2026 23:24:52

Conversation 143 (3 posts):
  [post_id: 5703866] microphor — Apr 9, 2026 06:22:34
  [post_id: 5703927] RandyBrian — Apr 9, 2026 07:52:41
  [post_id: 5704290] microphor — Apr 9, 2026 14:57:55

Conversation 144 (2 posts):
  [post_id: 5704791] Rigby5 — Apr 10, 2026 02:14:52
  [post_id: 5704794] straightUp — Apr 10, 2026 02:48:57

Conversation 145 (3 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5704862] EmilyD — Apr 10, 2026 07:36:50
  [post_id: 5705140] RandyBrian — Apr 10, 2026 13:05:01

Conversation 146 (4 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5704862] EmilyD — Apr 10, 2026 07:36:50
  [post_id: 5705235] Rigby5 — Apr 10, 2026 14:40:18
  [post_id: 5705310] straightUp — Apr 10, 2026 16:23:46

Conversation 147 (3 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5704862] EmilyD — Apr 10, 2026 07:36:50
  [post_id: 5705275] straightUp — Apr 10, 2026 15:37:50

Conversation 148 (7 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5704862] EmilyD — Apr 10, 2026 07:36:50
  [post_id: 5705309] Rigby5 — Apr 10, 2026 16:22:01
  [post_id: 5705365] EmilyD — Apr 10, 2026 17:31:33
  [post_id: 5705369] Rigby5 — Apr 10, 2026 17:33:52
  [post_id: 5705373] EmilyD — Apr 10, 2026 17:39:33
  [post_id: 5705427] Rigby5 — Apr 10, 2026 18:47:05

Conversation 149 (2 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5705197] straightUp — Apr 10, 2026 13:48:13

Conversation 150 (12 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5705229] Rigby5 — Apr 10, 2026 14:28:36
  [post_id: 5705665] RandyBrian — Apr 11, 2026 00:32:06
  [post_id: 5705684] Rigby5 — Apr 11, 2026 00:53:59
  [post_id: 5705691] RandyBrian — Apr 11, 2026 00:59:17
  [post_id: 5705701] Rigby5 — Apr 11, 2026 01:19:06
  [post_id: 5705722] RandyBrian — Apr 11, 2026 01:47:27
  [post_id: 5705741] Rigby5 — Apr 11, 2026 02:13:58
  [post_id: 5705812] RandyBrian — Apr 11, 2026 06:47:10
  [post_id: 5706619] Rigby5 — Apr 12, 2026 00:19:53
  [post_id: 5706626] RandyBrian — Apr 12, 2026 00:27:22
  [post_id: 5706633] Rigby5 — Apr 12, 2026 00:43:09

Conversation 151 (12 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5705229] Rigby5 — Apr 10, 2026 14:28:36
  [post_id: 5705665] RandyBrian — Apr 11, 2026 00:32:06
  [post_id: 5705684] Rigby5 — Apr 11, 2026 00:53:59
  [post_id: 5705691] RandyBrian — Apr 11, 2026 00:59:17
  [post_id: 5705701] Rigby5 — Apr 11, 2026 01:19:06
  [post_id: 5705722] RandyBrian — Apr 11, 2026 01:47:27
  [post_id: 5705741] Rigby5 — Apr 11, 2026 02:13:58
  [post_id: 5705812] RandyBrian — Apr 11, 2026 06:47:10
  [post_id: 5706619] Rigby5 — Apr 12, 2026 00:19:53
  [post_id: 5706626] RandyBrian — Apr 12, 2026 00:27:22
  [post_id: 5706677] microphor — Apr 12, 2026 06:05:19

Conversation 152 (11 posts):
  [post_id: 5704807] RandyBrian — Apr 10, 2026 05:13:15
  [post_id: 5705229] Rigby5 — Apr 10, 2026 14:28:36
  [post_id: 5705665] RandyBrian — Apr 11, 2026 00:32:06
  [post_id: 5705684] Rigby5 — Apr 11, 2026 00:53:59
  [post_id: 5705691] RandyBrian — Apr 11, 2026 00:59:17
  [post_id: 5705701] Rigby5 — Apr 11, 2026 01:19:06
  [post_id: 5705722] RandyBrian — Apr 11, 2026 01:47:27
  [post_id: 5705741] Rigby5 — Apr 11, 2026 02:13:58
  [post_id: 5705812] RandyBrian — Apr 11, 2026 06:47:10
  [post_id: 5706619] Rigby5 — Apr 12, 2026 00:19:53
  [post_id: 5706671] microphor — Apr 12, 2026 06:01:59

Conversation 153 (2 posts):
  [post_id: 5705362] EmilyD — Apr 10, 2026 17:27:57
  [post_id: 5705439] straightUp — Apr 10, 2026 19:11:54

Conversation 154 (4 posts):
  [post_id: 5705366] Rigby5 — Apr 10, 2026 17:31:36
  [post_id: 5705371] EmilyD — Apr 10, 2026 17:36:49
  [post_id: 5705420] Rigby5 — Apr 10, 2026 18:40:25
  [post_id: 5705436] EmilyD — Apr 10, 2026 19:08:33

Conversation 155 (2 posts):
  [post_id: 5705366] Rigby5 — Apr 10, 2026 17:31:36
  [post_id: 5705495] straightUp — Apr 10, 2026 20:40:03

Conversation 156 (2 posts):
  [post_id: 5705437] convicted mattoid — Apr 10, 2026 19:10:38
  [post_id: 5705670] RandyBrian — Apr 11, 2026 00:39:52

Conversation 157 (2 posts):
  [post_id: 5705475] EmilyD — Apr 10, 2026 20:11:46
  [post_id: 5705565] straightUp — Apr 10, 2026 22:06:22

Conversation 158 (2 posts):
  [post_id: 5705667] RandyBrian — Apr 11, 2026 00:36:07
  [post_id: 5705685] Rigby5 — Apr 11, 2026 00:55:41

Conversation 159 (2 posts):
  [post_id: 5705673] RandyBrian — Apr 11, 2026 00:41:53
  [post_id: 5705689] Rigby5 — Apr 11, 2026 00:57:43

Conversation 160 (6 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706584] Hadenough — Apr 11, 2026 23:12:16
  [post_id: 5706669] microphor — Apr 12, 2026 06:00:41
  [post_id: 5707576] Hadenough — Apr 12, 2026 23:55:15
  [post_id: 5707636] microphor — Apr 13, 2026 06:06:34
  [post_id: 5707979] Rigby5 — Apr 13, 2026 13:27:36

Conversation 161 (11 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706584] Hadenough — Apr 11, 2026 23:12:16
  [post_id: 5706669] microphor — Apr 12, 2026 06:00:41
  [post_id: 5707576] Hadenough — Apr 12, 2026 23:55:15
  [post_id: 5707977] Rigby5 — Apr 13, 2026 13:26:19
  [post_id: 5707987] Hadenough — Apr 13, 2026 13:38:20
  [post_id: 5708088] Rigby5 — Apr 13, 2026 15:12:09
  [post_id: 5708100] RandyBrian — Apr 13, 2026 15:21:55
  [post_id: 5708120] Rigby5 — Apr 13, 2026 15:34:07
  [post_id: 5708127] RandyBrian — Apr 13, 2026 15:38:00
  [post_id: 5708153] Rigby5 — Apr 13, 2026 16:06:51

Conversation 162 (9 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706584] Hadenough — Apr 11, 2026 23:12:16
  [post_id: 5706669] microphor — Apr 12, 2026 06:00:41
  [post_id: 5707576] Hadenough — Apr 12, 2026 23:55:15
  [post_id: 5707977] Rigby5 — Apr 13, 2026 13:26:19
  [post_id: 5707987] Hadenough — Apr 13, 2026 13:38:20
  [post_id: 5708088] Rigby5 — Apr 13, 2026 15:12:09
  [post_id: 5708142] Hadenough — Apr 13, 2026 15:49:27
  [post_id: 5708157] Rigby5 — Apr 13, 2026 16:10:40

Conversation 163 (3 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706584] Hadenough — Apr 11, 2026 23:12:16
  [post_id: 5707606] EmilyD — Apr 13, 2026 01:38:55

Conversation 164 (5 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707634] microphor — Apr 13, 2026 06:03:26

Conversation 165 (12 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707973] Rigby5 — Apr 13, 2026 13:23:46
  [post_id: 5708007] EmilyD — Apr 13, 2026 13:54:04
  [post_id: 5708107] Rigby5 — Apr 13, 2026 15:28:37
  [post_id: 5708217] EmilyD — Apr 13, 2026 17:10:33
  [post_id: 5708238] Rigby5 — Apr 13, 2026 17:32:33
  [post_id: 5708250] EmilyD — Apr 13, 2026 17:37:09
  [post_id: 5708273] Hadenough — Apr 13, 2026 17:52:15
  [post_id: 5708284] EmilyD — Apr 13, 2026 17:59:11

Conversation 166 (18 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707973] Rigby5 — Apr 13, 2026 13:23:46
  [post_id: 5708007] EmilyD — Apr 13, 2026 13:54:04
  [post_id: 5708107] Rigby5 — Apr 13, 2026 15:28:37
  [post_id: 5708217] EmilyD — Apr 13, 2026 17:10:33
  [post_id: 5708238] Rigby5 — Apr 13, 2026 17:32:33
  [post_id: 5708250] EmilyD — Apr 13, 2026 17:37:09
  [post_id: 5708287] Rigby5 — Apr 13, 2026 18:05:15
  [post_id: 5708290] EmilyD — Apr 13, 2026 18:13:48
  [post_id: 5708302] Rigby5 — Apr 13, 2026 18:29:31
  [post_id: 5708347] EmilyD — Apr 13, 2026 19:10:10
  [post_id: 5708358] Rigby5 — Apr 13, 2026 19:25:00
  [post_id: 5708385] EmilyD — Apr 13, 2026 19:54:52
  [post_id: 5708403] Rigby5 — Apr 13, 2026 20:07:30
  [post_id: 5708631] EmilyD — Apr 14, 2026 04:03:41

Conversation 167 (17 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707973] Rigby5 — Apr 13, 2026 13:23:46
  [post_id: 5708007] EmilyD — Apr 13, 2026 13:54:04
  [post_id: 5708107] Rigby5 — Apr 13, 2026 15:28:37
  [post_id: 5708217] EmilyD — Apr 13, 2026 17:10:33
  [post_id: 5708238] Rigby5 — Apr 13, 2026 17:32:33
  [post_id: 5708250] EmilyD — Apr 13, 2026 17:37:09
  [post_id: 5708287] Rigby5 — Apr 13, 2026 18:05:15
  [post_id: 5708290] EmilyD — Apr 13, 2026 18:13:48
  [post_id: 5708302] Rigby5 — Apr 13, 2026 18:29:31
  [post_id: 5708664] microphor — Apr 14, 2026 06:23:29
  [post_id: 5708875] Rigby5 — Apr 14, 2026 12:02:47
  [post_id: 5709063] microphor — Apr 14, 2026 14:58:39
  [post_id: 5709188] Rigby5 — Apr 14, 2026 17:03:28

Conversation 168 (9 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707973] Rigby5 — Apr 13, 2026 13:23:46
  [post_id: 5708094] RandyBrian — Apr 13, 2026 15:18:29
  [post_id: 5708113] Rigby5 — Apr 13, 2026 15:30:47
  [post_id: 5708123] RandyBrian — Apr 13, 2026 15:36:32
  [post_id: 5708149] Rigby5 — Apr 13, 2026 16:02:17

Conversation 169 (9 posts):
  [post_id: 5705854] EmilyD — Apr 11, 2026 08:19:08
  [post_id: 5706623] Rigby5 — Apr 12, 2026 00:22:23
  [post_id: 5706676] microphor — Apr 12, 2026 06:04:36
  [post_id: 5706801] RandyBrian — Apr 12, 2026 08:57:31
  [post_id: 5707973] Rigby5 — Apr 13, 2026 13:23:46
  [post_id: 5708190] microphor — Apr 13, 2026 16:42:23
  [post_id: 5708199] Rigby5 — Apr 13, 2026 16:48:35
  [post_id: 5708658] microphor — Apr 14, 2026 06:17:48
  [post_id: 5708896] Rigby5 — Apr 14, 2026 12:22:01

{"@timestamp":"2026-04-15T03:59:00.347711261Z","@version":"1","message":"Raven-Jobs has finished.","logger":"us.deans.raven.App","thread":"main","level":"INFO","level_value":20000,"app":"raven-jobs","operation":"R7","environment":"dev","run_id":"20260415T035858Z"}


## Verification Queries

##### get a count of all documents  (mongo)
```
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments()
```

##### count documents with no html  (mongo)
```
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments({  
    $or: [  
      { html: null },  
      { html: "" },  
      { html: { $exists: false } }  
    ]  
  })
```
###### Or more concisely:
```
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments({  
  html: { $exists: true, $ne: null, $ne: "" }  
})
```
##### list of upload_ids extracted from a collection of all the documents without html. 
```
db.getSiblingDB("Raven-1").getCollection("posts").distinct("upload_id", {  
    $or: [  
      { html: null },  
      { html: "" },  
      { html: { $exists: false } }  
    ]  
})
```
##### count of total records in the uploads table (maria)
```
select count(*) from uploads;
```
## Application Queries

| -------------------------------------------------- | ----------------------------------------------------------------------- |
| -------------------------------------------------- | ----------------------------------------------------------------------- |
| List of Uploads                                    | select * from uploads                                                   |
| Post Collection per Upload                         | collection.find(Filters.eq("upload_id", strUploadId))                   |
### Upload List  
Source: MariaDB
Drives: Raven-Jakarta-Web/view-uploads.xhtml 

**view-uploads.xhtml:** 
```
<h:dataTable value="#{consoleBean.uploads}
```
**ConsoleBean():  (constructor)**
```
private List<RvnJob> uploads;
uploads = maria_dao.getMetaData();
```
**Maria_DAO.getMetaData(): **
```
private String sql_get_job_list = "select * from uploads"
...
statement = maria_connection.prepareStatement(sql_get_job_list)
```

### Post Collection per Upload
Source: MongoDB
Drives: Raven-Jakarta-Web/view-upload.xhtml

**view-upload.xhtml:**
```
<f:viewParam name="jid" value="#{uploadBean.upload_id}"/>
...
<h:form>  
    <h:dataTable  
            value="#{uploadBean.postList}"
```

**UploadBean.init()  (@PostConstruct)**
```
postList = curator.getPostList(upload_id);
```

**OppCurator.getPostList(long)   (implements Curator)**
```
postList = mongoDao.getPostList(upload_id);
...
return postList;
```

**MongoDAO.getPostList(long)**
```
MongoCursor<Document> cursor = collection.find(Filters.eq("upload_id", strUploadId))
```





## 4/15/26 Assessment


Currently, the upload list = 41 upload records
```
select count(*) from uploads;
```
Each record has a post count. All post counts for all 41 uploads =  5,024
```
select sum(post_count) from uploads;
```
*This SHOULD match the total number of documents in the Mongo database. But it doesn't!
```
db.posts.countDocuments({}) 
```
gives me 26,885 - ???

So I got 22,861 orphan documents in Mongo?

I guess I need to start looking for the documents without an html field.
```
db.posts.countDocuments({
  html: { $exists: false }
})
```
20,290. ...



1:40 PM - OK, I found the issue with those queries...
```
db.getSiblingDB("Raven-1")  
  .getCollection("posts")  
  .distinct("upload_id", {  
  $or: [  
        { html: null },  
        { html: "" },  
        { html: { $exists: false } }  
    ]  
})
```
Basically, I was looking for a return values with empty or null values in the html field. But the reality is that upload 340 didn't even have an html field. lol - they're documents, not records!


## Purge 2026!
Since I found over 20,000 documents that don't even have html fields. I figure we can just purge the damned data... then start uploading again. So...
```
db.posts.deleteMany({});
```
And on the MariaDB side...
```
delete from uploads;
```
OK, well that worked. Time to start uploading.



### Here's what else I learned...

##### db.getSiblingDB()
```
db.getSiblingDB("Raven-1").getCollection("posts").countDocuments()
```
The method: db.getSiblingDB("Raven-1"), is **not standard MongoDB query language**—it’s a **Mongo shell helper**.

- Works in:
    - Mongo shell (`mongo`, `mongosh`)
    - Tools that emulate the shell (like **DataGrip**)
- Does **NOT** work in:
    - Drivers (Java, Node, etc.)
    - Some GUI tools (like **DBGate** depending on configuration)

DataGrip is essentially running your query in a **shell-like context**, so:
```
db.getSiblingDB("Raven-1")
```
is valid there.


## Credentials

#### Post Data Database (Document DB) : MongoDB: Raven-1

"mongodb://localhost:27017"
databaseName = "Raven-1"
collectionName = "posts"

#### Metadata Database (Relational DB) : MariaDB: raven_1

jdbc:mariadb://vortex:3306/raven_1
username: bambam
password: bambam


Note: I have not been able to connect to Mongo on Vortex over the network. The Raven components all run on Vortex, including the Mongo database so, the java code uses localhost to address the back end databases. Larry is the only agent currently running on Vortex.


## Logging

### Larry's Answer to my Request for a "consumable" log format.
I would like to use a query-friendly dataset for a log and some systems like Splunk do that. So when Larry was writing the bash scripts to launch jobs, I asked for a "consumable"  format.

Larry's answer was ndjson. (New-line Delimited JSON)

So, he wrote a bash script to make that work - and it does. But I don't really understand it. Larry kept saying something about sending it to Java? 

### Current Usage:



| \|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\| | \|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|\|                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Raven-Processor                                                                          | <dependency>  <br>    <groupId>org.slf4j</groupId>  <br>    <artifactId>slf4j-api</artifactId>  <br>    <version>2.0.13</version>  <br>    <scope>provided</scope>  <br></dependency>                                                                                                                                          |
| Raven-Jakarta-JAXRS                                                                      | <dependency>  <br>    <groupId>org.slf4j</groupId>  <br>    <artifactId>slf4j-api</artifactId>  <br>    <version>2.0.13</version>  <br>    <scope>provided</scope>  <br></dependency>                                                                                                                                          |
| Raven-Jakarta-Web                                                                        | ( no logging dependency - huh )                                                                                                                                                                                                                                                                                                |
| Raven-Jobs <br>*First from Minimal-Starter*                                              | <dependency>  <br>    <groupId>org.slf4j</groupId>  <br>    <artifactId>slf4j-api</artifactId>  <br>    <version>2.0.12</version>  <br></dependency>  <br>  <br><dependency>  <br>    <groupId>ch.qos.logback</groupId>  <br>    <artifactId>logback-classic</artifactId>  <br>    <version>1.5.6</version>  <br></dependency> |
|                                                                                          |                                                                                                                                                                                                                                                                                                                                |


## 4/20/26 Data Flush! Oh No!

Today, I inadvertently deleted all the upload records from the SQL server. 8(

It wasn't a huge loss I just flushed the data out of the entire Raven system and started uploading topics from OPP.  I just finished my first pass (the selection of ALL topics that I initiated since 2013)

```
select count(*) from uploads;         =  154
select sum(post_count) from uploads;  =  20,159

db.posts.countDocuments({})           =  20,159
```
I'm going to run Operation M1 next to eliminate the duplicate downloads. I know there's a few of them.

![[Pasted image 20260420174901.png]]

OK. I ran Operation-M1... Here's what we got...

```
select count(*) from uploads;         =  150
select sum(post_count) from uploads;  =  19,840

db.posts.countDocuments({})           =  19,840

```
![[Pasted image 20260420180353.png]]


## Rendering Content of "html" Field.
Quite happy to discover that the html field does in fact contain the images... Or at least references to the images. 

![[Pasted image 20260420182709.png]]

Of course, I had to go to the site and find a post with an image and copy the post number to the query...
```
db.posts.find({post_id: "5556601"},{_id: 0, html: 1, author: 1})
```
Then I copied the content of the html field to an editor and saved it to my ~/tmp as an HTML before opening the file in Chrome.


## R7 to UI Gap

After some thought... Here's what I want to do... This would either be an expansion of Operation R7, or it will be Operation R8, in which case, the output of Operation R7, as it is now...

![[Pasted image 20260421143154.png|332]]

...would be the input for Operation R8 which would...

1. link to the post document for each post_id and pull the "html" AND "link" fields into a scrollable sequence of posts arranged according to the output that Operation R7 currently returns.
2. add styling (currently that is not coming from the uploads).


## Design Decision - UI
I suppose the expansion of Operation R7 (or addition of Operation R8) would be a call to start the Raven-Jakarta-Analyzer project but I'm second guessing myself on that. We already have a Raven-Jakarta-Web, with the basics already set up. Why not just expand on that? I like the anonymity of "web" too because I'm not 100% sure where this project is going and I don't want to throw down "assignment-sounding" labels just yet.

Currently, Web only has two views
1. complete list of upload records.
2. complete list of posts for a specific upload record.

The point to mention here is that Web is using JSF, as implemented by the Jakarta framework. So if we run into any limitations we may want to consider a client-side "analyst" and a server-side expansion of JAXRS.



## 4/21/26
Pierre had trouble accessing the repositories on GitHub. We need to change that.


## 4/24/26
Larry just finished WP-6... Going over the layout in view-uploads.

1. I would like to avoid conversations that start with the first responder. I want them to start with what the first responder is responding to. In the first conversation this would no doubt be a response to the original post, which is always the first post in the upload and does not typically have a quote div.
2. When we add the post that the first responder is responding to to the beginning of ever conversation, I'd like to visually set that first post apart... For now, lets just go with a very pale blue background for the post-card, like ```
```
   #f0f0ff
```
or something like that.


| -------------------- | ------- |
| Raven-Processor      | master  |
| Raven-Jobs           | main    |
| Raven-Jakarta-JAXRS  | main    |
| Raven-Jakarta-Web    | master  |


## 5/3/26

Rules:

Type 1
IF post has NO quote AND blabber is NOT quoted in subsequent posts
THEN go to the next post

Type 2
IF post has NO quote AND blabber IS quoted in subsequent posts
THEN then create a new conversation 
THEN add the current post 
THEN go to the next post 

Type 3
IF post HAS a quote
THEN add current post to conversation



## 5/5/26

upload 732: 
post #1: 5251390 - discarded cuz responses put author of post #1 in <strong/> * 
post #2: 5251403


upload 786:
post #1: 5722253
post #2: 5722255 - not included as a reply cuz quote 
post #2


I just realized why the author would be in <strong/>... OPP 
57222555722255Cuz the author is the same user that is logged in on OPP highlights the reference to the current user. 



## 5/16/26 - (WP-12) - RFP-Feature-Solution-Map

## 5/16/26 - Agentic-Raven Reassessment

I find myself getting confused a bit, with what my initial intentions were in how I structured the markdown files when I brought agents into the project.

I think now, having used this system for a few months, I would like to establish a less ambiguous understanding. ;)

Ideas/Challenges   -  stuff I think up (can be either file)
Proposals/Reviewed-Specs  -  proposed solutions from or co-authored by AI
Work Prompts  -  First level directions, that may direct to other sources of instruction.
Review - summary of changes, work and notes after build and test. 



## Color Schemes


Dark Mode:

table { 
	border-spacing: 1px;
	background-color: (#454130);
}

`td {` 
	font-size: 14px;
	color: (#ffdf5d);
	background-color: (#1b211e)
`}`



## Purpose of Raven
Status: Open

So far, all my focus has been on the nuts and bolts of the system, but its coming to the point where I want to start developing the value of the system as a whole... It's purpose being to collect anonymous conversations about politics into a dataset against which to execute various types of analysis. 

Output value in the form of reports 





