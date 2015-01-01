# Sending a Push Notification


Android and iOS use different systems for sending Push Notifications.

- Google Cloud Messenger (GCM) 
- Apple Push Notification service (APN) 


### Sending from the server app 

We use a `PushSetting` model per-user to keep track of what they'd like to be notified on.

We use a common format for sending a Push Notification to a user. This checks a user's `PushSetting` and `PushSettingMod` to determine if a Push Notifiation should be sent, and then sends the corresponding iOS or Android notification.

	m.PushNotification.pushToUser(user_id, {
		ios_title: 'iOS Push Message',
		title: 'Android Title of Push Notification',
		message: 'body of message, Android only',
		payload: {type: 'new_message', id: newMessage._id}
	}, 'pushsetting_mod_key', pushsetting_mod_find_conditions);

> Note: iOS has only one field (`ios_title`), while Android wants both `title` and `message`

### Android Setup 

Follow the instructions at http://developer.android.com/google/gcm/gs.html 

Add the created GCM Sender ID to the server's `config.json` file as `gcm_sender_api_key`. The key/id should looking something like: "A09LKsdEyCinSF8J2slkGsdYZEbC6-rN5uxbC83wg"

### iOS Setup 

iOS requires that certificate files are present in the `/certs` directory. Learn how to create the iOS certificates from 


### Mobile Web 

Push Notifications do not yet work for the mobile web version of an app. 

