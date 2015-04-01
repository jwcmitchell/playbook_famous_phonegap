# Build Waiting App


### Links

Waiting App GitHub Repository:  https://github.com/FamousMobileApps/waiting_app

### Local Steps

When using the Cordova CLI to build your app locally, you'll notice that only the `your_app_name/www` directory is committed to `git`. So to get a copy of the __Waiting App__ that works locally, we need to replace our local `www` directory with the remote Waiting repository.

First step, create a new local project:

    $ cordova create waiting_app com.waiting.app.dev Waiting
    $ cd waiting_app

- directory: `waiting_app`
- app id: `com.waiting.app.dev`
- app name: `Waiting`

Add a platform:

    $ cordova platform add android

Now we'll remove the `www` directory and replace it with a remote repo.

    // still in waiting_app/
    $ rm -rf www
    $ git clone git@github.com:famousmobileapps/waiting_app.git www

> Notice the trailing `www` on line directly above me

Let's remove the remote .git connection as well

    $ cd www
    $ git remote rm origin

Finally we need to install the native plugins that the __Waiting App__ expects. You can find these listed in the `config.xml` file with the prefix `gap:plugin`:

    $ cordova plugin add org.apache.cordova.device
    $ cordova plugin add org.apache.cordova.console
    $ cordova plugin add org.apache.cordova.file
    $ cordova plugin add org.apache.cordova.file-transfer
    $ cordova plugin add org.apache.cordova.camera
    $ cordova plugin add org.apache.cordova.inappbrowser
    $ cordova plugin add org.apache.cordova.geolocation
    $ cordova plugin add org.apache.cordova.globalization
    $ cordova plugin add com.phonegap.plugins.pushplugin
    $ cordova plugin add com.phonegap.plugins.barcodescanner
    $ cordova plugin add com.phonegap.plugin.statusbar
    $ cordova plugin add com.verso.cordova.clipboard
    
    // Check config.xml and make sure you include all plugins! 
    

Neglecting to install __at least__ the `device` plugin will result in the app not appearing to work on devices, because it is expecting a `device_ready` event to fire from that plugin.

And now we'll build and run it!

    $ cordova run android -d

It may take a few minutes to build. If it __fails__, see Chapter 4.1 about building the default Hello World app for Android or iOS.


