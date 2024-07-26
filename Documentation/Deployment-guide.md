# Deployment guide

Please note - this deployment guide assumes a proficient level of knowledge in deploying Power Apps solutions and managing SharePoint list permissions.

## Prerequisites

To begin, you will need:

- Power Apps and Power Automate (seeded licenses) enabled and rolled out across your organisation.
- Power Apps environment with a Dataverse database deployed (only required due to the solution using Environment Variables). You may use the default environment, however a production environment is recommended. If you do not have capacity to create a Dataverse database, you may need to use the default environment. 
- Service account (optional) to deploy the Power App and Flows, if you do not wish to create a service account then an ordinary user with Environment Maker permissions is adequate.
- Access to the Power Apps environment you wish to deploy to (Environment Maker) access.
- SharePoint site which will contain the lists - we recommend creating a new one for Prompt Pulse.
- Full Control access to the above site. 
- Regional settings set correctly on the SharePoint site for your timezone and locale (this is important for the scheduling to work correctly).
- The [latest release](https://github.com/pnp/prompt-pulse/releases/latest) of Prompt Pulse.

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

6. Create a list name 'Configuration'.
7. Create the following columns:

| Column Name    | Type |
| -------- | ------- | 
| Value  | Single line of text   

8. Create a list item in the above list with the following details:

Title: AppId

Value: Leave empty

## Step 2: Deploy Power Apps solution

1. Navigate to **Power Apps**.
2. Click on the **Solutions** tab.
3. Click on **Import**.
4. Upload the solution zip file and click **Next**.
5. Click **Sign in** next to each of the connectors and wait for the green tick.
6. On the **Environment Variables** pane, select the SharePoint site you created and the two lists.
7. Click **Import**.
8. A message should be displayed to say the solution has been imported successfully.
9. Obtain the **'ID'** for the Power App by clicking the elipsis next to the app.
10. Copy the **App ID** value and paste this into the **Value** column of the list item you created in the **Configuration** list.

## Step 3: Share the app/flows

1. Locate the app under **Apps**.
2. Share the app with all users who will use Prompt Pulse (you may want to add any administrators as co-owners so they can modify the app if you wish to).
3. Test the app by 'Playing' it.

You may also wish to share the 3 flows (**'Send Scheduled Prompt'**, **'Send Prompt'** and **'Like Prompt'**) with admins who may need to view the run history or edit them. You can find them under **Flows** in the **Power Apps** portal or in the **Power Automate** portal.

## Step 3: Add the app to Teams (Optional)

1. Navigate to **Power Apps** as the account you wish to install the app for and click 'Apps' in the left pane, you should see the Prompt Pulse Power App. You may need to select the correct Environment in which you deployed the solution from the Environment menu at the top.
2. Select the app and click 'Add to Teams' from the top menu bar.

At this point you have two options:

Add the app to Teams globally using policies in the Teams Admin Center OR sideload the app into the Teams client and install for the current logged in user only.

3. If you wish to sideload the app, click the 'Add to Teans' option in the dialog that appears. The Teams client will open (you may choose the web client or desktop) and the app will install for the current logged in user.
   
If you wish to roll the app out via policies, please refer to our general documentation on docs.microsoft.com for how to upload to the Teams Admin Center and deploy globally.

Prompt Pulse can be pre-installed for all users and optionally pinned to the rail if you wish. Or you can deploy the app and allow users to add it to their Teams as a tab.

## Step 4: Configure SharePoint list permissions

Before rolling out Prompt Pulse, it is neccessary to configure the permisions on the SharePoint lists to ensure users can only read/write/delete their own items.

1. Amend the permissions on the **Prompts** list (Advanced Settings) to **Read items that were created by the user** and **Create items and edit items that were created by the user**.
2. Amend the permissions on the **Users** list (Advanced Settings) to **Read items that were created by the user** and **Create items and edit items that were created by the user**.
3. Break permission inheritance on the **Prompts** and **Users** lists and add only users/groups that will use Prompt Pulse.
4. Ensure that admins who may need to read, edit and delete all list items have **Full Control**/**Owner** permissions on the SharePoint site.

### Deployment is now complete - enjoy using Prompt Pulse!
