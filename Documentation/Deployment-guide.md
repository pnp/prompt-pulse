# Deployment guide

Please note - this deployment guide assumes a proficient level of knowledge in deploying Power Apps solutions and managing SharePoint list permissions.

## Video Walkthrough 

https://github.com/user-attachments/assets/b334e743-8c7b-4371-b5fd-4cf1f6e739dd

## Prerequisites

To begin, you will need:

- Power Apps and Power Automate (seeded licenses) enabled and rolled out across your organisation.
- Power Apps environment with a Dataverse database deployed (only required due to the solution using Environment Variables). You may use the default environment, however a production environment is recommended. If you do not have capacity to create a Dataverse database, you may need to use the default environment. 
- Service account (**highly recommended**) to deploy the Power App and Flows. This should be a normal M365 user with a license that includes Power Apps, SharePoint, Viva Engage, Power Automate and Outlook. The account can have MFA enabled and you may choose the display name to suit your organization.
- Access for the service account to the Power Apps environment you wish to deploy to (Environment Maker) access.
- SharePoint site which will contain the lists - we recommend creating a new one for Prompt Pulse.
- Full Control access to the above site. 
- Regional settings set correctly on the SharePoint site for your timezone and locale (this is important for the scheduling to work correctly).
- The [latest release](https://github.com/pnp/prompt-pulse/releases/latest) of Prompt Pulse.

### Prompt Buddy Integration

If you wish to integrate with Prompt Buddy, it must be deployed and set up in a Teams Team before deploying Prompt Pulse.

Once Prompt Pulse is deployed, follow 'Step 5' below to deploy the Prompt Buddy integration, this requires deployment of an additional Power Apps solution.

Note - You must create ALL of the columns below even if you are not deploying the integration.

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
| ChannelIds    | Multiple lines of text  |
| GroupChatIds    | Multiple lines of text  |
| CommunityIds    | Multiple lines of text  |
| Scheduled    | Yes/No  | Default value = No |
| ScheduledDateTime    | Date and time  | Include time |
| Status    | Choice  | Not Sent, Send, Sent, Failed | Default value = Not Sent
| Source    | Choice  | Pulse, Buddy
| SyncedToBuddy    | Yes/No  | Default value = No
| SyncedToPulse    | Yes/No  | Default value = No
| BuddyId    | Single line of text  |
| App | Choice | Copilot, Word, PowerPoint, Planner, Excel, Outlook, Teams, Loop, Forms, Whiteboard, Designer, SharePoint, Stream, OneNote, OneDrive |

4. Create a list named 'Users'.
5. Create the following columns:

| Column Name    | Type | Values |
| -------- | ------- | ------- | 
| User  | Person or Group    |
| LikedPrompts | Lookup     | Source list = 'Prompts', Select a column = 'ID' |
| TutorialComplete | Yes/No     | Default value = No |

6. Create a list named 'Reporting'.
7. Create the following columns:

| Column Name    | Type |
| -------- | ------- | 
| PromptsSent  | Number
| AppLaunches  | Number
| PromptsCopied  | Number
| PromptsLiked  | Number

8. Create a single item in the 'Reporting' list with the value of each column set to 0.

9. Create a list named 'Configuration'.
10. Create the following columns:

| Column Name    | Type |
| -------- | ------- | 
| Value  | Single line of text  

11. Create a list item in the above list with the following details:

Title: AppId

Value: Leave empty

12. Create a second list item in the above list with the following details:

Title: ServiceAccountUPN

Value: UPN (Email) of your service account

13. Create a third list item in the above list with the following details:

Title: PromptBuddyInstalled

Value: 'true' (if you wish to integrate with Prompt Buddy) or 'false'.

14. Create a fourth list item in the above list with the following details:

Title: ShowShareEngage

Value: 'true' (if you wish to allow sharing to Viva Engage) or 'false'.

15. Create a fifth list item in the above list with the following details:

Title: ShowShareUsers

Value: 'true' (if you wish to allow sharing to users) or 'false'.

## Step 2: Deploy Power Apps solution

1. Navigate to **Power Apps** as the service account.
2. Click on the **Solutions** tab.
3. Click on **Import**.
4. Upload the solution zip file and click **Next**.
5. Click **Sign in** next to each of the connectors and wait for the green tick.
6. On the **Environment Variables** pane, select the SharePoint site you created and the two lists.
7. Click **Import**.
8. A message should be displayed to say the solution has been imported successfully.
9. Obtain the **'ID'** for the Power App by clicking the elipsis next to the app.
10. Copy the **App ID** value and paste this into the **Value** column of the list item you created in the **Configuration** list.

## Step 3: Turn on the flows

On occasion the flows are turned off on import and they need to be turned on. Follow the steps below to turn on each flow.

1. Navigate to **Power Apps** as the service account.
2. Click on the **Flows** tab.
3. Click on the **Send Prompt** flow. 
4. Click the 'Turn on' option on the top menu.
5. Repeat this process for the following flows - **'Send Scheduled Prompt'**, **'Like Prompt'**

## Step 4: Share the app/flows

1. Locate the app under **Apps**.
2. Share the app with all users who will use Prompt Pulse (you may want to add any administrators as co-owners so they can modify the app if you wish to).
3. Test the app by 'Playing' it.

You may also wish to share the 5 flows (**'Send Scheduled Prompt'**, **'Send Prompt'**, **'Like Prompt'**, **'Get Group Chats'** and **'Get Engage Communities'**) with admins who may need to view the run history or edit them. You can find them under **Flows** in the **Power Apps** portal or in the **Power Automate** portal.

## Step 5: Add the app to Teams (Optional)

1. Navigate to **Power Apps** as the account you wish to install the app for and click 'Apps' in the left pane, you should see the Prompt Pulse Power App. You may need to select the correct Environment in which you deployed the solution from the Environment menu at the top.
2. Select the app and click 'Add to Teams' from the top menu bar.

At this point you have two options:

Add the app to Teams globally using policies in the Teams Admin Center OR sideload the app into the Teams client and install for the current logged in user only.

3. If you wish to sideload the app, click the 'Add to Teans' option in the dialog that appears. The Teams client will open (you may choose the web client or desktop) and the app will install for the current logged in user.
   
If you wish to roll the app out via policies, please refer to our general documentation on docs.microsoft.com for how to upload to the Teams Admin Center and deploy globally.

Prompt Pulse can be pre-installed for all users and optionally pinned to the rail if you wish. Or you can deploy the app and allow users to add it to their Teams as a tab.

## Step 6: Configure SharePoint list permissions

Before rolling out Prompt Pulse, it is neccessary to configure the permisions on the SharePoint lists to ensure users can only read/write/delete their own items.

1. Amend the permissions on the **Prompts** list (Advanced Settings) to **Create items and edit items that were created by the user**.
2. Amend the permissions on the **Users** list (Advanced Settings) to **Read items that were created by the user** and **Create items and edit items that were created by the user**.
3. Break permission inheritance on the **Prompts** and **Users** lists and add only users/groups that will use Prompt Pulse.
4. Break permission inheritance on the **Configuration** and **Reporting** lists and ensure that only admins have write access to these lists, users should have read access.
4. Ensure that admins who may need to read, edit and delete all list items have **Full Control**/**Owner** permissions on the SharePoint site.

## Step 7: Deploy Prompt Buddy Integration

These steps deploy the integration between Prompt Pulse and Prompt Buddy. This integration is provided in the form of a single Flow which is responsible for syncronizing items from the Prompt Buddy dataverse tables to the Prompts SharePoint list and visa versa.

You may use any account to deploy this flow (it will need to be a member of the Teams Team in which Buddy is deployed), however it is worth noting that any prompts synced to Buddy will be created as the user who deploys this flow. Therefore you may wish to use the service account.

This flow runs on a recurrent schedule and by default runs every hour, feel free to edit the flow and update the interval as needed.

1. Launch Microsoft Teams. 
2. Open the Power Apps app in Teams.
3. Navigate to the **Build** tab.
4. Select the environment in the left pane in which Prompt Buddy is deployed.
5. Click **See all** in the list items created in the environment.
6. Click **Import > Import solution** from the menu bar.
7. Click **Browse** and locate the **PromptPulseBuddyIntegration_1.0.0.0.zip** solution zip file (if there is a newer version please use that).
8. Click **Next**.
9. Click **Next**.
10. Click **Sign in** next to each of the connectors and wait for the green tick.
11. Click **Next**.
12. On the **Environment Variables** pane, select the SharePoint site you created and the **Prompts** List.
13. Click **Import**.
14. A message should be displayed to say the solution has been imported successfully.
15. Locate the **Prompt Pulse Sync** flow and click on it.
16. Click **Run** from the menu bar and click **Run flow**.
18. Verify the run completes without errors.
19. The integration is now deployed and the flow will automatically sync the prompts in both solutions.

### Deployment is now complete - enjoy using Prompt Pulse!

## Upgrading from V2

When upgrading from V2 to V3, in addition to importing the V3 solution, be sure to create the new 'App' column in the 'Prompts' list.

See Step 1 - Create SharePoint lists.

## Viva Engage Rich Text Posts/Announcements

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-vivaengage-post-screenshot.png?raw=true" alt="Prompt Pulse Viva Engage Announcement Screenshot"><br/>

When prompts are shared to Viva Engage, the messages can be posted as announcements with rich text support. 

For this to work, the Service Account used to deploy and set up Prompt Pulse must be an admin of the selected community.

This is a manual step as Prompt Pulse will not automatically set this up. Please therefore make the Service Account an admin of those communities that you will mainly share with.

**Note - The Prompt Pulse app displays all communities that the current user is a member of regardless of the Service Account permissions.**

If the Service Account is not an admin, there is a fallback to use a plain text message.

