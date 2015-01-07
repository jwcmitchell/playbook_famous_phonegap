# External Services (APIs) 

The Waiting app uses a whole host of external services, for everything from authentication using Facebook to image transformation with TransloadIt. 

Here are all the services you'll want to sign up for, with additional information included where necessary. 


### App Stores 

#### Play Store (Google Developer) 

Cost is one-time $25. 

#### iTunes (Apple Developer) 

Costs $99 to signup for a year. 


### Logins (OAuth) 

#### Facebook 

https://developers.facebook.com 

Facebook credentials go in config.xml, credentials.json, and config.json (server). 


#### Google Plus 

To enable user sign-in with Google+, follow the instructions in Step 1 at https://developers.google.com/+/quickstart/android. Keystore-creation instructions are also available in __Chapter 11.1: Android__. 

You'll add the created `gplus_ios_client_id` to your app's `credentials.json`. 

The server expects values for `gplus_server_client_id` and `gplus_server_client_secret` in `config.json`. 

For Push Notifications: Developer console: https://console.developers.google.com/





### Hosting 

#### Heroku (code) 

Sign up at https://heroku.com/

Heroku is one of many cloud providers. We'll use their command-line tools for simple development and deployment. 

Heroku Toolbelt: https://toolbelt.heroku.com/

Make sure to follow the Getting Started instructions on the page, including a local `heroku login` so that you can easily deploy. 

`foreman` will also be installed, which we'll use to recreate our Heroku environment and develop locally. 


#### MongoLab (MongoDB)  

We're using MongoLab's free 500mb sandbox for testing our apps (but any mongo host would work). 

If using heroku, it is simple to set up a new database and user (https://devcenter.heroku.com/articles/mongolab):

    heroku addons:add mongolab
    
The server is set up to handle values from the `MONGOLAB_URI` environment variable, or via `config.json`.

> When setting up a new database via the web panel, they don't auto-create a first/new user, so it must be done manually after creating the db. 


#### Firebase (websocket) 

Sign up and create a new Firebase. Secret key is for the server's config.json 

The security rules are configured from your dashboard on firebase.com and should look like: 

    {
      "rules": {
        ".write" : false,
        ".read" : false,
        "users": {
          "$uid": {
            ".write": "auth != null && auth.uid == 'admin'",
            ".read": "auth != null && (auth.uid == $uid || auth.uid == 'admin')"
          }
        }
      }
    }


### Payments 

#### Stripe 

Waiting supports switching between dev/production modes 


### Analytics and Errors 

#### Google Analytics 

Credentials go in credentials.json (the token is used, including the `UA-` part). 

#### Track.js 

http://trackjs.com/

Provides a free services for gathering javascript errors, and can be upgraded to an awesome dashboard for multiple apps. 


### Email (transactional email) 

#### SendWithUs 

https://www.sendwithus.com/

Useful for easy, beautiful templates. Needed for signup/welcome emails to work. Also can hook up SendGrid credentials. 

#### Sendgrid 

https://www.sendgrid.com/ 

A basic sendgrid account can be created using the Heroku CLI: `heroku addons:add sendgrid:starter` . It is incredibly slow delivering emails at first, so we'll probably recommend a new provider soon. 


### Misc 

#### Transload.it 

https://transloadit.com/

This is a simple, useful service for handling image resizing, cropping, filtering, thumbnailing, etc. When media is uploaded to our server, we send it over to Transload.it for processing, and receive the completed images back in a webhook. 

The Transload.it `key` and `secret` go in `config.json`. 







