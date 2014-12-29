# Push Notifications

Push Notifications have a server and client-side component.


### Certificates and Setup

#### iOS

Apple uses the Apple Push Notifiation service (APN). This is an excellent tutorial on obtaining keys:
http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1

#### Android

Use Google Cloud Messaging to obtain the necessary keys.
http://developer.android.com/google/gcm/gs.html

See also __Chapter 11.1 - Production -> Android__ for more Release/Production setup instructions. 


### Server Side

We use a PushSetting model per-user to keep track of what they'd like to be notified on.

We use a common format for sending a Push Notification to a user. This checks a user's PushSetting and PushSettingMod to determine if a Push Notifiation should be sent, and then sends the corresponding iOS or Android notification.

	m.PushNotification.pushToUser(user_id, {
		ios_title: 'iOS Push Message',
		title: 'Title of Push Notification',
		message: 'body of message, Android only',
		payload: {type: 'new_message', id: newMessage._id}
	}, 'pushsetting_mod_key', pushsetting_mod_conditions);

__iOS__ has only one field (`ios_title`) while __Android__ accepts both `title` and `message`.


### Client Side

Couple of steps:
- register device for Push Notifications
- upload token to server
- handle incoming messages and payloads

Inside `device_ready.js` we handle registration for iOS and Android Push Notifications. `Utils.process_push_notification_message` is the function that handles the incoming payload.

    process_push_notification_message : function(payload){

        if(typeof payload === typeof ""){
            payload = JSON.parse(payload);
        }

Your payload could be anything... in our example we show a Popover asking the user if they want to view the message.

        switch(payload.type){
            case 'new_message':
                Utils.Popover.Buttons({
                    title: 'New Message',
                    buttons: [
                        {
                            text: 'Ignore'
                        },
                        {
                            text: 'View Message',
                            success: function(){
                                App.history.navigate('message/' + payload.id);
                            }
                        }
                    ]
                });

                break;

### When will a Push Notification pop up?

On the server, you need to include the `title` and `message` parameters (Android) or `body` (iOS). Skipping these will result in the Push Notification being delivered in the background (nothing audible or visual to the user).

