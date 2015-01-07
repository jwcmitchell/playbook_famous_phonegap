# credentials.json


This file holds all the strings/credentials your app will need. You can access any value using the global `App.Credentials` variable. 


We first need a few generic values for the name of our app, and the initial route to follow (assuming they're logged in, otherwise we use the `user/landing` route). 

    {
        "app_name" : "Waiting",
        
        "home_route" : "user/waiting",
        
Our API server endpoint (`server_root`) and public-facing website URLs are next: 

        "server_root" : "https://_your_subdomain_here_.herokuapp.com/",

        "public_url" : "_your_subdomain_here_.herokuapp.com",
    
We use the following tokens for saving our logged in user. These values are stored in LocalStorage. 
    
        "local_user_key" : "waiting_user_v4",
        "local_token_key" : "waiting_user_token_v4",
        "local_user_email" : "waiting_user_email_v1",
    
When testing you may have a remote path you'd like to use for images. 

        "path_to_imgs" : "img/",


Find more on Firebase in __Chapter 7.3: Websockets__. 

        "firebase_url" : "https://waiting.firebaseio.com//",
    

        "_GoogleAnalytics" : "",
        "trackjs_opts" : {
            "application" : "waiting",
            "token" : "__token_here__",
            "console" : {
                "watch" : ["error","warn","debug","info"]
            }
        },


Stripe is a payment provider we can use for everything from simple purchases to complex marketplaces. We switch between `test` and `prod` to determine which public key we'll use. 

        "stripe_mode" : "test",
        "stripe_publishable_key_prod" : "",
        "stripe_publishable_key_test" : "",

Decide if you're app is registering (both iOS and Android) for Push Notifications, and add the Project ID (Sender ID) for Android.  

        "push_notifications" : true,
        "push_notification_sender_id" : "454863296911",
    

Our social login providers are next: 

        "fb_app_id" : "1576585215910000",
        "fb_permissions" : ["public_profile","email","user_friends"],

        "gplus_scopes" : "email",
        "gplus_ios_client_id" : "",
        "gplus_web_client_id" : "",
    
    
If using an desktop computer or larger screen device (iPad), the app will be centered and fixed with the following dimensions: 

        "max_width" : 550,
        "max_height" : 2000,

Add multiple emails to enable additional admin-only features (Send a Test Push Notification, etc.)

        "admin_emails" : ["nick@famousmobileapps.com"],
        

You can use the following Frames-Per-Second counter to help diagnose complex animation or interaction problems. 

        "show_fps" : false,
        "fps_threshold" : 61
        
    }

