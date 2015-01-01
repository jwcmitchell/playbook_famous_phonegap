# iOS

### Mobile Provisioning Profile 

Most of the information here is a rehashing of an excellent tutorial, which I __highly suggest__ you consult: http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1. It is more detailed, and has pictures! 

PhoneGap Build also has a brief iOS signing guide: http://docs.build.phonegap.com/en_US/signing_signing-ios.md.html#iOS%20Signing


The folder structure we use to manage our certificates locally is:

    ~/Desktop/
        AppleiOSCerts/
            reuse_these_pieces/
            MyApp/
                development/
                production/

#### Create your unique App ID 

In the Apple Provisioning Portal, find _Identifiers -> App IDs_

Link: https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action

Click the + icon in the top-right to create a new App ID. 

Type an App ID Name (or App ID Description) such as "Waiting"

Type a Bundle ID (or App ID Suffix) such as "com.waiting.app.pub"

In App Services, choose the Push Notifications checkbox. 

Continue, and the App ID has been created. 

#### Add your iOS device 

In the Apple Provisioning Portal, find _Devices -> All_

Link: https://developer.apple.com/account/ios/device/deviceList.action

Click the + icon in the top-right to add your device.  

Enter a name and the 40-character hex string that identifies your device. 

Continue to add your device. 


#### Create a Certificate Signing Request (CSR) file 

The CSR is what identifies you as the "owner" of an app. You'll create it locally, and then upload to Apple. We'll also export a `p12` file (with a password) that we use later. 

Open Keychain Access. From the menu bar, choose the __Certificate Assitant__ dropdown, then __Request a Certificate from a Certificate Authority__. 

Fill out the form (use you or your company's name), and choose the __Save to Disk__ option. Click Continue and save the file to `resuse_these_pieces/MyName.certSigningRequest`. 

After it has been created, visit the Keys section of Keychain Access, where you'll find a new __MyName__ private key has been created. 

Right-click to open the menu and click the __Export "MyName"__ option.

Save the file to  `reuse_these_pieces/MyName.p12` with a password you'll remember (or just write it down)! 

#### Create Development/Distribution Signing Certificate 

In the Apple Provisioning Portal, find _Certificates -> All_

Link: https://developer.apple.com/account/ios/certificate/certificateList.action

Click the + icon in the top-right to add your certificate. 

Choose _iOS App Development_ and continue. 

Upload the `reuse_these_pieces/MyName.certSigningRequest` file when requested. 

Generate the certificate. 

You don't need to download this certificate, if you're using PhoneGap Build! Instead, PGB only needs the .mobile_provision file created in the next step. 


#### Create the .mobile_provision file for PhoneGap Build  

In the Apple Provisioning Portal, find _Provisioning Profiles -> All_

Link: https://developer.apple.com/account/ios/profile/profileList.action

Click the + icon in the top-right to create a new profile.  

Choose Development or App Store. 

Choose the App ID you created earlier. 

Choose the MyName certificate you uploaded earlier. 

Choose all the devices you want to distribute to. 

Name the file. I use a variation of `MynameDec292014-1` because I frequently add/remove devices from profiles. 

Save your `.mobile_provision` file to the relevant `development` or `production` folder locally. 

#### Upload to PhoneGap Build 

Follow the instructions from __Chapter 4.3: PhoneGap Build__. 

The app you created on PGB must have the same App ID in its `plugin.xml` as you created previously (com.something.app.pub). 

On the App's page, add a new Key using the `MyName.p12` file and `.mobile_provision` file. I usually name the key the same as the mobile_provision file (ex: MyNameDec292014-1). 

After uploading the files, you'll be asked for the .p12 password, and an `.ipa` file will be built and signed. 

#### Distributing your App (the .ipk) 

There are a number of services for distributing for app to beta testers: 

- TestFlight: https://www.testflightapp.com/
- HockeyApp: http://hockeyapp.net/




### Push Notifications 

In the Apple Provisioning Portal, find _Identifiers -> App IDs_

Click the App ID for your app, and Edit, then [Push Notifications: Configure]

Upload `~/Desktop/AppleiOSCerts/reuse_these_pieces/MyName.certSigningRequest` 

Download the `aps_development.cer` file to `~/Desktop/AppleiOSCerts/(name-of-app)/development/`

 
    cd ~/Desktop/AppleiOSCerts/(name-of-app)/development/
    openssl x509 -in aps_development.cer -inform der -out dev_cert.pem

Notice that in the next command we are using the p12 file in __resuse_these_pieces__  
- "Enter Import Password" is the password for the p12 file (you created this earlier) 
- "Enter PEM Passphrase" is what we'll create now  


    openssl pkcs12 -nocerts -out dev_key.pem -in ~/Desktop/AppleiOSCerts/MyApp/reuse_these_pieces/MyName.p12  

now we're combining our cert and key, for testing purposes (the server uses them separately)  
    
    cat dev_cert.pem dev_key.pem > dev_cert_key_combined.pem

Test with following command. You should get a long line of text in return. Notice the `sandbox` addition to the domain name, which would be removed if you were testing a production connection. 

    openssl s_client -connect gateway.sandbox.push.apple.com:2195 -cert dev_cert.pem -key dev_key.pem

Move the files [dev_cert.pem, dev_key.pem] to the included server's  /your_nodeserver/certs/...


#### Development vs. Production Certificates 

iOS has sandbox and production urls that can be controlled (and matched with their relevent `.pem` files) by changing the `ios_push_notification_mode` key in `config.json` to either `dev` or `production`. 


## Resources (icons, spalsh screens, logos, etc.)

Visit __Chapter 6.3: Building the Waiting App: Media__ for details on creating icons, splash screens, etc.  



