# Deployment guide

## Prerequisites

To begin, you will need:

- Power Apps and Power Automate (seeded licenses) enabled and rolled out across your organisation.
- Power Apps environment with a Dataverse database deployed (only required due to the solution using Environment Variables). You may use the default environment, however a production environment is recommended. If you do not have capacity to create a Dataverse database, you may need to use the default environment. 
- Service account (optional) to deploy the Power App and Flows, if you do not wish to create a service account then an ordinary user with Environment Maker permissions is adequate.
- Access to the Power Apps environment you wish to deploy to (Environment Maker) access.
- SharePoint site which will contain the lists.
- Full Control access to the above site. 

## Step 1: Create SharePoint Lists

  1. Navigate to the SharePoint site.
  2. Create a list named 'Prompts'.
  3. Create the following columns:

| Column Name    | Type | Values |
| -------- | ------- | ------- | 
| Prompt  | Multiple lines of text    |
| Likes | Number     |
| ShareLocation | Choice     | Community, Team, Group Chat, Users |
| Users    | Person or Group (Allow multiple selections)  |
| TeamId    | Single line of text  |
| ChannelId    | Single line of text  |
| GroupId    | Single line of text  |
| GroupChatId    | Single line of text  |
| MessageId    | Single line of text  |
| Scheduled    | Yes/No  | Default value = No |
| ScheduledDateTime    | Date and time  | Include time |
| Status    | Choice  | Not Sent, Send, Sent, Failed | Default value = Not Sent

4. Create a list named 'Users'.
5. Create the following columns:

| Column Name    | Type | Values |
| -------- | ------- | ------- | 
| User  | Person or Group    |
| LikedPrompts | Lookup     | Source list = 'Prompts', Select a column = 'ID' |
| TutorialComplete | Yes/No     | Default value = No |

## Step 2: Deploy Power Apps solution

