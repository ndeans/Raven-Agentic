---
aliases:
---
---
## Operation R7 - Conversations: 
Status: Solved
In Answer To: [[Challenges#Operation R7 Conversations]]
### Input : (upload_id)
Operation R7 will be initiated with a single parameter query against the content database (MongoDB) in which an upload_id will be used to extract all the posts containing that upload_id. 
```
db.posts.find(
{upload_id: "337"},
{post_id: 1, author: 1, head: 1, html: 1, link: 1}
)
```
For each returned row representing a post in the upload, the contents will be copied or linked to a new post_object in the operational data structure.  

### Operational Data Structure:
This structure will be initialized with the results of the query mentioned in the last section, but there will be an additional "width" field added and initialized to zero. 
```
list
	post 
		post_id, author, head, html, link, width	
```
The "post_id", "author", "head", "html" and link fields will be copies or references to those same fields in each post contained in the list.  The "width" will be a numeric field containing the count of responding posts. 

### Output : Data Structure:
This structure will be used to reconstruct the conversations. They will be instantiated each time a conversation is discovered and added to a list. At the end of the operation there will be a list of conversations that will be sent to the UI.
```
List 
	Conversation
		Post #1
		Post #2
```

### Linking (Approach #1) - Nested Loops / Text Matching

#### Overview
Inside the "html" field, there are two divisions, one nested inside another. For example...
```
<div style="margin-top:1%; margin-left:1%; margin-right:1%; color:#333333;" class="smalltext">Turtle keeper wrote:
	<div class="quote_colors" style="border-width:1px; border-style:dotted;          padding-left:1%; padding-right:1%; line-height:1.5em;">Oops. A Republic and      a Democracy are 2 DIFFERENT types of government.  Look it up and read it.        How many times does the word Democracy show up in the Constitution?  Go          ahead and guess…..</div>
</div>
```
The rest of the HTML field will be content from the author of the post being inspected.

The key here is in the content of the outside division (class="smalltext"), before the start of the inside division (class="quote_colors"). So in this case, "Turtle keeper wrote:" If we remote:" at the end we are left with "Turtle keeper" which is the author of the post that the current post is responding to.

But Turtle keeper may have written several previous posts so we can't rely on that alone to find the specific post being responded to. For that we have to go further...

The inside division contains the contents of the uplink post. Here we need to use pattern matching to find the specific post by Turtle keeper that we are looking for. Since many of the posters are grammatically incompetent, we can't depend on grammatical symbols like periods. It's probably safer to go with a word count.   

So if we pull out the first 20 words, we get...

*"Oops. A Republic and a Democracy are 2 DIFFERENT types of government.  Look it up and read it. How many"

Now were are reducing potential up-links to those authored by Turtle keeper and starting with those 20 words. 

On the very unlikely chance that there are more than one post within the upload by the same author with the same first 20 words within the upload, I am willing to allow the system to error out... Abandon the attempt, log the issue and move on. Otherwise, once a match is found. The "width" field for the current object in the outside loop should be incremented. 

#### Operational Loops:
The operation starts by using the input query to generate a return set of posts which it will map, row by row, to the operational data structure.

Once the data is loaded, the system will loop through each post record using a recommended
a loop through the entire query result (entire upload by default) at the first post, which can be determined by the post_id. 
There will be an outside loop, run once through all the objects in the list. For each object, there will be an inside loop that looks for posts that are in direct response. If none are found, the "width" field remain at zero for the current object in the outside loop. Otherwise, the width will be incremented for every responding post found.
#### Standalone Comments:
Any post object in which the html field is missing the nested divisions to describe an up-link post and for which the width is zero at the completion of the outside loop, should be removed from the list.

#### Sample Process:

###### from MongoDB:

| post   | author | quoted author    | quote                     | blabber                   |
| ------ | ------ | ---------------- | ------------------------- | ------------------------- |
| post 1 | bob    | (original post)  |                           | The moon is cheese.       |
| post 2 | frank  | reply to bob     | The moon is cheese.       | get a job.                |
| post 3 | hilda  | reply to bob     | The moon is cheese.       | That would be impossible. |
| post 4 | chuck  | reply to hilda   | That would be impossible. | Why?                      |
| post 5 | julie  | reply to bob     | The moon is cheese.       | You must be hungry - lol  |
| post 6 | hilda  | reply to chuck   | Why?                      | There's no cows in space. |
| post 7 | hank   | (reply no quote) |                           | frank rhymes with hank :) |
| post 8 | bob    | reply to julie   | You must be hungry - lol  | very hungry - lol         |

###### Nested Loops:  

| post | quote | width |       |       |       |                                 |
| ---- | ----- | ----- | ----- | ----- | ----- | ------------------------------- |
| 1    | 0     | 3     |       |       |       | inner loops for 2,3,5           |
|      | post  | quote | width |       |       |                                 |
|      | 2     | 1     | 0     |       |       | two-post conversation, depth=2  |
|      | 3     | 1     | 1     |       |       | inner loop for 4                |
|      |       | post  | quote | width |       |                                 |
|      |       | 4     | 3     | 1     |       | inner loop for 6                |
|      |       |       | post  | quote | width |                                 |
|      |       |       | 6     | 4     | 0     | sustained conversation, depth=4 |
|      | 5     | 1     | 1     | 1     | 0     | inner loop for 8                |
|      |       | 8     | 5     | 0     | 0     | sustained conversation, depth=3 |
| 7    | 0     | 0     |       |       |       | removed                         |

###### Conversations:

| conversation | post sequence |
| ------------ | ------------- |
| 1            | 1,2           |
| 2            | 1,3,4,6       |
| 3            | 1,5,8         |


### Linking (Approach #2) - Recursive Functions / CSS Selectors

#### The Library: Jsoup

In Java, the standard tool for this job is **Jsoup** — a widely-used HTML parsing library. It lets you point at an HTML string and say _"find me the element matching this CSS selector"_ — exactly like a browser's developer tools. Clean, battle-tested, one dependency.
#### The Two CSS Selectors You Need

Given this HTML structure from a quote-reply post:
```html
<div class="smalltext">Turtle keeper wrote:
    <div class="quote_colors">Oops. A Republic and a Democracy...</div>
</div>
```
html

| What you want                                | CSS Selector       | Returns                                 |
| -------------------------------------------- | ------------------ | --------------------------------------- |
| The outer wrapper (contains author name)     | `div.smalltext`    | `"Turtle keeper wrote:"`                |
| The inner quote block (contains quoted text) | `div.quote_colors` | `"Oops. A Republic and a Democracy..."` |

If **neither selector finds anything** → the post has no quote → it's a potential root post.

This replaces all the fragile string-searching in Approach #1 with two clean, targeted lookups.


#### The Four Phases
---
##### Phase 1 — Load

Query MongoDB with the `upload_id`. Map each result into your operational data structure, adding one new field:
```
post_id, author, head, html, link, width, uplink_post_id
```
`uplink_post_id` starts as **null** for every post.

---
##### Phase 2 — Parse & Link

Loop through every post. For each one, use Jsoup to check the HTML:
```
For each post in the list:

    outerDiv = jsoup.select("div.smalltext")
    innerDiv = jsoup.select("div.quote_colors")

    If innerDiv is found:
        quotedAuthor  = outerDiv.ownText()         // "Turtle keeper wrote:"
                        .removeSuffix(" wrote:")   // → "Turtle keeper"

        quotedText    = innerDiv.text()            // plain text, tags stripped
                        .first20Words()            // → first 20 words

        match = find post in list where:
                    author == quotedAuthor
                    AND html.first20Words == quotedText

        If match found:
            post.uplink_post_id = match.post_id
            match.width += 1

        If no match found:
            log the issue, skip this link
```
After this phase, every post either points to its parent or doesn't. The graph is built.

---
##### Phase 3 — Find the Roots
```
roots = all posts where uplink_post_id == null
                    AND width > 0
```
_(Posts where both are null/zero are the standalone comments — discard them.)_

---
##### Phase 4 — Recursive Conversation Builder

This is the heart of the new approach. One function, calling itself:

```
function buildConversations(currentPost, pathSoFar):

    children = all posts where uplink_post_id == currentPost.post_id

    // BASE CASE — no one replied to this post
    If children is empty:
        save pathSoFar as a completed Conversation
        return

    // RECURSIVE CASE — go deeper for each child
    For each child in children:
        buildConversations(child, pathSoFar + [child])


// Kick it off from each root
For each root in roots:
    buildConversations(root, [root])
```

That's it. The function doesn't know or care how deep the tree goes. It just keeps diving until it hits a leaf, then saves the path.

##### Traced Against Your Sample Data

After Phase 2, the links look like this:

```
Post 1 → (root)
Post 2 → uplink: Post 1
Post 3 → uplink: Post 1
Post 4 → uplink: Post 3
Post 5 → uplink: Post 1
Post 6 → uplink: Post 4
Post 7 → (root, but width=0) ← discarded in Phase 3
Post 8 → uplink: Post 5
```
Phase 4 traces:

```
Start at Post 1
├── Child: Post 2 → no children → save [1, 2]          ✓ Conv 1
├── Child: Post 3 → has child Post 4
│   └── Child: Post 4 → has child Post 6
│       └── Child: Post 6 → no children → save [1,3,4,6] ✓ Conv 2
└── Child: Post 5 → has child Post 8
    └── Child: Post 8 → no children → save [1,5,8]     ✓ Conv 3
```
Matches your expected output exactly.

##### What This Approach Buys You

| Approach #1 (original)                 | CSS Selector + Recursion                            |
| -------------------------------------- | --------------------------------------------------- |
| Manual string searching in raw HTML    | Jsoup handles HTML parsing cleanly                  |
| Fragile to whitespace / tag variations | CSS selectors are robust to style attribute changes |
| Two-level loop (depth limited)         | Recursive — handles any depth                       |
| No explicit parent link stored         | `uplink_post_id` makes the tree queryable           |
| Conversation rule implied              | Root-to-leaf rule is explicit in the code           |


---

## Operation M2 - Selective Removals
Status: Solved
In Answer To:  [[Challenges#Operation M2 Selective Removals]]

Good thinking on M2 as the cleanup vehicle. Two things worth noting for when you design it:

What M2 needs to delete: upload_id in uploads table (MariaDB) + all posts documents where upload_id = ? (MongoDB) — same cascade pattern as M1's sweep phase, just triggered on demand rather than by the pruning query.
Filtering targets: The query is simple: WHERE html IS NULL or equivalently, just all uploads below a certain upload_id threshold (~300).
-- Larry


## Daily Verification  (Operation M3)
Status: Solved
Run daily at 9:00 AM

From MariaDB:
```
--- total number of posts according to post_count for each upload.
select sum(post_count) from uploads;
```
From MongoDB:
```
--- count number of post documents.
db.posts.countDocuments({})

```
The results from both of these queries SHOULD match. 
If they do...
- send a timestamped confirmation, to the Raven Log. 
If they don't...
- send a timestamped notification, with details on the mismatch to the Raven Log 
- open an issue on GitHub under the @Raven project.
- send a notification to ncdeans@gmail.com

## Raven Log
Status: Solved
Raven Log doesn't exist yet. The Raven project has come to a point where we probably need to start thinking about a unified logging and notification system. This is what I am referencing as Raven Log.

The starter project that the Java projects are based on, wire in SL4J and Logback. But when Larry was writing scripts to call Raven-Jobs and I asked him to include a log that is consumable by system like Splunk, he went with the ndjson format.

So it seems there is more than one logging system... I'm not even sure at this point that all of the java projects are using the same system. I think the older jakarta projects might be using Log4J. So this is why I think we need to unify the logging.

Ultimately, these are the requirements.
- timestamps
- consumable (ndjson, etc...)
- split output - (parameterized) 

With the split (I'm thinking something like a tee command in bash), I'm imagining three sinks for output. 
- output to console  (raw format, not wrapped up in json) 
- output to file : (ndjson or similar format)
- output to DB : (raw format, not wrapped up in json)

### Notes
The output to DB will require a new table added to the raven-1 database running on the Maria DBMS, as that is where I would want Raven Log to exist.

Since we're including conditional logic to the logging system, we could also use it to offload some of the notification options from other skills.

To parameterize the distribution of the Raven Log, we can just use a config file, which also doesn't exist yet.


This will be a new table in the raven-1 database (MariaDB) This table should include two fields...

- unique key (sequence number)
- timestamp 
- message (ndjson object)

We are using ndjson formats for the logs generated by the bash scripts calling Raven-Jobs. Larry started this when I asked for a log file that is consumable by other systems like Splunk. 

## Content Redundancy 
Status: Solved
Maintenance Query: 
There's a lot of repeated content being stored in the database.
Solution: Operation-M1

## Keywords
Status: Open
Research Queries: 
Find all uploads with keyword matches in topic_title. 
Find all posts with keyword matches with author

## Historical Post Counts
Status: Open
The second column in the view-conversations table, titled "Posts" is a count of how many posts are in the listed conversation.

I would like to expand on that by also counting the number of posts from the root post of the listed conversation back to the original post for the topic.

So using the model in Official/Operation R7.md, We can assume that Operation R7 will find the third post by Hilda has responses and tag that post as the root post for an extracted  conversation. It turns out there are three posts in that conversation. there is...

post #3: (hilda) root post for conversation
post #4: (chuck)
post #6: (hilda)

With this, R7 returns a count of 2

But there needs to be a tie in to the root for the topic or users will get disoriented. So let's also count back to the original post for the topic, which is bob. In this case, post #3 was a direct response to post #1 (which is always the original post) So there is only one post to count. Then display both numbers. So for our little model, it would be...

| # | Posts | Root Author | ...
| n | 1/2     | hilda              | 


## Filter Conversations on Author
Status: Open

Need to work on this.
## Classifying by Subject Matter
Status: Open

I feel this one is going to be key to the kind of analysis I want to do.

One way of looking at this would be to imagine a report generated on a specific subject matter that includes all the conversations about it.

This is where inference will make an entry into the Raven system

## Search Posts by Subject Matter
Status: Open

OPP topics often spin off tangents that can sometimes develop into serious conversations that have little to do with the original post.

I would like a way to query the posts regardless of topic, while identifying subject matters and collecting all the posts related to such subjects matters in their own categories.

This can be repeated every time a report is generated, or maybe once with additional fields in MongoDB or corresponding meta records in MariaDB that get populated as a result.

## Isolate the Nonsense
Status: Open

A LOT of the posts found within the topics are irrelevant insults and dismissals. I would like a way to isolate those for two purposes. 

1. To track how much nonsense particular topics or authors draw.
2. To track how much of an authors contributions ARE nonsense.
3. To remove the nonsense from queries in search of substance.

## Listing by Author

db.posts.find({
    author: "AuntiE",
},{
    topic_id: 1, 
    upload_id: 1, 
    author: 1, 
    text: 1 
})


## Saving Pasted Images

A fair number of people who post on OPP are barely literate. It's important to capture their sentiments because almost all of them are US citizens who vote. But this does offer linguistic challenges. It offers other challenges too, like how they don't know to insert an image using the [img][/img] markup. Instead, they just cut and paste their images, which the OPP system just adds to the end of the post, which the current user script isn't designed to pick up.

The problem with this is that for these simpletons, the image is often a meme that itself IS the post. They just copy and paste in a meme and call it a day, often these are original posts and if we can't collect the image, we are left with conversations about unknown statements.

example: selector:
body > div:nth-child(1) > div:nth-child(10) > div:nth-child(4) > img

same example: html:
```
<div class="contentlook">
    <div style="color:#333333;">
	    <img src="https://static.onepoliticalplaza.com/upload/2024/7/25/t1-285129-20240725_065254.jpg" alt="" class="avatar_responsive_width_topic" style="float:left; margin-right:1%;">
        <span style="white-space:nowrap;"><a href="/user-profile?usernum=2425" class="tdn vsc">archie bunker</a></span>
        <span class="smalltext" style="white-space:nowrap;">(a regular here)</span>
        <span class="smalltext" style="white-space:nowrap;">Joined: Aug 14, 2013</span>
        <span class="smalltext" style="white-space:nowrap;">Posts: 43593</span>
        <span class="smalltext" style="white-space:nowrap;">Loc: Texas</span>
    </div>
  
    <div class="smalltext" style="clear:both;">&nbsp;</div>
    <div style="line-height:1.5em; margin-top:1%; overflow-wrap:break-word;">Lefties first, please!</div>
    <div style="text-align:center; overflow-wrap:break-word;">
        <br>
        <img src="https://static.onepoliticalplaza.com/upload/2016/6/15/699318-fb_img_1466031829200.jpg" alt="" style="max-width:100%; max-height:850px;"><br>
    </div>
   
    <div class="postsigtext" style="margin-top:1em; margin-right:20%; border-top-style:solid; border-width:1px; border-color:#999999; overflow-wrap:break-word;">
        archie bunker
    </div>
    <br> 
 </div>

```



## Title Categorization

I would like a way for the Raven system to on command do the following...

1. run M-series operations (M1, M3) to remove duplicate records in the database.
2. list topic_title fields from a selection of upload records on MariaDB.
3. For each topic_tile, infer a category of political issues, that "seems" appropriate for the title.
4. output a map of all the titles and the inferred categories.


## Color Modes

I would like a way to change the colors of the UI to suit light-mode and dark-mode options. Currently, the design uses default settings.  

Following is a list of CSS overrides that apply to all screens regardless of mode.

| `all`                                                                                                                                                                                                                                                                                                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| table {<br>	border-spacing: 1px;<br>	background-color: darkgrey;<br>}<br><br>th { <br>	background-color: (#ebebe3);<br>	text-align: left;<br>	font-weight: 600;<br>}<br><br>td {<br>    font-size: 14;<br>}<br><br>.btn-upload {<br>	background-color: goldenrod;<br>	border: 1px solid darkgrey;<br>}<br><br>.btn-dialogs {<br>    background-color: (#16d96d)<br>	border: 1px solid darkgrey;<br>} |
|                                                                                                                                                                                                                                                                                                                                                                                                      |

Following is a lost of CSS overrides on response to mode. 

| `light`                                                                                                                                                                  | `dark`                                                                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| body { background-color: floralwhite }<br><br>td {<br>	font-size: 14pt; <br>	color: black;<br>	background-color: floralwhite;<br>}<br><br>h1 { <br>    color: black<br>} | body { background-color: darkgrey }<br><br>td {<br>	font-size: 14pt; <br>	color: cornsilk;<br>	background-color: (#2f3533);<br>}<br><br>h1 { <br>    color: cornsilk<br>}<br> |
|                                                                                                                                                                          |                                                                                                                                                                               |

The challenge is to switch styles on the client side.

We need to add a user option, perhaps a 


## Scrolling Tables

We need to modify the UI so that tables are scrollable in all view pages. 


## User Options

We need to add a feature for user options. On the main (view-uploads) page, we need to add a hamburger button  in the upper right above the data table. Clicking on this should bring up a menu with the following items.

### UI Access

| User Option            | Facilitated As        |
| ---------------------- | --------------------- |
| About                  | Link to an About Page |
| Light/Dark Mode switch | Toggle switch         |

## File Persistence

The options should be recorded to a configuration file or a cookie or some other kind of persistent storage so that when the user brings up Raven on his browser, his configuration is as he left it.





































