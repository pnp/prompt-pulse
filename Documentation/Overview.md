# Overview

Prompt Pulse consists of the following components:

- Power App
- 6 x Power Automate flows.
- 4 x SharePoint lists for data storage

Separate Dataverse for Teams solution for integration with Prompt Buddy containing 1 Power Automate flow.

For more details on what each of these does please check out the [Architecture](Architecture.md) documentation.

## The App

The Prompt Pulse Power App provides users with an easy to use interface to share prompts share prompts directly, schedule the sharing of prompts and view prompts (all/recent/liked).

The app is fully responsive, works on mobile devices 📱 and supports dark mode! 🕶️ (please note that completion of the initial tutorial needs to be carried out on a desktop device).

To get the most out of Prompt Pulse, we recommend using the app in Microsoft Teams either as a pinned app and/or add the app into individual teams as a tab. 

Prompt Pulse integrates with Prompt Buddy, follow the [Deployment guide](Deployment-guide.md) to deploy the integration.

When a user launches Prompt Pulse for the first time, they will be taken through a tutorial explaining how to use the app. 

## Using the app

### Sharing prompts

From the main screen, the process for sharing a prompt is as follows:

- Enter a **title** and the **prompt** in the boxes provided.
- Select the 'app' that the prompt is designed to be used with.
- Choose locations to share the prompt to by using the radio buttons - 

    Viva Engage Communities

    Microsoft Teams Channels

    Group Chats

    Individual Users

- Send or schedule the send by using the buttons. The **Send** button sends the prompt instantly via a Power Automate flow which is trigger when an item is added/modified in the Prompts list (based on a certain criteria). The **Schedule** option will open a dialog allowing you to specify a date and time on which to send the prompt.

- Once sent, users will receive a notification in the format of an adaptive card in the chosen location.

**Note - Only group chats with a name AND the Prompt Pulse service account/user added as a participant will display in the app. Users will need to add this user manually for the group chat to be visible in the app.**

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-share-screenshot.png?raw=true" alt="Prompt Pulse Share Screenshot"><br/>

**Note - Viva Engage and Users radio buttons are only enabled if the 'ShowShareEngage' and 'ShowShareUsers' list items in the Configuration list are set to true.**

### Viewing/using prompts

From the main screen, users can view the latest 20 prompts. To use a prompt simply click the prompt **'tile'** and the prompt text will be copied to your clipboard. 

You can then paste the prompt into Microsoft 365 Copilot/the app that the prompt was designed to be used in.

The **All Prompts** tab allows you to view all prompts that have been shared, text can also be copied by clicking the tile. If the integration with Prompt Buddy is deployed, an icon will be visible showing the 'source' of the prompt (Pulse or Buddy).

The adaptive card also has a button which will take a user straight to the **All Prompts** screen. Please note - this will open the app as a 'personal' app due to restrictions with deeplinking, if the user does not have the app installed they will be prompted to install it.

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-viewliking-screenshot.png?raw=true" alt="Prompt Pulse View/Like Screenshot"><br/>

### Liking prompts

To like/unlike a prompt, simply use the thumbs up icon. This will create a personalized collection of **liked** prompts which can be viewed on the **All Prompts** screen.

Prompts can also be liked from the adaptive card by clicking the **Like Prompt** button. 

### Scheduling

Prompts can be scheduled to be sent at specific dates/times using the **Schedule** option. To view all scheduled prompts use the **Scheduled** tab. This table shows all the prompts that have been scheduled to be sent organised by date.

From this screen, dates/times for scheduled prompts can be updated using the edit icon and prompts can be deleted using the trash icon. 

The **Send Now** option sends the prompt straight away and removes it from the scheduled list.

**Note - this view shows the current users' scheduled prompts only.**

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-scheduled-screenshot.png?raw=true" alt="Prompt Pulse My Scheduled Prompts Screenshot"><br/>

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-scheduledialog-screenshot.png?raw=true" alt="Prompt Pulse Schedule Prompt Dialog Screenshot"><br/>


### Importing

Prompts can be imported into Prompt Pulse in bulk, enabling you to easily schedule and send prompts right away, encouraging the use of Copilot from the get go.

Prompts can be imported from CSV or Prompt Buddy.

A csv file has been provided [Prompts.csv](../Prompts.csv) with pre-built prompts for you to import. Feel free to edit the csv and add/remove as many prompts as you like (you will also be able to remove when importing). The pre built csv will be update periodically with new prompts so please keep an eye out for changes.

Note that we've provided an additional format for the sample prompts at [prompts.json](../prompts.json). This is the format used for importing INTO Prompt Buddy. If you want these sample prompts in both places, it may be easier to start by importing this list to Prompt Buddy, then using the import from Prompt Buddy steps below.

**Note - at the time of writing, prompts that are imported from CSV and/or Prompt Buddy will have the 'App' set to 'Copilot', functionality to select the appropriate app for each prompt will be released in the future.**

#### Import from CSV

To import prompts from CSV, follow the steps below:

1. Navigate to the import screen by clicking the **Import Prompts** tab.
2. Click the **CSV file** button.
3. Use the attachment control to upload the csv file (only csv files are supported AND the csv file must be in the same format as the provided sample).
4. Click **Upload**.
5. The table will update to show all the prompts from the csv.
6. For each row/prompt, choose whether to schedule or send the prompt (**send** will send the prompt straight away when imported). Also choose the locations to share the prompt to.
7. Once ready, click **Import**, this will import the prompts into the SharePoint list and send/schedule them as per your selections.

**To delete prompts that you do not wish to import, use the trash icon.**

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-import-screenshot.png?raw=true" alt="Prompt Pulse Import Prompts Screenshot"><br/>

#### Import from Prompt Buddy

To import prompts from Prompt Buddy, follow the steps below:

1. Navigate to the import screen by clicking the **Import Prompts** tab.
2. Click the **Prompt Buddy** button.
3. Use the table at the top to add prompts to import by clicking the **+** icon. This table shows Prompt Buddy prompts that have not yet been imported into Prompt Pulse.
4. Prompts added will appear in the **Selected Prompts** table.
6. For each row/prompt, choose whether to schedule or send the prompt (**send** will send the prompt straight away when imported). Also choose the locations to share the prompt to.
7. Once ready, click **Import**, this will import the prompts and send/schedule them as per your selections.

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-import-buddy-screenshot.png?raw=true" alt="Prompt Pulse Import Prompt Buddy Prompts Screenshot"><br/>

**Note - Only 'Copilot' prompts are imported from Prompt Buddy currently.**

#### Reporting

Basic reporting to showcase the adoption of Prompt Pulse is now available in the form of a SharePoint list. A single list item will be created in this list by the app and flows if it does not exist.

The 'Reporting' list shows the following information:

- PromptsSent - The number of prompts sent in Prompt Pulse either directly or via scheduling.
- AppLaunches - The number of times the app has been launched/opened.
- PromptsCopied - The number of times prompts have been copied (clicking the prompt card) in the app.
- PromptsLiked - The total of prompts that have been liked by users.

You may wish to create your own reports using tools such as Excel or PowerBI to visualize this data.

<img src="https://github.com/pnp/prompt-pulse/blob/main/Documentation/Images/prompt-pulse-reporting-list-screenshot.png?raw=true" alt="Prompt Pulse Reporting List Screenshot"><br/>

### Tips

- Use a dedicated service account/M365 user when deploying Prompt Pulse (adaptive cards will be sent from this account).
- Deploy the app in the Teams admin center to allow users to install.
- Add the app in Teams as a tab where appropriate.
- Encourage users to install and pin the app in the rail.
- Bulk schedule prompts to encourage and nurture the usage of copilot through regular notifications.

### Considerations

- In it's current iteration, Prompt Pulse shows ALL prompts that have been shared irrespective of where the app is installed (personal app or in a teams tab) so bear this in mind when sharing prompts.
- Sharing prompts in a personal install of Prompt Pulse will also show these prompts in Prompt Pulse when installed as a teams tab.
- Only group chats that include the Prompt Pulse service account/user will appear in the app. Users will need to add this account into the group chat manually in order for it to display.
- The Prompt Pulse service account/user will be automatically added to Teams and Engage Communities when a prompt to the location for the first time.

Bear the above in mind when using Prompt Pulse, in a future iteration we will look to add support for sharing and viewing prompts in only the team the app is installed in. 


## Power Automate Flows

There are 6 flows that are part of the Prompt Pulse solution, these are listed below along with a brief description of what these do, for more details please view the [Architecture](Architecture.md) documentation.

As mentioned earlier there is an additional flow for the Prompt Buddy integration.

- Send Prompt: This flow executes when a list item is created or modified in the 'Prompts' list and sends the prompt to the specified location using adaptive cards or in the case of Viva Engage, a message. 
- Send Scheduled Prompt: This flow runs on a recurrent schedule (5 minutes by default) and is responsible for checking for scheduled prompts in the list and sending these.
- Like Prompt: This flow runs when a user clicks the **Like Prompt** button in the adaptive cards and adds the prompt to the users' liked prompts.
- Get Group Chats: This flow is used in the Power App and retrieves a list of group chats that the current user is a member of where the Prompt Pulse account is a member of the chat.
- Get Engage Communities: This flow is used in the Power App and returns a list of Viva Engage Communities that the current user is a member of.
- Parse Prompt CSV: This flow is used in the Power App and parses the content of the uploaded csv file and returns it in JSON format.
- Prompt Pulse Sync: This flow is used when Prompt Buddy is deployed, it syncronizes prompts from Prompt Buddy to Prompt Pulse and visa versa.

## Data Storage

Prompt Pulse uses SharePoint lists to store it's data. This avoids the need to use Dataverse or Dataverse for Teams which is subject to data storage constraints.

There are 3 SharePoint lists used in the solution:

- Prompts: Stores the prompts shared/scheduled in the app.
- Users: Stores the users who have opened/used Prompt Pulse and their liked prompts.
- Configuration: Stores configuration settings for Prompt Pulse.
- Reporting: Stores basic reporting data for Prompt Pulse.

These lists can be easily extended or changed should you wish to modify/customize Prompt Pulse. You can of course edit and delete prompts directly from the list if required. Avoid modifying the values of the GroupId, GroupChatId, TeamId, ChannelId and MessageId columns as these are used heavily in the Power Automate flows.

### Tutorial completion reset

When a user opens Prompt Pulse for the first time, they will need to complete a tutorial. Once completed, their list item in the **Users** list is updated and the **TutorialComplete** column is set to true (Yes). 

If you wish to reset tutorial completion for a user for any reason, simply locate the user in the list and delete the list item, the next time the user launches the app, they will be presented with the tutorial again.
