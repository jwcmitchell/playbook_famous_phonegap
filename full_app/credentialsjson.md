# credentials.json

Many methods exist for switching between development/staging/production servers. I use this setup to quickly comment out (or add an underscore) to switch (`_server_root`). When parsed, last-seen is the "winner" between keys. In the above example, the `...herokuapp.com/` server would be the one used.

    {
        "app_name" : "Waiting",
        "launch_key" : "waiting://",
    
        "server_root" : "http://_your_subdomain_here_.ngrok.com/",
        "server_root" : "http://waitingtest1.ngrok.com/",
        "server_root" : "https://internalapp1.herokuapp.com/",
    
        "public_url" : "_your_subdomain_here_.ngrok.com",
        "public_url" : "waitingserver1.herokuapp.com",
    
        "_GoogleAnalytics" : "",
        "trackjs_opts" : {
            "application" : "waiting",
            "token" : "__token_here__",
            "console" : {
                "watch" : ["error","warn","debug","info"]
            }
        },
    
        "stripe_mode" : "test",
        "stripe_publishable_key_prod" : "",
        "stripe_publishable_key_test" : "",
    
        "show_fps" : false,
        "fps_threshold" : 61,
    
        "local_user_key" : "waiting_user_v4",
        "local_token_key" : "waiting_user_token_v4",
        "local_user_email" : "waiting_user_email_v1",
    
        "path_to_imgs" : "img/",
    
        "firebase_url" : "https://waiting.firebaseio.com//",
    
        "home_route" : "user/waiting",
    
        "push_notifications" : true,
        "push_notification_sender_id" : "454863296911",
    
        "fb_app_id" : "1576585215910000",
        "fb_permissions" : ["public_profile","email","user_friends"],
    
        "max_width" : 550,
        "max_height" : 2000,
    
        "admin_emails" : ["nick@famousmobileapps.com"],
    
        "gplus_scopes" : "email",
        "gplus_ios_client_id" : "",
        "gplus_web_client_id" : "",
        "gplus_android_web_client_id" : ""
    
    }

