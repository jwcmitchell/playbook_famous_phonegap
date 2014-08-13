# config.xml


    <?xml version='1.0' encoding='utf-8'?>
    <widget id="com.nemesis.app.pub" version="0.0.1.52" xmlns="http://www.w3.org/ns/widgets" xmlns:gap="http://phonegap.com/ns/1.0" xmlns:cdv="http://cordova.apache.org/ns/1.0">
        <name>Hello World</name>
        <description>
            My First App
        </description>
        <author email="your_email_here@example.com" href="http://cordova.io">
            Nicholas Reed
        </author>
        <content src="index.html" />

        <access origin="*" />

        <preference name="phonegap-version" value="3.4.0" />

        <!-- Android SDK Version -->
        <preference name="android-minSdkVersion" value="14" />
        <!-- iOS Version -->
        <preference name="deployment-target" value="7.0" />

        <preference name="orientation" value="portrait" />
        <preference name="fullscreen" value="false" />

        <preference name="target-device" value="handset" />
        <preference name="disallowOverscroll" value="true" />
        <preference name="webviewbounce" value="false" />
        <preference name="exit-on-suspend" value="false" />
        <preference name="detect-data-types" value="false" />
        <preference name="StatusBarOverlaysWebview" value="false" />

        <gap:platform name="ios" />
        <gap:platform name="android" />

        <!-- Hide Status Bar iOS -->
        <gap:config-file platform="ios" parent="UIViewControllerBasedStatusBarAppearance" overwrite="true">
            <false/>
        </gap:config-file>
        <gap:config-file platform="ios" parent="UIStatusBarHidden" overwrite="true">
            <true/>
        </gap:config-file>

        <gap:plugin name="org.apache.cordova.device" />
        <gap:plugin name="org.apache.cordova.console" />
        <gap:plugin name="org.apache.cordova.file" />
        <gap:plugin name="org.apache.cordova.file-transfer" />
        <gap:plugin name="org.apache.cordova.camera" />
        <gap:plugin name="org.apache.cordova.inappbrowser" />
        <gap:plugin name="org.apache.cordova.geolocation" />
        <gap:plugin name="org.apache.cordova.globalization" />
        <gap:plugin name="com.phonegap.plugins.pushplugin" />
        <gap:plugin name="com.phonegap.plugins.barcodescanner" />
        <gap:plugin name="com.phonegap.plugin.statusbar" />
        <gap:plugin name="com.verso.cordova.clipboard" />

        <gap:plugin name="nl.x-services.plugins.socialsharing" />
        <gap:plugin name="nl.x-services.plugins.toast" version="1.0" />
        <gap:plugin name="de.appplant.cordova.plugin.local-notification" />
        <gap:plugin name="com.adobe.plugins.gaplugin" />


        <feature name="BarcodeScanner">
            <param name="ios-package" value="CDVBarcodeScanner" />
            <param name="android-package" value="com.phonegap.plugins.barcodescanner.BarcodeScanner" />
        </feature>

        <feature name="SocialSharing">
          <param name="ios-package" value="SocialSharing" />
          <param name="android-package" value="nl.xservices.plugins.SocialSharing" />
        </feature>

        <feature name="Toast">
          <param name="ios-package" value="Toast" />
          <param name="android-package" value="nl.xservices.plugins.Toast" />
        </feature>

        <feature name="Device">
            <param name="android-package" value="org.apache.cordova.device.Device" />
        </feature>

        <feature name="File">
            <param name="android-package" value="org.apache.cordova.file.FileUtils" />
            <param name="ios-package" value="CDVFile" />
        </feature>

        <feature name="FileTransfer">
            <param name="android-package" value="org.apache.cordova.filetransfer.FileTransfer" />
            <param name="ios-package" value="CDVFileTransfer" />
        </feature>

        <feature name="InAppBrowser">
            <param name="android-package" value="org.apache.cordova.inappbrowser.InAppBrowser" />
            <param name="ios-package" value="CDVInAppBrowser" />
        </feature>

        <feature name="PushPlugin" >
            <param name="android-package" value="com.plugin.gcm.PushPlugin"/>
            <param name="ios-package" value="PushPlugin"/>
        </feature>

        <feature name="LocalNotification">
            <param name="android-package" value="de.appplant.cordova.plugin.localnotification.LocalNotification"/>
            <param name="ios-package" value="APPLocalNotification"/>
        </feature>

        <icon src="icon.png" />
        <icon src="icon_ios.png" gap:platform="ios" />

        <gap:splash src="splash.png" />
        <gap:splash src="splash/ios/Default_iphone5.png" gap:platform="ios" width="640" height="1136" />

    </widget>


When building locally, all of the `<gap...` tags are ignored, and the `<feature...` tags are used. On PhoneGap Build, the opposite holds true. The rest of the tags work for both build locations. Local builds use the `root_dir/config.xml` while PhoneGap Build uses one a directory deeper: `root_dir/www/config.xml`. Because it is annoying to update multiple config files and keep local/remote config files separate, we simply use symbolic links (symlinks) to a single `config.xml` file.

    $: cd ~/Sites/hello
    $: [ -f config.xml ] && echo "Found" || echo "Not"
    "Found" // just confirming that our config.xml exists
    $: cd www
    $: ln -s ../config.xml

