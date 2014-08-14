# device_ready.js


## Waiting for .onReady to be...ready

> the `onReady` event is emitted by Cordova when the webview has loaded all the native plugins

At the start we're creating some promises that will get resolved in `init`.

    test: "hello",
    readyDeferred: readyDeferred,
    ready: readyDeferred.promise(),
    init: function(){
        var that = this;

Are we running on a phone/emulator with access to native functions? (we assign this `App.Data.usePg`)

        App.Data.usePg = false;
        if (navigator.userAgent.match(/(iPhone|iPod|iPad|Android|BlackBerry)/)) {
            // Yes, we're running on a phone/emulator
            App.Data.usePg = true;
        }

        // Trigger .onReady

If we are in the browser (in Development, versus Production/Device), we'll manually call `onReady` because the native plugin never runs (because we are not on a phone)

        if(!App.Data.usePg){
            // Yes, we're running in a browser
            that.onReady();
        }


The following lines are to make sure the `onReady` promise gets resolved. Just in case the "deviceready" listener on the document wasn't already on there, we add it. If it has already resolved (from the previous addition, in index.html) then `GLOBAL_onReady` would have been set previously.

        if(GLOBAL_onReady === true){
            that.onReady();
        } else {
            document.addEventListener("deviceready", function(){
                that.onReady();
            }, false);
        }


## .onReady function

In the .onReady function, we enable many different functions, as long as `App.Data.usePg == true`.

### error tracking via track.js

Notice that we're only enabling track.js on devices, in production.
This reduces clutter in your error logs, and keeps your debugging local.

            if(App.Data.usePg && App.Prod){

                // lazy-load track.js
                var script = document.createElement( 'script' );
                script.type = 'text/javascript';
                script.src = 'src/lib2/track.js';
                // $(script).attr('data-customer','2138571d0e004d109396748e01e291a0');
                $("body").append( script );

                // Need to init it first
                console.error();

                //override the error function (the immediate call function pattern is used for data hiding)
                console.error = (function () {
                  //save a reference to the original error function.
                  var originalConsole = console.error;
                  //this is the function that will be used instead of the error function
                  function myError (stackTrace) {
                    // alert( 'Error is called. ' );
                    try {
                        trackJs.track(stackTrace);
                    }catch(err){
                        console.log('trackJs.track non-existant for now');
                    }

                    //the arguments array contains the arguments that was used when console.error() was called
                    originalConsole.apply( this, arguments );
                  }
                  //return the function which will be assigned to console.error
                  return myError;
                })();

                // // Testing in production
                // window.setTimeout(function(){
                //     // Throw a failure
                //     throw WeAreInProduction
                // },3000);

            }


### Pausing


            // Pausing (exiting)
            document.addEventListener("pause", function(){
                // Mark as Paused
                // - this prevents Push Notifications from activating all at once when Resuming

                App.Data.paused = true;
                App.Data.was_paused = true;

            }, false);

### Resuming

            // Resume
            // - coming back to application
            document.addEventListener("resume", function(){
                // Gather existing Push Notifications and see if we
                // should summarize them, or show individually (confirm, etc.)

                App.Events.trigger('resume');

                App.Data.paused = false;
                App.Data.was_paused = true;

                // Run 1 second after returning
                // - collecting all the Push Notifications into a queue
                // - enough time for all Push events to be realized
                setTimeout(function(){

                    App.Data.paused = false;
                    App.Data.was_paused = false;

                },1000);

            }, false);

### Menu button



            // Init MENU button on Android (not always there?)
            document.addEventListener("menubutton", function(){
                App.history.navigate('settings');
            }, false);

            // Init BACK button on Android
            // - disable default first
            document.addEventListener("backbutton", function(killa){
                App.Events.emit('backbutton');
                killa.stopPropagation();
                killa.preventDefault();
                return false;
            }, true);

### Online / Offline state

            // // Online/Offline state

            // //Create the View
            // // - render too
            // App.Data.GlobalViews.OnlineStatus = new App.Views.OnlineStatus();
            // App.Data.GlobalViews.OnlineStatus.render();

            // // Online
            // // - remove "not online"
            // document.addEventListener("online", function(){
            //  // Am now online
            //  // - emit an event?
            //  App.Data.GlobalViews.OnlineStatus.trigger('online');

            // }, false);
            // document.addEventListener("offline", function(){
            //  // Am now online
            //  // - emit an event?
            //  App.Data.GlobalViews.OnlineStatus.trigger('offline');

            // }, false);



## Back button

### In-Browser (development) capturing of the DELETE/Backspace button
            // Back button capturing in Browser
            if(!App.Data.usePg){
                $(document).keydown(function (e) {
                    var preventKeyPress;
                    if (e.keyCode == 8) {
                        var d = e.srcElement || e.target;
                        switch (d.tagName.toUpperCase()) {
                            case 'TEXTAREA':
                                preventKeyPress = d.readOnly || d.disabled;
                                break;
                            case 'INPUT':
                                preventKeyPress = d.readOnly || d.disabled ||
                                    (d.attributes["type"] && $.inArray(d.attributes["type"].value.toLowerCase(), ["radio", "checkbox", "submit", "button"]) >= 0);
                                break;
                            case 'DIV':
                                preventKeyPress = d.readOnly || d.disabled || !(d.attributes["contentEditable"] && d.attributes["contentEditable"].value == "true");
                                break;
                            default:
                                preventKeyPress = true;
                                break;
                        }
                    }
                    else
                        preventKeyPress = false;

                    if (preventKeyPress){
                        e.preventDefault();
                        App.Events.emit('backbutton');
                    }
                });
            }

### On Device Back button (Android)

            // Init BACK button on Android
            // - disable default first
            document.addEventListener("backbutton", function(killa){
                App.Events.emit('backbutton');
                killa.stopPropagation();
                killa.preventDefault();
                return false;
            }, true);


### GPS Updates

        // GPS updates
        runGpsUpdate: function(){
            // We only want one running at a time
            if(App.Cache.running_gps_update === true){
                return;
            }

            App.Cache.running_gps_update = true;

            var runGpsUpdateFunc = function(){
                navigator.geolocation.getCurrentPosition(function(position){
                    console.log('coords');
                    console.log(position.coords);
                    App.Events.emit('updated_user_current_location');
                    App.Cache.geolocation_coords = position.coords;
                    // alert(position.coords);
                    // alert(position.coords.latitude);
                    // $.ajax({
                    //     url: Credentials.server_root + 'user_coords',
                    //     cache: false,
                    //     method: 'POST',
                    //     success: function(){
                    //         console.log('Made call OK');
                    //     }
                    // });

                }, function(err){
                    console.log('GPS failure');
                    console.log(err);
                });

                window.setTimeout(runGpsUpdateFunc, 1000 * 60 * 5); // 5 minutes
            };

            // runGpsUpdateFunc(); // uncomment for coordinate uploads

        },



## Push Notifications

### Initializing Push Notifications

        initPush: function(){
            console.info('Registering for PushNotification');

            // Disable Push in debug mode
            if(App.Prod != true){
                console.error('Development mode');
                return;
            }
            try {
                App.Data.pushNotification = window.plugins.pushNotification;
                if (device.platform.toLowerCase() == 'android') {

                    App.Data.pushNotification.register(function(result){
                        console.log('Push Setup OK');

                    }, function(err){
                        alert('failed Push Notifications');
                        console.error(err);
                    },
                    {
                        "senderID": "your_sender_id_here",
                        "ecb": "onNotificationGCM"
                    });
                } else if (device.platform.toLowerCase() == 'ios') {

                    App.Data.pushNotification.register(function(token){
                        console.log('ios token');
                        console.log(token);

                        // Write the key
                        // - see if the user is logged in
                        var i = 0;
                        var pushRegInterval = function(){
                            window.setTimeout(function(){
                                // See if logged in
                                if(App.Data.User.get('_id')){
                                    App.Data.User.set({ios: [{reg_id: token, last: new Date()}]});
                                    App.Data.User.save(); // update the user
                                } else {
                                    // Try running again
                                    // App.Utils.Notification.debug.temp('NLI - try again' + i);
                                    console.log('Not logged in - try android registration update again');
                                    console.log(App.Data.User.get('_id'));
                                    i++;
                                    pushRegInterval();
                                }
                            },3000);
                        };
                        pushRegInterval();


                    },function(err){
                        console.log('Failed ios Push notifications');
                        console.log(err);
                    },   {"badge":"true","sound":"true","alert":"true","ecb":"onNotificationAPN"});
                }
            }
            catch(err) {
                // txt="There was an error on this page.\n\n";
                // txt+="Error description: " + err.message + "\n\n";
                // alert(txt);
                // alert('Push Error2');
                if(App.Data.usePg){
                    console.error('Push Error 2');
                }

                // console.log(err);
                // App.Utils.Notification.debug.temporary('Push Error');
            }
        },


### Handling incoming Push Notifications

    function onNotificationAPN(event) {
        var pushNotification = window.plugins.pushNotification;
        console.log("Received a notification! " + event.alert);
        console.log("event sound " + event.sound);
        console.log("event badge " + event.badge);
        console.log("event " + event);
        if (event.alert) {
            navigator.notification.alert(event.alert);
        }
        if (event.badge) {
            console.log("Set badge on  " + pushNotification);
            pushNotification.setApplicationIconBadgeNumber(function(){
                console.log('succeeded at something in pushNotification for iOS');
            }, event.badge);
        }
        if (event.sound) {
            var snd = new Media(event.sound);
            snd.play();
        }
    };

    // GCM = Google Cloud Messag[something]
    function onNotificationGCM(e){
        // Received a notification from GCM
        // - multiple types of notifications

        // App.Utils.Notification.debug.temp('New Notification: ' + e.event);
        // alert('onNotificationGCM');
        console.log('onNotificationGCM');

        switch( e.event ){
            case 'registered':
                // alert('registered');
                // Registered with GCM
                if ( e.regid.length > 0 ) {
                    // Your GCM push server needs to know the regID before it can push to this device
                    // here is where you might want to send it the regID for later use.
                    // alert('registration id: ' + e.regid);
                    // App.Utils.Notification.debug.temp('Reg ID:' + e.regid.substr(0,25) + '...');
                    console.log('Android registration ID for device');
                    console.log(e.regid);

                    // // Got the registration ID
                    // // - we're assuming this happens before we've done alot of other stuff
                    // App.Credentials.android_reg_id = e.regid;

                    // Write the key
                    // - see if the user is logged in
                    var i = 0;
                    var pushRegInterval = function(){
                        window.setTimeout(function(){
                            // See if logged in
                            if(App.Data.User.get('_id')){
                                // Sweet, logged in, update user's android_reg_id
                                // alert('saving user!'); // ...
                                // alert(App.Data.User.get('_id'));
                                // alert(e.regid);
                                App.Data.User.set({android: [{reg_id: e.regid, last: new Date()}]});
                                App.Data.User.save(); // update the user

                                // App.Plugins.Minimail.updateAndroidPushRegId(App.Credentials.android_reg_id);
                            } else {
                                // Try running again
                                // App.Utils.Notification.debug.temp('NLI - try again' + i);
                                console.log('Not logged in - try android registration update again');
                                console.log(App.Data.User.get('_id'));
                                i++;
                                pushRegInterval();
                            }
                        },3000);
                    };
                    pushRegInterval();

                }
                break;

            case 'message':
                // if this flag is set, this notification happened while we were in the foreground.
                // you might want to play a sound to get the user's attention, throw up a dialog, etc.

                Utils.process_push_notification_message(e.payload.payload);

                break;

            case 'error':
                alert('GCM error');
                alert(e.msg);
                break;

            default:
                alert('An unknown GCM event has occurred');
                break;
        }
    };




