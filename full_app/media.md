# Media

Every app has multiple media files, especially if you're pushing to an app stores. Logos, icons, and splash screens all need to be created. 

When building on PhoneGap Build, first read through their documentation: http://docs.build.phonegap.com/en_US/configuring_icons_and_splash.md.html 


### Icons and Splash Screens 

There a TON of icons you _could_ create, but really we only need a few to make our app display an icon on most platforms. 

In our `config.xml` file we include at the bottom: 

    <icon src="icon.png"/>
    <icon src="icon_ios.png" gap:platform="ios" />

    <gap:splash src="splash.png" />
    <gap:splash src="splash_ios.png" gap:platform="ios" />
    
We've only references two icons files, and two splash screens. This covers all we need, as the icons will be copied to all the necessary places by PGB. 

`icon.png` should be __512 x 512__, while `icon_ios.png` should be __120 x 120__. 

`splash.png`should be __960 x 640__, and `splash_ios.png` at __1136 x 640__. 


#### Tools to auto-build icons 

A number of tools exist online (as well as Photoshop batch scripts) to help you easily build the full range of icons for Android/iOS: 

http://www.gieson.com/Library/projects/utilities/icon_slayer 

http://makeappicon.com/ 


### Logos 

    /www/img/logo.png (512 x 512)

We use this logo file in `index.html` and on the loading screen (it fades in and out) that is displayed after the Splash screen. 



