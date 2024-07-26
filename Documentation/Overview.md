# Overview

Prompt Pulse consists of the following components:

- Power App
- 3 x Power Automate flows.
- 3 x SharePoint lists for data storage

For more details on what each of these does please check out the [Architecture](Documentation/Architecture.md) documentation.

## The App

The Prompt Pulse Power App provides users with an easy to use interface to share prompts share prompts directly, schedule the sharing of prompts and view prompts (all/recent/liked).

The app is fully responsive, works on mobile devices 📱 and supports dark mode! 🕶️ (please note that completion of the initial tutorial needs to be carried out on a desktop device).

To get the most out of Prompt Pulse, we recommend using the app in Microsoft Teams either as a pinned app and/or add the app into individual teams as a tab. 

When a user launches Prompt Pulse for the first time, they will be taken through a tutorial explaining how to use the app. 

## Using the app

### Sharing prompts

From the main screen, the process for sharing a prompt is as follows:

- Enter a **title** and the **prompt** in the boxes provided.
- Choose a location to share the prompt to by using the radio buttons - 

    Viva Engage Community

    Microsoft Teams Channel

    Group Chat

    Individual Users

- Send or schedule the send by using the buttons. The **Send** button sends the prompt instantly via a Power Automate flow which is trigger when an item is added/modified in the Prompts list (based on a certain criteria). The **Schedule** option will open a dialog allowing you to specify a date and time on which to send the prompt.

- Once sent, users will receive a notification in the format of an adaptive card in the chosen location.

### Viewing/using prompts

From the main screen, users can view the latest 20 prompts. To use a prompt simply click the prompt **'tile'** and the prompt text will be copied to your clipboard. 

You can then paste the prompt into Microsoft 365 Copilot. 

The **All Prompts** tab allows you to view all prompts that have been shared, text can also be copied by clicking the tile.

The adaptive card also has a button which will take a user straight to the **All Prompts** screen. Please note - this will open the app as a 'personal' app due to restrictions with deeplinking, if the user does not have the app installed they will be prompted to install it.

### Liking prompts

To like/unlike a prompt, simply use the thumbs up icon. This will create a personalized collection of **liked** prompts which can be viewed on the **All Prompts** screen.

Prompts can also be liked from the adaptive card by clicking the **Like Prompt** button. 

### Scheduling

Prompts can be scheduled to be sent at specific dates/times using the **Schedule** option. To view all scheduled prompts use the **Scheduled** tab. This table shows all the prompts that have been scheduled to be sent organised by date.

From this screen, dates/times for scheduled prompts can be updated using the edit icon and prompts can be deleted using the trash icon. 

The **Send Now** option sends the prompt straight away and removes it from the scheduled list.

**Note - this view shows the current users' scheduled prompts only.**

### Tips

- Deploy the app in the Teams admin center to allow users to install.
- Add the app in Teams as a tab where appropriate.
- Encourage users to install and pin the app in the rail.
- Bulk schedule prompts to encourage and nurture the usage of copilot through regular notifications.

### Considerations

- In it's current iteration, Prompt Pulse shows ALL prompts that have been shared irrespective of where the app is installed (personal app or in a teams tab) so bear this in mind when sharing prompts.
- Sharing prompts in a personal install of Prompt Pulse will also show these prompts in Prompt Pulse when installed as a teams tab.

Bear the above in mind when using Prompt Pulse, in a future iteration we will look to add support for sharing and viewing prompts in only the team the app is installed in. 


## Power Automate Flows

There are 3 flows that are part of the Prompt Pulse solution, these are listed below along with a brief description of what these do, for more details please view the [Architecture](Documentation/Architecture.md) documentation.

- Send Prompt: This flow executes when a list item is created or modified in the 'Prompts' list and sends the prompt to the specified location using adaptive cards or in the case of Viva Engage, a message.
- Send Scheduled Prompt: This flow runs on a recurrent schedule (5 minutes by default) and is responsible for checking for scheduled prompts in the list and sending these.
- Like Prompt: This flow runs when a user clicks the **Like Prompt** button in the adaptive cards and adds the prompt to the users' liked prompts.

## Data Storage

Prompt Pulse uses SharePoint lists to store it's data. This avoids the need to use Dataverse or Dataverse for Teams which is subject to data storage constraints.

There are 3 SharePoint lists used in the solution:

- Prompts: Stores the prompts shared/scheduled in the app.
- Users: Stores the users who have opened/used Prompt Pulse and their liked prompts.
- Configuration: Stores configuration settings for Prompt Pulse, at the time of writing it is only used to store the ID of the Power App (required for deep linking the adaptive cards in Teams).

These lists can be easily extended or changed should you wish to modify/customize Prompt Pulse. You can of course edit and delete prompts directly from the list if required. Avoid modifying the values of the GroupId, GroupChatId, TeamId, ChannelId and MessageId columns as these are used heavily in the Power Automate flows.

### Tutorial completion reset

When a user opens Prompt Pulse for the first time, they will need to complete a tutorial. Once completed, their list item in the **Users** list is updated and the **TutorialComplete** column is set to true (Yes). 

If you wish to reset tutorial completion for a user for any reason, simply locate the user in the list and delete the list item, the next time the user launches the app, they will be presented with the tutorial again.