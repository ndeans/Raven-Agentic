
## WP-11 : Operation R7: Modification

Larry, please review the  [[Issues#Raven-Processor R7Service Phase 2 — Author Extraction Fails for Logged-In User's Posts]]

This section describes a bug that you would have no way of knowing about during development. I just happened to notice the unwanted effect. Please read through and let me know if you're ready to make the modifications or if you have any questions.



## WP-12 : Scan Road Map for Use Cases

I have a list of different features that I want to add to Raven, some of which involve making inferences against the conversational data. I also have a list of different potential solutions for making these inferences. 

I want you to scan both lists and make recommendations for which features make the best use cases for each potential solution.

List of Potential Solutions I am Interested in Experimenting With:

| potential solution              | comment         |
| ------------------------------- | --------------- |
| local LMM (3b or less) on Olama |                 |
| Prolog  (swi-prolog)            | find at least 1 |
| Python                          |                 |
| Java                            | add to JAR      |

List of Features I want to Add to Raven (These would be R-Series Operations)

| feature                 | comment                                                      |
| ----------------------- | ------------------------------------------------------------ |
| Subject Classification  |                                                              |
| Nonsense Filter/Capture | posts with few words containing no value to the discussion.  |
| Tone Detection          |                                                              |
| Assertions Targets      | Isolate strong statements made about specific things (facts) |
| Fallacy Detection       |                                                              |



## WP-13: UI Modifications

There are three changes that I want to make to the UI

1. Make the data tables scrollable.
2. Create a system for allowing user to select a color mode 

Please see the following sections in the Ideas.md file to get details.

User Options
File Persistence
Scrolling Tables
Color Modes


### Follow up


#### Page Headers

I'd like to make some changes to the page header... 
1. Add ``` <h1>Raven Console</h1> ``` just to the left of the hamburger 


#### Table Header


I decided to make the TH header static across both modes

	th { 	color: black; 	background-color: grey; }

#### Conversations View
The hyperlinks are rendered in dark blue. This gets lost against the dark background. Looking for a way to fix that.



Titles

I'm ready to make the page titles a little more user friendly

| page               | H1 title                                        |
| ------------------ | ----------------------------------------------- |
| view-uploads.xhtml | Raven Console: Upload List                      |
| view-upload.xhtml  | Raven Console: Post List for Upload [upload id] |
|                    |                                                 |




## WP-14 - Recovering Attached Images 

This prompt is for Pete, our Javascript and client-side expert.  Please read [[Challenges#Saving Pasted Images]] and let me know 
