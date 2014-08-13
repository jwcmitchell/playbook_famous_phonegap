# Welcome

My name is Nicholas Reed. I'm a web developer in the Bay Area, and this is the "Playbook" that I use while developing apps for Android and iOS. I'm a proponent of this technique:

> "when you want the __speed__ of prototyping, with the __quality__ of native."

Instead of writing with Java, Obj-C, or C#, you'll write in Javascript, using the Famo.us framework for your display code, using very little HTML. We'll use the Cordova toolchain, and PhoneGap Build, to wrap the code and produce native binaries for iOS and Android. Hopefully Windows soon too.

Through my experience building PhoneGap apps, I've encountered many unknowns/bugs/features, and if you've tried building a PhoneGap app, you likely have as well. From handling the Back button on Android and incorporating it into your routing, to having consistent and beautiful transitions across every page, the scope and challenges of building an end-to-end PhoneGap app can expand quickly. This guide is a series of patterns that can be used to augment existing knowledge, or as a reference resource.

Patterns and examples include:
- Comprehensive routing, including Back button
- Popovers/Modals
- Famo.us gotchas/clarifications
- Mouse/Finger movement and physics-based drag
- Welcome screens
- Wizards (step-by-step pages)
- Data fetching via models and collections
- Authentication
- Push Notifications


Included, and referenced throughout, is a sample app called __Waiting__ (it may also be referenced as __Internal App__). Coupled with a node.js server, it works as a complete showcase of many of the concepts referenced, and can be used as a jumping off point for many apps you'd like to build.

__Waiting__ includes some useful features like:
- login and welcome pages
- friends and invite-by-sms from local contacts
- username searching
- dragging and momentum
- messaging
- Push Notifications for Android and iOS







