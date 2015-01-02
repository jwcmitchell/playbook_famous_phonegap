# Building the Client App 

> The server example is available in Chapter 10. 

The Waiting app for iOS, Android, and Mobile Web includes features and examples for:  

- Landing, signup, and login flow
- Friends and auto-connections 
- Username searching 
- Image upload 
- Messaging between users
- Push Notifications for Android and iOs
- Comprehensive per-page routing
- BackButton customization (android)
- Popovers/Modals
- Mouse/Finger movement and drag
- Welcome screens
- Wizards (step-by-step pages)
- Data fetching via models and collections
- Authentication
 
See Chapter 4.2 for details on cloning and building the default app. 



### Chapter Guide 

Here is a brief overview of the following chapters: 

- Start by explain the directory structure of the Waiting App
- Go over misc components such as `config.xml`, `CSS Styles`, `credentials.json`, etc.
- Dive into the launching point, `main.js`, and explain the order of everything being initialized
- Explain line-by-line many of the core components of initialization, including `routing`, `utilities`, and `localization`.
- Explore Waiting's method of gathering data from a server via models and collections, and explain the setup of our Waiting App Server
- Go line-by-line over many of Waiting's PageViews, and try out various view patterns such as Wizards, Popovers, and Searching/Autocomplete.
- Discuss debugging using Chrome on your local computer, and Eclipse/XCode when necessary 
- Encourage the use of Selenium (todo) 
- Deploy an app on the Play Store (Android Market), and on TestFlight


### Core Libraries and Plugins

__Famo.us__:
Used for our Views, currently using 0.3 with some modifications

__Require.js__ (http://requirejs.org/)

__Backbone.js__:
A few modifications to the backbone core, mostly centered around Model and Collection handling, including adding the `populated()` promise and `hasFetched` boolean flag. These will be moved to plugins eventually. `backbone-adapter.js` combines all the backbone plugins.

__Handlebars.js__: For HTML templating. Used sparingly, mostly  prototyping, and should be removed in favor of native Famo.us layout components. Remember that a Surface is the lowest thing you want to animate; you would never animate a `div` that exists inside a Surface!

__jQuery__: `jquery-adapter.js` combines all the jquery plugins.

