# Architecture

The below documentation details the architecture of the Prompt Pulse solution. Prompt Pulse is provided as a Power Apps solution file (**unmanaged**), this allows easy updates when new versions are released.

## Connectors

The following connectors are used in the Prompt Pulse solution:

- Viva Engage
- Office 365 Users
- SharePoint
- Microsoft Teams

When executed from the Power App (to retrieve Groups, Teams and Viva Engage communities) they will be executed in the context of the current user so they should only see ones they have access to. They will also only see group chats they are part of.

The above connectors are also used in the Power Automate flows.

## Power App

The Prompt Pulse Power App is a canvas app using mostly **modern** controls where possible. Prompts that are shared/scheduled are stored in the **Prompts** SharePoint list.

The app uses the above connectors to interact with the SharePoint site/lists and retrieve the locations in which to share the prompts.

The app can be customized if you wish, though it's worth noting that customizations will be lost if you attempt to update the solution in the future when new releases are available.

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

## Data Source

As detailed in the [Overview](Documentation/Overview.md) documentation, there are 3 SharePoint lists used in prompt pulse. Please see the details of each list below and what each column is used for:

### Prompts list

| Column Name    | Type | Used for |
| -------- | ------- | ------- | 
| Prompt  | Multiple lines of text    | Storing the prompt text.
| Likes | Number     | Number of likes the prompt list item has.
| ShareLocation | Choice     | Location to share the prompt to.
| Users    | Person or Group (Allow multiple selections)  | Users to share the prompt to.
| TeamId    | Single line of text  | Id of the Team selected in the app to share the prompt to.
| ChannelId    | Single line of text  | Id of the Channel selected in the app to share the prompt to.
| GroupId    | Single line of text  | Id of the Group selected in the app (when sharing to Viva Engage).
| GroupChatId    | Single line of text  | Id of the Group Chat selected in the app to share the prompt to.
| MessageId    | Single line of text  | Unique id of the adaptive card message - used so we can refer back to it in Power Automate.
| Scheduled    | Yes/No  | Whether or not the prompt has been scheduled.
| ScheduledDateTime    | Date and time  | Date/Time to send the prompt.
| Status    | Choice  | Status of the prompt.

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


