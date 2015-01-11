# Splash Screen

Our Splash Screen is only displayed on iOS/Android (not mobile web) and is meant to be a seamless transition between the device-specific splashscreen (always an image) and our first PageView. 

    // Splash Page
    var createSplashLoading = function(){
        // 
        App.Views.SplashLoading = new RenderController({
            inTransition: false // want to immediately show our splash image
        });
        // Splash image same as image used in cordova
        App.Views.SplashLoading.Image = new ImageSurface({
            content: (App.Config.devicePlatform === 'ios') ? 'splash_640x960.png' : 'splash.png',
            size: [undefined, undefined]
        });

        App.Views.SplashLoading.show(App.Views.SplashLoading.Image);

        App.Functions.SplashAction = function(){
            // No animation, but could have one!
        }

        // Only show the SplashLoading if on a device
        if(App.usePg){
            App.MainView.add(Utils.usePlane('splashLoading')).add(App.Views.SplashLoading);
        }

    };
    

Later in `main.js` you'll find the code that actually disabled the device splash screen, leaving ours behind. 


    // Hide our device-specific splashscreen image
    if(App.usePg){
        navigator.splashscreen.hide();
    }
    


To hide our splash screen, we call `.hide` (remember it is simply a RenderController!): 

    App.Views.SplashLoading.hide();


    
    
