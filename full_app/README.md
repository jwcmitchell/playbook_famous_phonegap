# Building the App

Now that you understand how Famo.us works and have seen your first PhoneGap app running, let's look at a larger application structure.

Make sure you have access to the __Waiting/Internal App__ source code, so you'll be able to understand how each piece fits into the overall structure.


### Structure of the following pages

- Explain the structure of the Internal/Waiting App
- Go over misc components such as `config.xml`, `CSS Styles`, `credentials.json`, etc.
- Dive into the launching point, `main.js`, and explain the order of everything being initialized
- Explain line-by-line many of the core components of initialization, including `routing`, `utilities`, and `localization`.
- Explore Internal Apps method of gathering data from a server, and explain the setup of our Internal App Server
- Go line-by-line over many of Internal App's PageViews, and try out various view patterns such as Wizards, Popovers, and Searching/Autocomplete.
- Discuss debugging using Chrome on your local computer, and Eclipse/XCode when necessary
- Encourage the use of Selenium
- Deploy an app on the Play Store (Android Market), and on TestFlight


### Core Libraries and Plugins

__Famo.us__:
Used for our Views, currently using 0.2.

__Require.js__ (http://requirejs.org/)

__Backbone.js__:
A few modifications to the backbone core, mostly centered around Model and Collection handling, including adding the `populated()` promise and `hasFetched` boolean flag. These will be moved to plugins eventually.

__Handlebars.js__: For HTML templating. Used sparingly, mostly  prototyping, and should be removed in favor of native Famo.us layout components. Remember that a Surface is the lowest thing you want to animate; you would never animate a `div` that exists inside a Surface!

__jQuery__: `jquery-adapter.js` combines all the jquery plugins.

