# Android 


### Google Developer Console 

https://console.developers.google.com/ 

This is where you'll create all your credentials and API keys for Google Cloud Messenger (GCM), Google+, etc. 


### Creating .keystore file (signing for Release/Production) 

Guide on PGB: http://docs.build.phonegap.com/en_US/signing_signing-android.md.html 

Steps:

    cd ~/.android
    keytool -genkey -v -keystore androidproduction.keystore -alias androidproduction -keyalg RSA -keysize 2048 -validity 10000

Notes: 
- You'll be asked for a password (this will be requested by PGB later), and additional information (Name, Department, Company Name, City, State, Country). 
- press ENTER on the prompt after typing `yes` (it asks something about your key password being the same). 
- you can name it anything, I used "androidproduction" as both the file and alias name (alias cannot be changed!) 


### Exporting SHA1 of .keystore  

If you're using the Google+ plugin for sign-in, then you'll need to export the SHA1 hash of your keystore plugin. 

    keytool -exportcert -alias androidproduction -keystore androidproduction.keystore -list -v

You'll enter the hash as specified on this page: https://developers.google.com/+/quickstart/android 


### Push Notifications 

Instructions from http://developer.android.com/google/gcm/gs.html



## Resources (icons, spalsh screens, logos, etc.)

Visit __Chapter 6.3: Building the Waiting App: Media__ for details on creating icons, splash screens, etc.  

