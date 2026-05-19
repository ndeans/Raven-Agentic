
## Wildfly Deployment Conflict (2026-03-16)
The Wildfly server fails to boot due to a `Duplicate resource` error for `Raven-JAXRS.war`.
- **Symptoms**: `WFLYCTL0212: Duplicate resource` and reports of duplicate JAX-RS contexts.
- **Hypothesis**: Conflicting entries between `standalone.xml` and the `deployments/` folder, possibly involving a context root overlap between `Raven-JAXRS` and `Raven-Jakarta-JAXRS`.
- **Status**: Investigating `standalone.xml` and `jboss-web.xml`.


## Operation M2: Selective Removals

We need an operation will allow an entire upload to be removed based on the upload_id. That means the record for that upload in the MariaDB will need to be removed plus all the post records in the MongoDB with the same upload_id and like with Operation M1 this will need to be wrapped up in a transaction.  

We also need ways to trigger the process with the upload_id as a parameter value. There should be two ways to do that...

1. From UI listing of uploads in the Raven-Jakarta-Web application.
2. From the Raven-Jobs application.
#### From the UI Listing 
There is already a listing of all the uploads available in the Raven-Jakarta-Web application. Each upload record is preceded with a check box provided to allow for multi-select operations... Removal will certainly be one such operation. 
#### From the Raven-Jobs application
In this case, we can just call the Raven-Jobs application using the same approach as M1 or R7.

## Operation R7 : Conversations

### Overview : 
This operation needs to apply to one upload at a time and will reconstruct the conversations within the upload. To do this, we need to use elements in the html field to link an originating (up-link) post to a responding (down-link) post. 

On the OPP website, members reading a post have the option to "reply" or "quote reply"

If they choose to "reply" the HTML field saved from their resulting post will not contain any divisions describing any other post. But if they chose "Quote Reply" the HTML field saved from their resulting post WILL contain divisions describing another post. 

So the challenge is to use these structural patterns to link individual posts into conversational threads.

### Approaches and Ideas :

[[Ideas#Operation R7 - Conversations]]

### Page Mods for Upload Listings 
*(uploads.xhtml)

We need to upgrade this view. Right now, it's just a simple table to list all the upload records.

1. I want to contain that dataTable inside a division similar to how view-conversation.xhtml works now.
2. I want the SQL that currently drives the dataTable itself to be modified to include one or two parameters.
	1. date range - (mm/yyyy to mm/yyyy)
	2. keywords - match in topic_title 
3. Above the element containing the dataTable, I want a non-scrolling header that includes a form type of element that provides a way to pass those parameters and a command button the execute.


### Page Mods for Conversation Listings 
*(view-conversations.xhtml)

When a user clicks on the <btn-dialogs> element  his browser is redirected to the conversations view which lists all the conversations according to Operation R7. 

I need to modify this view to contain the dataTable within a container element just like with the mod for the Uploads view, so that we have a non-scrolling area at the top to support the page headers and a query form offering these parameters to filter the conversations...

1. author - pull up any of the conversations where...
	any of the posts contain a match to post-author.
2. keyword - pull up any of the conversations where...
	any of the posts contain a match in post-content

If both parameter fields are populated, run the author query on the outside and the keyword query inside that.


### Weird Readings from Operation R7

I'm not getting the listing that I was hoping for... I'm sure the problem is in my specs. I'll use a few selected uploads to illustrate...

#### Upload: 732

|     |     |
| --- | --- |
| Topic ID | #346601 | 
| Topic Title | "Too Smart for Doge"|
| original post author | straightUp|
| original post id | 5251390 |

##### Post List  (btn-upload)
*First eight Records*

|     |     |
| --- | --- |
| 5251390 | straightUp |
| 5251403 | guzzimaestro|
| 5251417 | straightUp |
| 5251434 | Snake |
| 5251436 | Snake|
| 5251445 | guzzimaestro |
| 5251447| archie bunker|
| 5251459| Snake |


##### Post List (btn-dialog)

Conversation #1

|     |     |
| --- | --- |
| 5251403 | guzzimaestro|
| 5251417 | straightUp |


Conversation #2

|     |     |
| --- | --- |
| 5251403 | guzzimaestro|
| 5251459 | Snake |


The problem that I have here is that conversation #1 and conversation #2 are both in response to the original post and yet, neither of them contain the original post.

If we review the [[Operation R7#Sample Trace]]] We will see that the first post is included in every conversation that starts by quoting it.

It SHOULD be...

Conversation #1

|     |     |
| --- | --- |
| 5251390 | straightUp |
| 5251403 | guzzimaestro|
| 5251417 | straightUp |

Conversation #2

|     |     |
| --- | --- |
| 5251403 | guzzimaestro|
| 5251417 | straightUp |


#### Upload: 786

On Upload #786, the original post *IS* included. 

|     |     |
| --- | --- |
| Topic ID | #382815 | 
| Topic Title | "|   |
|---|
|Kimmel Needs To Be Taken Off The Air|
| original post author | tbutkovich|
| original post id | 5722253 |


##### Post List  (btn-upload)
*First eight Records*

|     |     |
| --- | --- |
| 5722253 | tbutkovich |
| 5722255 | RascalRiley|
| 5722256 | tbutkovich |
| 5722258 | tbutkovich |
| 5722264 | RascalRiley |
| 5722269 | tbutkovich |
| 5722280 | archie bunker |
| 5722284 | Anvil |

![[Pasted image 20260504151232.png]]
This is the report overlay generated by OPP userscript.

##### Post List (btn-dialog)

Conversation #1

|     |     |
| --- | --- |
| 5722253 | tbutkovich |
| 5722301 | Kevyn |
| 5722342 | Anvil |


Conversation #2

|     |     |
| --- | --- |
| 5722253 | tbutkovich |
| 5722301 | Kevyn |
| 5722615 | tbutkovich |

In THIS case, the original poster was NOT excluded from the data set for each conversation.

#### The Ending Problem:

I'm struggling to envision how a conversation is actually terminated or transitioned into another conversation.  

I *KNOW* I'm not the first to think about reconstructing conversations from shared discussion sites. I'd be interested in some collective intelligence from academic theories to best practices.


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
Currently, these loose images are being left out of the uploads. I would like to reincorporate these images into the conversations by moving the image links to 




<div style="text-align:center; overflow-wrap:break-word;">

	<br>
    <img src="https://static.onepoliticalplaza.com/upload/2016/10/19/714899-img_2483.jpg" alt="" style="max-width:100%; max-height:850px;">
    <br>
    
</div>    



<div style="text-align:center; overflow-wrap:break-word;">

	<br>
    <img src="https://static.onepoliticalplaza.com/upload/2016/10/19/836569-img_1911.jpg" alt="" style="max-width:100%; max-height:850px;">
    <br>

</div>    

