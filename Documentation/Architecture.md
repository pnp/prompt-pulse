# Architecture

The below documentation details the architecture of the Prompt Pulse solution. Prompt Pulse is provided as a Power Apps solution file (**unmanaged**), this allows easy updates when new versions are released.

The Prompt Buddy integration is provided as a Dataverse for Teams solution file (**unmanaged**) and is deployed separately from the Prompt Pulse solution.

## Connectors

The following connectors are used in the Prompt Pulse solution:

- Viva Engage
- Office 365 Users
- SharePoint
- Microsoft Teams
- Office 365 Groups

When executed from the Power App (to retrieve Groups, Teams and Viva Engage communities) they will be executed in the context of the current user so they should only see ones they have access to. They will also only see group chats they are part of.

It's worth noting that Group Chats and Viva Engage Communities are pulled into the app via two flows.

The above connectors are also used in the Power Automate flows.

The following connectors are used in the Prompt Buddy Integration solution:

- Dataverse
- SharePoint

## Service Account

**It is highly recommended to deploy Prompt Pulse using a dedicated service account/M365 user** and share the app across the organization/with the required users. The UPN for this service account is stored in the configuration list.

Adaptive cards and Viva Engage notifications are sent using the account that deploys the Prompt Pulse Power Platform solution and configures the connections. Therefore by using a service account you can control the name of the user the notifications come from.

In addition, in order to post the cards/notifications, the user account sending them needs to be member of the Team, Group Chat, Viva Engage community, this is handled automatically (with the exception of Group Chats) through the app. **Bear this in mind because not using a service account would result in the user who deployed the solution being added to these locations**.

This service account can also be used to deploy the Prompt Buddy integration.

## Power App

The Prompt Pulse Power App is a canvas app using mostly **modern** controls where possible. Prompts that are shared/scheduled are stored in the **Prompts** SharePoint list.

The app uses the above connectors (and flows listed below) to interact with the SharePoint site/lists and retrieve the locations in which to share the prompts. 

The app can be customized if you wish, though it's worth noting that customizations will be lost if you attempt to update the solution in the future when new releases are available.

## Prompt Buddy Integration

Integration with Prompt Buddy is provided through a single flow (details can be found below) which adds Prompt Buddy prompts to the **Prompts** list and Prompt Pulse prompts to the relevant dataverse tables used by Prompt Buddy. This keeps both solutions in sync and ensures prompts can be accessed from both apps.

# Flows

Detailed information about the flows used in the solution can be found below:


## Send Prompt:

Triggers when an item in the Prompts list is created or modified using the 'When an item is created or modified' SharePoint trigger.

The following trigger condition is used to only trigger the flow when a prompt should be sent:

```@equals(triggerBody()?['Status']?['Value'], 'Send')```

The flow uses a switch statement to switch on the value of the 'ShareLocation' column and based on the value, the relevant actions are used to post the adaptive cards/message in the Viva Engage community.

The status of the list item is then updated to 'Sent'.

**Please note - The cards and message will be sent AS the user whom you deployed the solution as. You may change the connections if you wish to change the user.**

If the flow fails, a scope has been used to capture the error and the 'Status' column will be updated with the value **Failure**, you can then locate the flow and troubleshoot the error.


## Send Scheduled Prompt

Triggers on a recurrent schedule (default is 5 minutes). The flow retrieves all prompts that are scheduled to be sent by using the following odata query on the 'Get Items' action:

```ScheduledDateTime lt '@{outputs('Current_datetime')}' and Status eq 'Not Sent' and Scheduled eq 1```

The remainder of the flow is the same as the **Send Prompt** flow.

## Like Prompt

This flow is triggered using the 'When someone responds to an adaptive card' trigger and is executed when the user clicks the 'Like Prompt' button in the adaptive card.

The user responding to the adaptive card is retrieved from the 'Users' list and the prompt list item is retrieved from the 'Prompts' list.

The flow then increments the value of the 'Likes' column by 1 and adds the prompt to an array. The value of the 'LikedPrompts' column in the Users list is checked to see whether it already contains the current prompt id. 

If this value does not exist, it is appended to an array and the value of the 'LikedPrompts' column in the Users list is updated.

## Get Group Chats

This flow is triggered in the Power App when it is opened. It uses the Microsoft Teams connector to perform the following high-level steps:

- Get group chats the user is part of.
- Loop through group chats.
- List members.
- Check to see if the Prompt Pulse service account is a member of the chat AND that the chat has a name.
- Return only the chats that contain the service account.

This flow is required because prompt adaptive cards will not send if the service account is not a member of the chat. At the time of writing there is no workaround for this other than for the user to add the service account to the chat manually.

## Get Engage Communities

This flow is triggered in the Power App when it is opened. It uses the Viva Engage and Office 365 Groups connectors to perform the following high-level steps:

- Get communities the user is a member of.
- Loop through communities.
- Get the M365 group for the community by filtering on the display name (may retrieve multiple groups).
- Loop through the returned groups checking for ones where 'creationOptions' contains 'YammerProvisioning' (Viva Engage backed group).
- Return the communities along with the groupids.

This flow is required because the native Viva Engage connector does not return the M365 group id for the community. The group id is needed to add the service account to the group through the app when sending a prompt.

## Parse Prompt CSV

This flow is triggered from the Power App when the user clicks the **Upload** button. It parses the content of the csv file and returns it as JSON.

## Prompt Pulse Sync

This flow is triggered on a recurrent scheduled inside the Dataverse for Teams environment in which the Prompt Buddy app resides. It performs the following high-level steps:

- Get Prompt Buddy Microsoft 365 Copilot prompts.
- Loop through prompts and create a list item in the Prompts list, set the value of the 'SyncedToPulse' column to true and set the 'BuddyId' column to the unique id of the dataverse record.
- Loop through prompts in the Prompts list and create the relevant records in the Prompt Buddy dataverse tables, set the 'SyncedToBuddy' column to true.

## Data Source

As detailed in the [Overview](Overview.md) documentation, there are 3 SharePoint lists used in prompt pulse. Please see the details of each list below and what each column is used for:

### Prompts list

| Column Name    | Type | Used for |
| -------- | ------- | ------- | 
| Prompt  | Multiple lines of text    | Storing the prompt text.
| Likes | Number     | Number of likes the prompt list item has.
| ShareLocation | Choice     | Location to share the prompt to.
| Users    | Person or Group (Allow multiple selections)  | Users to share the prompt to.
| TeamId    | Single line of text  | Id of the Team selected in the app to share the prompt to.
| ChannelIds    | Multiple lines of text  | Ids of the Channels selected in the app to share the prompt to.
| GroupChatIds    | Multiple lines of text  | Ids of the Group Chats selected in the app to share the prompt to.
| CommunityIds    | Multiple lines of text  | Ids of the Communities selected in the app (when sharing to Viva Engage).
| MessageId    | Single line of text  | Unique id of the adaptive card message - used so we can refer back to it in Power Automate.
| Scheduled    | Yes/No  | Whether or not the prompt has been scheduled.
| ScheduledDateTime    | Date and time  | Date/Time to send the prompt.
| Status    | Choice  | Status of the prompt.
| SyncedToBuddy    | Yes/No  | Whether or not the prompt has been synced to Prompt Buddy (only used if the Prompt Buddy integration is deployed).
| SyncedToPulse    | Yes/No  | Where or not the prompt has been synced from Prompt Buddy to Pulse (only used if the Prompt Buddy integration is deployed).
| BuddyId    | Single line of text  | Unique id of the prompt in the Prompt Buddy dataverse prompts table.


### Users list

| Column Name    | Type | Values |
| -------- | ------- | ------- | 
| User  | Person or Group  | The user that has opened Prompt Pulse.
| LikedPrompts | Lookup     | The prompts that the user has liked.
| TutorialComplete | Yes/No     | Whether or not the user has completed the tutorial.

### Configuration list

| Column Name    | Type | Used for |
| -------- | ------- | ------- | 
| Value  | Single line of text | The configuration value.


