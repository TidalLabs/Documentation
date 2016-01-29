Campaigns 2.0
=============

Definitions:
------------

The *Campaign* stores relatively permanent information about the campaign such as name, start and end dates, image and the owning channel. 

A *Campaign Activation* is a relationship between a single user and a single campaign workflow.  It stores data specific to that relationship and that user's progress through the workflow.  

The *User* stores relatively permanent information about the user such as name and address. A user may be in multiple workflows. A user may be in the same workflow more than once. A workflow will nearly always have multiple users.  

A *Campaign Workflow* stores a series of grouped Workflow Steps.  Each step will have a name, and a series of generic or custom methods that are executed to bring the campaign to life. See detailed "Workflow Step Definition" below. 

Workflows and steps can vary a lot, but in practice most workflows will be copies of a previous one.  Creation of new step types is likely to always be a consulting/engineer task.  In the short term assembling steps into a json encoded  workflow will be too, but once settled a UI that allows humans to do it should be cost effective.

*Campaign Pages* are single html pages that can be skinned and customized to suit the project but generally have limited functionality.  They are intended to be a landing page that gives information, or a form that handles it's own submission.  They can be used for marketing copy, image/asset download, address detail entry/update, terms and conditions acceptance.  Their viewing or successful submitting can be made compulsory by making it a workflow step.  The participant UI will not be dashboard based, but will be based on single pages and templated emails.

The admin interface will be dashboard based, but this document does not try to describe that UI. The details of how that UI works are still open, but the information presented or required at each step will largely be set by how the API works.

Lifecycle of a campaign:
------------------------

* Create channel
* Create campaign
* Create workflow
* Start user on a workflow
* User receives emails, performs tasks, and gets directed to one page forms/descriptions
* User receives reminder email if stalled
* User completes/exits/stalls in workflow
* Reporting data is pulled
* Campaign ends
* Campaign is archived and workflow steps are no longer checked for changes

API Endpoints:
--------------

*Note: the abreviations below chid, cid, wid, uid, aid, pid, cpid stand for channel, campaign, workflow, user, activation, post and campaign page IDs respectively)*

    /v1/campaign/create POST
Create a campaign. Requires valid JSON definition of the campaign.  On success a database record will be created and you'll get a campaign id.
(Slug, name, tags, description, start date, end date, image)

Supports an admin dash feature that creates new campaigns

    /v1/campaign/<cid/update POST
Update a campaign. Requires valid JSON definition of the campaign.  On success the database record will be replaced.
(Slug, name, tags, description, start date, end date, image)

Supports an admin dash feature that edits campaigns

    /v1/workflow/create POST
Create a workflow. Requires a valid JSON definition of the workflow.  On success a database record will be created and you'll get a workflow id.

In the short term supports engineering defined workflow.  Later supports a complex UI that allows steps to be assembled.

    /v1/workflow/<chid> GET 
Get a list of workflows for a channel. 
Supports dash navigation

    /v1/workflow/<cid> GET 
Get the single workflow for a campaign. 

Supports dash viewing of workflow spec

    /v1/workflow/<uid> GET
Get a list of workflows for a user. 

Supports dash navigation/reporting

    /v1/workflow/<uid>/<cid> GET
Get a list of workflows for a user on a campaign.

Supports dash navigation/reporting
 
    /v1/workflow/<wid>/steps GET
Get a list of steps in a workflow.. On success an array of step names eg ["invited", "post published", "post accepted", "post rejected"]

Supports dash display of columns that show what status each user is in

    /v1/workflow/<wid>/start/<uid> POST
Start a user on a workflow. On success a database record will be created and you'll get a campaign activation id.

Supports dash feature to invite a user, or user facing page that allows a user to self-apply

    /v1/workflow/<wid>/check/<uid> GET or POST
Read a user's current status(es). On success you'll get a JSON response containing campaign activation id, name of current step and a brief user presentable description of what their next action is. 

Eg [{"activation": "abcdef000000000123456789abcdef000000000123456789", "name": "Invited", "nextAction": "Click 'accept' in the email you received", "lastChecked":"2015-05-14T18:25:43.511Z"}]

Note a user may have more than one activation on the current workflow so the result could be an array with more than one element.

A POST request triggers a check to see if the user can move to the next step.  It's up to the UI to decide when potentially slow checks should be made and when a GET request and a cached answer is acceptable. Eg for reporting purposes, a lastChecked date in the last hour may be acceptable but in a user facing interface to check if tracking pixel is acceptable a new check could be made every time.    

Supports dash displays for reporting

    /v1/workflow/<wid>/check/<limit>/<page> GET
As above, but for all users.  

Supports dash displays for reporting. Paginated

    /v1/workflow/check POST
Update all user statuses 

Supports cron job to keep things progressing.  Interval can be tuned over time. 

    /v1/campaign/<cid>/countUsers GET
How many active or successful users are there.  How many users started? In order for there to be a different there must be at least one state in the workflow that calls 

Supports counting for limited participant campaigns so as well as the total number of users (that will match report rows) show the total that are still in progress or successfully completed.  

    /v1/campaign/<cid>/report GET
Get summary reporting data on campaign (posts (submitted, rejected, pending, published), contributors, shares (by network), pageviews, sphere of influence, top posts (date, title, url, votes, views, shares), reach (blog, twitter)

Supports replacing current display at /manage/do/page/stats/campaigns but can use published posts rather than all submitted posts

    /v1/campaign/<cid>/report/<uid> GET
As above but reporting data on campaign for a single user (may contain multiple posts)

Supports detailed view report page

    /v1/campaign/<cid>/users/<limit>/<page> GET
Fetch list of users in campaign with summary user data and workflow status(es)

Supports dashboard moderation interface (paginated)

    /v1/campaign/<cid>/moderate/<uid> POST
Accept/reject a user in this campaign

Supports dashboard moderation interface

    /v1/campaign/<cid>/posts/<limit>/<page> GET
Fetch list of posts in campaign with summary user and post data and workflow status

Supports dashboard moderation interface (paginated)

    /v1/campaign/<cid>/moderate/<pid> POST
Accept/reject a post in this campaign

Supports dashboard moderation interface (paginated)

    /v1/campaign/pixel/<uid> GET/POST
Get a tracking pixel for each activation for this user on this campaign (reuse current format.  If that stores a record it should be a POST.  If it generates a hash that can be decoded is it should be a GET).  No new endpoint to record pixel loads required.

Supports giving users a tracking pixel code fragment in email or on a web page

    /v1/campaignpage/create POST
Store a URL and a blob of HTML and template macros as a campaign page. Receive a campaign page id. Users will be directed to these pages by email with a tracked url. The visit will be logged in the campaign activation. Macros can output data from the current user, campaign, or workflow. They can store information into the current user or activation. That way they can be customized to some degree without code. 

They will need to be created or reviewed by tidal admins as making them both secure and user editable is difficult or impossible.  Allowing users to edit blocks of text that will be output on them gives access for copy changes, without irreconcilable conflict with security needs.

Supports admin interface to upload engineering or client created pages

    /v1/campaignpage/<cpid> POST
Store a replacement blob of HTML as a campaign page. Note that page can be in use in multiple workflows so you need to be careful with updates.  The idea is that once a client has approved a page that (for example) asks for a shipping address and stores it that page can be reused in future workflows.

Supports admin interface to edit pages

    /v1/campaignpage/<cpid> GET
Fetch a campaign page's HTML.  

Supports code that deploys pages where they can be publicly viewed and submitted and preview feature in adming dash. Except for preview, it's probably not a good idea to have a system that loads from the DB on every view.  Thus there is no method to receive a form submit to a campaign page in this API

Workflow definition:
--------------------

This document is mostly about workflows and their steps, so let's have an example.

*this definition will be stored as json, but is presented with less punctuation here for readability*

A simple workflow to prompt a user to write a post could be defined as:

	name: start
	action: sendEmail('inviteTemplate')
	next: 'invited'
	delayed: 24, sendEmail('inviteTemplate')

	name: invited
	action: // no action means nothing happens on entry except for check
	check: hasSubmittedPage('acceptTandC'), waitingForPost
	delayed: 72, sendEmail('reminderTemplate)

	name: writing
	action: 
	check: hasSubmittedPage('submitPost'), moderation
	delayed: 72, sendEmail('reminderTemplate)
	delayed:  '2015-05-14T18:25:43.511Z', sendEmail('deadlineCloseTemplate')
	expires: 144, droppedOut
			
	name: moderation
	action: sendEmail('staffModerate')
	check: isPostAccepted(), published 
	check: isPostRejected(), rejected
	delayed: 24, sendEmail('YO-NICK')

	name: published
	action: sendEmail('congratulationsTemplate')
	postCheck: hasBeenPaid(), paid
	delayed: 72, sendEmail('show me the money')

	name: paid
	action: sendEmail('paidTemplate')
	check: // no check and no postCheck means this is a termination state
	delayed:

	name: rejected
	action: sendEmail('sorryTemplate')
	check: // no check and no postCheck means this is a termination state
	delayed:

	name: droppedOut
	action: removeParticipant()
	check: // no check means this is a termination state

Workflow Step Definition:
-------------------------

Each step has a name.  The names are used for human and reporting use, but must be unique with a workflow as they are also used for flow control.

Each step can have a description.  The description is for user presentation and should tell them what needs to happen for them to progress.

Steps can have a next.  That's the name of a step that the user will unconditionally progress to after then action in this step has been run.

Steps can have an expires.  That's a delay in hours and the name of a step that the user will progress to if the campaign ends while the user is in this step, or if that many hours pass before the user exits the step.  Checks, actions and delayeds will not be executed after the campaign end time.  Expires clauses will be checked once more after campaign end.

Steps could have some or all of action, check, postCheck and delayed that call methods.  They are all similar and in theory could call shared methods.  None are required and all could be present more than once. 

An action is a method that gets run once when a user enters that step.  The named method will be called with any specified parameters and with the campaign activation and workflow step.  Return value is not checked, so if it could fail use a check clause to verify and possibly retry.  Note that you don't know in advance how often check will be called so be careful with retry attempts.

A check is a method that gets run every time the user's updated status is requested and has the name of a step to progress to. The method will be called with any specified parameters and with the campaign activation and workflow step. It would generally make no changes or at least not take any action that is problematic to repeat.  It must return true or false.  It could be checking if a user has a postal address stored, or has a tracking pixel at an url, or has a hashtag in a tweet at an url, or potentially more complex tests. It might be checking that staff action has happened, such as moderation or payment.

If a check method returns true the user progresses to the named step. If there are multiple checks, they will be tried in order and the first one to return true will shortcut execution.

Checks might be called frequently, so the should not be expensive to run.

A postCheck clause is exactly like a check clause except that unlike checks it will be checked after the campaign ends (as well as during the campaign).  After a reasonable delay (a month) the campaign is archived and postChecks stop getting run too.

A delayed clause has a delay in hours or a timestamp then a method. For the hours version, if the user has not exited that state when that many hours have elapsed the method will be called once.  For the timestamp version, if the user has not exited that state when that that timestamp arrives the method will be called once.  

Note that delays will not be precisely observed.  Depending on implementation they may be checked minutes or hours after the deadline, so actions or templates should be written so they make sense in that context.  "You have less than 24 hours left to …" would be good.  "You have exactly 24 hours left to …" would be bad.

Predefined action, check, delayed and expires methods: 
------------------------------------------------------

Methods are likely to get added for client requests but to support common workflows we need

	sendEmail($activation, $workflowStep, $templateName)
Gets user email address via $activation.  Sends email using $templateName.  May use user record and metadata from the workflow or workflowstep or campaign to personalize template.

	hasSubmittedPage($activation, $workflowStep, $campaignPageId)
Checks if the user has submitted the campaign page successfully.  That means they've visited, filled any required fields, pressed submit and it's passed validation. Useful for terms and conditions, address complete, paypal details, submitting post, submitting social media post urls.

	hasVisitedPage($activation, $workflowStep, $campaignPageId)
Checks if the user has visited the campaign page from an email prompt.  Useful for instructions, image/asset download, tracking pixel download.

	hasTrackingPixel($activation, $workflowStep) 
From the post id in the workflow metadata check if the tracking pixel is in the page at that url

	hasHashTag($activation, $workflowStep) 
From the url in the workflow metadata check if the hashtag is in the social post

	isPostAccepted($activation, $workflowStep) and isPostRejected($activation, $workflowStep)
From the post id in the workflow metadata check its moderation status for the campaign this activation belongs to

	hasBeenPaid($activation, $workflowStep)
Has tidal recorded this user as paid

	hasBeenShipped($activation, $workflowStep)
Has tidal recorded this user as being shipped sample/prize/product

	hasBeenDelivered($activation, $workflowStep)
Does the tracking number show the sample/prize/product was delivered

	removeParticipant($activation, $workflowStep)
For limited user campaigns, call to flag dropped out or rejected users so they do not take up a slot that could be reassigned.

	hasVacancy($activation, $workflowStep)
For campaigns with a set number of participants, check if all slots are taken and reject later applicants

	hasBadge($activation, $workflowStep)
Checks if user has a specific badge, perhaps for priority treatment … or speedy removal.

TL;DR: 
------
You are kidding right?
