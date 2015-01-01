# Push Notifications

Push Notifications also have a server-side component that is detailed in __Chapter 10.2__. 


### Client Side

Couple of steps that the app takes to prepare for push notifications and handle them when they arrive: 

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

