# Building Locally

> Assumes you have the necessary components installed from Chapter 3 - Setup / Requirements.

The Cordova command line toolchain can be used to install, build, and run your app locally.

## Build Hello World App


### Initial Cordova CLI commands

Use the pattern `cordova create directoryName id.of.app NameOfApp` when creating your new app:

    $ cordova create hello com.hello.world HelloWorld
    $ cd hello

### Android
    $ cordova platform add android
    $ cordova plugin add org.apache.cordova.device
    $ cordova run android -d

When you're able to run this command successfully, you'll have a working version of the Hello World app.

If you encounter issues connecting to your attached Android device, I recommend running

    $ adb devices

to make sure your device is visible.


### iOS

iOS instructions assume the use of OSX.

    $ cordova platform add ios
    $ cordova plugin add org.apache.cordova.device
    $ cordova build ios -d

After building successfully, we need to use XCode to finally launch our app.

Open the file `hello/platforms/ios/HelloWorld.xcodeproj` in XCode, choose your target device (either the emulator or a connected device/phone), and click the big __PLAY__ icon in the top-left.


### Failed?

If you're unable to build or run locally, please skip to the __PhoneGap Build__ step (Chapter 4.3) where we'll use the online build service to get valid binaries.


