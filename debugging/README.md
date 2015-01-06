# Debugging

> "Beware of bugs in the above code; I have only proved it correct, not tried it." - Donald Knuth

Thankfully, we have a number of tools at our disposal for debugging our PhoneGap/Famo.us apps.

### Android

#### Using Chrome's Web Console

On your Android device, enable Developer mode (on Android 4.x phones, go to `Settings` -> `About` and tap on `Build Number` (or a similar-looking static option) 7 times.

http://www.androidcentral.com/how-enable-developer-settings-android-42

Next, plug your Android phone into your computer and visit `chrome://inspect` in your Chrome browser. This will provide you with a list of connected devices (and webviews) that you can debug! 

Note that if you've signed your app, it WILL NOT show up on this list (though your phone will). 

#### Eclipse debugging for native problems

Install Eclipse and check the log (limit to `tag:chromium`) to detect native-level errors (contacts SUCK).

### iOS

#### Using Safari's Web Inspector

On your iOS device, visit `Settings` ->`Safari` -> `Advanced` and turn on `Web Inspector`.

![](https://dl.dropboxusercontent.com/u/6673634/IMG_0001.PNG)

After connecting your iOS device to your computer, open Safari and you'll see your device under the `Develop` tab.

![](https://dl.dropboxusercontent.com/u/6673634/Screenshot%202014-09-25%2013.33.29.png)


### Production Errors

When you deploy your app on devices, you'll want to know when things break (and the where/why/how)! I recommend using TrackJS and Google Analytics.

The TrackJS code included in the Waiting App in th `device_ready.js` file. After checking if the App is being run in Production, on a device, it loads the TrackJS script.


    // lazy-load track.js
    var script = document.createElement( 'script' );
    script.type = 'text/javascript';
    script.src = 'src/lib2/track.js';
    // $(script).attr('data-token', Credentials.trackjs_token);

    // trackjs options
    window._trackJs = Credentials.trackjs_opts;
    window._trackJs.version = App.ConfigImportant.Version;
    $("body").append( script );

After login, TrackJS is also updated with the user's email address (in `models/user.js`

      trackJs.configure({
        // Custom user identifier.
        userId: body.email.toLowerCase(),
      });

Google Analyticsis used to track PageView changes, so the `router.js` file contains the snippet:

    Utils.Analytics.trackRoute(window.location.hash);




### Phonegap build links (direct download for Android)

> This only works for Android, you have to use TestFlight for iOS

To easily distribute your app to folks on Android, simply copy the links on PhoneGap Build. The URL should include a `?qr_code` string.



