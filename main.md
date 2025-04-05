# 🚀 Build Your First Google Chat Bot using Apps Script  
Powered by **TechXNinjas**

[![Try on Cloud Skills Boost](https://img.shields.io/badge/Open_Lab-Cloud_Skills_Boost-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://www.cloudskillsboost.google/games/6064/labs/38615)

---

## 📌 About This Project

This is an introductory lab for creating a simple Google Chat bot using **Google Apps Script**.  
Perfect for students and beginners who are curious about automation and bot development using Google Workspace.

> ⚠️ **Note:** This project is strictly for learning purposes.  
Make sure to respect Google Cloud and Qwiklabs policies.

---

## 💡 Let's Start: Creating the Chat Bot

1. Open this [Chat Bot Apps Script Template](https://script.google.com/home/projects/create?template=hangoutsChat).
2. Rename the project to **`Attendance Bot`**.
3. Replace everything in the `Code.gs` file with the following script: 👇

```javascript
/**
 * Responds to a MESSAGE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onMessage(event) {
  var name = "";

  if (event.space.type == "DM") {
    name = "You";
  } else {
    name = event.user.displayName;
  }
  var message = name + " said \"" + event.message.text + "\"";

  return { "text": message };
}

/**
 * Responds to an ADDED_TO_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onAddToSpace(event) {
  var message = "";

  if (event.space.singleUserBotDm) {
    message = "Thank you for adding me to a DM, " + event.user.displayName + "!";
  } else {
    message = "Thank you for adding me to " +
        (event.space.displayName ? event.space.displayName : "this chat");
  }

  if (event.message) {
    // Bot added through @mention.
    message = message + " and you said: \"" + event.message.text + "\"";
  }
  console.log('Attendance Bot added in ', event.space.name);
  return { "text": message };
}

/**
 * Responds to a REMOVED_FROM_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onRemoveFromSpace(event) {
  console.info("Bot removed from ",
      (event.space.name ? event.space.name : "this chat"));
}
```

4. Hit **Save** 💾

---

## 📤 Deploy & Connect the Bot

To make your bot live and available for testing:

### ✅ Step 1: OAuth Setup
- Go to [OAuth Consent Screen](https://console.cloud.google.com/apis/credentials/consent?)
- See the Video: [How to Setup](https://www.youtube.com/@techxninjas/videos)

### ✅ Step 2: Configure Google Chat API
- Go to [Google Chat API Config](https://console.cloud.google.com/apis/api/chat.googleapis.com/hangouts-chat?)
- Fill the fields as below:

| Field               | Value                                                             |
|--------------------|-------------------------------------------------------------------|
| App Name           | Attendance Bot                                                    |
| Avatar URL         | `https://goo.gl/kv2ENA`                                           |
| Description        | Simple Chat Bot for attendance                                    |
| Functionality      | Tick both options: "Receive 1:1 messages" and "Join spaces and group conversations"  |
| Connection Settings| Use **App Script** and paste your **Deployment ID**      |
| Visibility         | Add your Gmail address                                            |

- Change the status to **LIVE**.

---

1. Update your `Code.gs` with the following:  
(Replace the code completely 👇)

```javascript
var DEFAULT_IMAGE_URL = 'https://goo.gl/bMqzYS';
var HEADER = {
  header: {
    title : 'Attendance Bot',
    subtitle : 'Log your vacation time',
    imageUrl : DEFAULT_IMAGE_URL
  }
};

/**
 * Creates a card-formatted response.
 * @param {object} widgets the UI components to send
 * @return {object} JSON-formatted response
 */
function createCardResponse(widgets) {
  return {
    cards: [HEADER, {
      sections: [{
        widgets: widgets
      }]
    }]
  };
}

/**
 * Responds to a MESSAGE event triggered
 * in Google Chat.
 *
 * @param event the event object from Google Chat
 * @return JSON-formatted response
 */
function onMessage(event) {
  var userMessage = event.message.text;

  var widgets = [{
    "textParagraph": {
      "text": "You said: " + userMessage
    }
  }];

  console.log('You said:', userMessage);

  return createCardResponse(widgets);
}

/**
 * Responds to an ADDED_TO_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onAddToSpace(event) {
  var message = "";

  if (event.space.singleUserBotDm) {
    message = "Thank you for adding me to a DM, " + event.user.displayName + "!";
  } else {
    message = "Thank you for adding me to " +
        (event.space.displayName ? event.space.displayName : "this chat");
  }

  if (event.message) {
    // Bot added through @mention.
    message = message + " and you said: \"" + event.message.text + "\"";
  }
  console.log('Attendance Bot added in ', event.space.name);
  return { "text": message };
}

/**
 * Responds to a REMOVED_FROM_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onRemoveFromSpace(event) {
  console.info("Bot removed from ",
      (event.space.name ? event.space.name : "this chat"));
}
```

2. Save the file, test it again in Google Chat – the bot should now respond with styled cards.

---

## 🧠 React to Button Clicks (Advanced)

Let’s take it up a notch!  
Your bot can now respond to actions like clicking buttons.

Paste this final script into `Code.gs`:

```javascript
var DEFAULT_IMAGE_URL = 'https://goo.gl/bMqzYS';
var HEADER = {
  header: {
    title : 'Attendance Bot',
    subtitle : 'Log your vacation time',
    imageUrl : DEFAULT_IMAGE_URL
  }
};

/**
 * Creates a card-formatted response.
 * @param {object} widgets the UI components to send
 * @return {object} JSON-formatted response
 */
function createCardResponse(widgets) {
  return {
    cards: [HEADER, {
      sections: [{
        widgets: widgets
      }]
    }]
  };
}

var REASON = {
  SICK: 'Out sick',
  OTHER: 'Out of office'
};
/**
 * Responds to a MESSAGE event triggered in Google Chat.
 * @param {object} event the event object from Google Chat
 * @return {object} JSON-formatted response
 */
function onMessage(event) {
  console.info(event);
  var reason = REASON.OTHER;
  var name = event.user.displayName;
  var userMessage = event.message.text;

  // If the user said that they were 'sick', adjust the image in the
  // header sent in response.
  if (userMessage.toLowerCase().indexOf('sick') > -1) {
    // Hospital material icon
    HEADER.header.imageUrl = 'https://goo.gl/mnZ37b';
    reason = REASON.SICK;
  } else if (userMessage.toLowerCase().indexOf('vacation') > -1) {
    // Spa material icon
    HEADER.header.imageUrl = 'https://goo.gl/EbgHuc';
  } else {
     // Reset to default image if keywords not found
     HEADER.header.imageUrl = DEFAULT_IMAGE_URL;
  }


  var widgets = [{
    textParagraph: {
      text: 'Hello, ' + name + '.<br>Are you taking time off today?'
    }
  }, {
    buttons: [{
      textButton: {
        text: 'Set vacation in Gmail',
        onClick: {
          action: {
            actionMethodName: 'turnOnAutoResponder',
            parameters: [{
              key: 'reason',
              value: reason
            }]
          }
        }
      }
    }, {
      textButton: {
        text: 'Block out day in Calendar',
        onClick: {
          action: {
            actionMethodName: 'blockOutCalendar',
            parameters: [{
              key: 'reason',
              value: reason
            }]
          }
        }
      }
    }]
  }];
  return createCardResponse(widgets);
}

/**
 * Responds to an ADDED_TO_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onAddToSpace(event) {
  var message = "";

  if (event.space.singleUserBotDm) {
    message = "Thank you for adding me to a DM, " + event.user.displayName + "!";
  } else {
    message = "Thank you for adding me to " +
        (event.space.displayName ? event.space.displayName : "this chat");
  }

  if (event.message) {
    // Bot added through @mention.
    message = message + " and you said: \"" + event.message.text + "\"";
  }
  console.log('Attendance Bot added in ', event.space.name);
  return { "text": message };
}

/**
 * Responds to a REMOVED_FROM_SPACE event in Google Chat.
 *
 * @param {Object} event the event object from Google Chat
 */
function onRemoveFromSpace(event) {
  console.info("Bot removed from ",
      (event.space.name ? event.space.name : "this chat"));
}

/**
 * Responds to a CARD_CLICKED event triggered in Google Chat.
 * @param {object} event the event object from Google Chat
 * @return {object} JSON-formatted response
 * @see https://developers.google.com/chat/reference/message-formats/events
 */
function onCardClick(event) {
  console.info(event);
  var message = '';
  var reason = event.action.parameters[0].value;
  if (event.action.actionMethodName == 'turnOnAutoResponder') {
    turnOnAutoResponder(reason);
    message = 'Turned on vacation settings in Gmail.';
  } else if (event.action.actionMethodName == 'blockOutCalendar') {
    blockOutCalendar(reason);
    message = 'Blocked out your calendar for the day.';
  } else {
    message = "I'm sorry; I'm not sure which button you clicked.";
  }
  return { text: message };
}

var ONE_DAY_MILLIS = 24 * 60 * 60 * 1000;
/**
 * Turns on the user's vacation response for today in Gmail.
 * Requires the Gmail API advanced service.
 * @param {string} reason the reason for vacation, either REASON.SICK or REASON.OTHER
 */
function turnOnAutoResponder(reason) {
  var currentTime = (new Date()).getTime();
  // Ensure Gmail API service is enabled in Apps Script
  try {
    Gmail.Users.Settings.updateVacation({
      enableAutoReply: true,
      responseSubject: reason,
      responseBodyHtml: "I'm out of the office today; will be back on the next business day.<br><br><i>Created by Attendance Bot!</i>",
      restrictToContacts: true,
      restrictToDomain: true,
      startTime: currentTime,
      endTime: currentTime + ONE_DAY_MILLIS
    }, 'me');
     console.log('Gmail vacation responder activated for:', reason);
  } catch (e) {
    console.error('Error activating Gmail vacation responder:', e);
    // Potentially return an error message to the user via Chat?
  }
}

/**
 * Places an all-day event on the user's primary Calendar.
 * Uses the default CalendarApp service.
 * @param {string} reason the reason for vacation, either REASON.SICK or REASON.OTHER
 */
function blockOutCalendar(reason) {
   try {
    CalendarApp.createAllDayEvent(reason, new Date(), new Date(Date.now() + ONE_DAY_MILLIS));
    console.log('Calendar blocked out for:', reason);
  } catch (e) {
    console.error('Error blocking out calendar:', e);
     // Potentially return an error message to the user via Chat?
  }
}
```

Try sending **"I'm sick"** or **"Vacation today"** to see different responses!

---

## 🌐 Stay Connected with TechXNinjas

Join our tech community and grow your skills with peers.  
We share updates on:

- Hackathons  
- Internships  
- Weekly contests  
- Roadmaps and mentorship  

🔗 [Join our WhatsApp Group](https://chat.whatsapp.com/B7uNN97yIwRHQnlYux2Tmn)  
🔗 [Follow TechXNinjas on LinkedIn](https://www.linkedin.com/company/techxninjas)  
📺 [Subscribe to YouTube](https://www.youtube.com/@TechXNinjas?sub_confirmation=1)  
📸 [Follow us on Instagram](https://www.instagram.com/techxninjas)  
👤 [Connect with Aadil Latif](https://www.linkedin.com/in/iaadillatif/)

---

## 🛠 Made with 💙 by TechXNinjas

If you found this helpful, share it with your friends and help them learn too.  
Let’s build cool stuff together! 💻
