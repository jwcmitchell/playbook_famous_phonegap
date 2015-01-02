# Forms and Keyboard


Using the keyboard introduces a few problems with layout and scrolling. Thankfully, Ionic has released a plugin to make it easier:

https://github.com/driftyco/ionic-plugins-keyboard/tree/3dd0060de1924384a4c1b2b78bef5fa8d786fd8e

When the keyboard opens, depending on the device (iOS versions differ, Android is different) your app (browser) window can be resized a bunch of different ways. We want to do three things:
- disable the "automatic" scrolling to the focused INPUT
- resize the MainController so that everything fits inside the newly-resized window.
- capture `Go` or `Submit` pressed on iOS/Android

All of this is handled by our `FormHelper` (`/views/common/FormHelper.js`). 

In `device_ready.js` we have:


    // Keyboard
    // - requires ionic keyboard plugin
    try {
        // disable keyboard scrolling
        cordova.plugins.Keyboard.disableScroll(true);
    }catch(err){
        console.error(err, 'no Keyboard');
    }
    // add listeners for keyboard show/hide
    window.addEventListener('native.keyboardshow', function(e){
        // Utils.Notification.Toast('Keyboard Show');

        Timer.setTimeout(function(){
            var keyboardHeight = e.keyboardHeight;

            // Has the body changed in height?
            // if yes, set that as the keyboardHeight
            if(App.defaultSize[1] != Utils.WindowHeight()){
                keyboardHeight = App.defaultSize[1] - Utils.WindowHeight();
                console.log('new keyboardheight from resized window', keyboardHeight + 0);
            } else {
                console.log('resizing because the window height did NOT change');
                // if no, use the supplied keyboardHeight
                switch(App.Config.devicePlatform){
                    case 'android':
                        keyboardHeight -= 40;
                        console.log('shorter android keyboard! -40');
                        break;
                    case 'ios':
                        break;
                    default:
                        break;
                }
            }

            App.mainSize = [App.defaultSize[0],App.defaultSize[1] - keyboardHeight];

            App.KeyboardShowing = true;
            App.Events.emit('KeyboardShowHide', true);

            App.Events.emit('resize');

        },16);

    });
    window.addEventListener('native.keyboardhide', function(e){
        // Utils.Notification.Toast('Keyboard HIDE');
        
        App.mainSize = [App.defaultSize[0],App.defaultSize[1]];

        // Update the page (tell 'em)
        App.KeyboardShowing = false;
        App.Events.emit('KeyboardShowHide', false);

        App.Events.emit('resize');
        

    });
    
All of the above is basically taking care of resizing the window when the Keyboard shows/hides. Various devices and Operating Systems handle sizing and keyboard differently, this attempts to normalize the window so you're not constantly worried about your inputs being hidden.  


### Preventing the default behavior for a HeaderFooterLayout 

In your `PageView` you can simple overwrite keyboardHandler to prevent any fancy keyboard layout events (scrolling, hiding header). 

    PageView.prototype.keyboardHandler = function(){
        // no keyboard logic
    };
    

#### Go or Submit Form with native keyboard

> Use `FormHelper` instead!!! This subchapter ("Go or ...") describes roughly how `FormHelper` handles scrolling and keyboard events. For the easy, "this is how to make it do something" version, _skip this subchapter_ and instead visit __Chapter 8.8: Forms (Email Signup)__. 

When editing an input field, you can press Enter / Submit / Go to submit the form. To make this work with Famo.us, you'll use two new surfaces:
- SubmitInputSurface
- FormContainerSurface

The key point is that you want your InputSurface's and SubmitInputSurface to be contained in a FormContainerSurface.

Generally, you'll have a few InputSurface's in a row, so you'll use a SequentialLayout to put them in vertical order. A portion of our Login view looks like:


        // create the scrollView of content
        this.contentScrollView = new ScrollView();
        this.contentScrollView.Views = [];

        // Form Container
        this.FormContainer = new FormContainerSurface();

        // Add surfaces to content (inputs, submit button)
        this.addSurfaces();

        // Sequence
        this.contentScrollView.sequenceFrom(this.contentScrollView.Views);

        this.FormContainer.add(this.contentScrollView);

        this.layout.content.add(this.FormContainer);


and the `addSurfaces` function is:


    PageView.prototype.addSurfaces = function() {
        var that = this;

        // Build Surfaces

        // Email
        this.inputEmailSurface = new InputSurface({
            name: 'email',
            placeholder: 'Email Address',
            type: 'email',
            size: [undefined, 50],
            value: ''
        });

        this.inputEmailSurface.View = new View();
        this.inputEmailSurface.View.StateModifier = new StateModifier();
        this.inputEmailSurface.View.add(this.inputEmailSurface.View.StateModifier).add(this.inputEmailSurface);
        this.contentScrollView.Views.push(this.inputEmailSurface.View);

        // Password
        this.inputPasswordSurface = new InputSurface({
            name: 'password',
            placeholder: 'Password',
            type: 'password',
            size: [undefined, 50],
            value: ''
        });

        this.inputPasswordSurface.View = new View();
        this.inputPasswordSurface.View.StateModifier = new StateModifier();
        this.inputPasswordSurface.View.add(this.inputPasswordSurface.View.StateModifier).add(this.inputPasswordSurface);
        this.contentScrollView.Views.push(this.inputPasswordSurface.View);

        // Submit button
        this.submitButtonSurface = new SubmitInputSurface({
            value: 'Login',
            size: [undefined, 60],
            classes: ['form-button-submit-default']
        });
        this.submitButtonSurface.View = new View();
        this.submitButtonSurface.View.StateModifier = new StateModifier();
        this.submitButtonSurface.View.add(this.submitButtonSurface.View.StateModifier).add(this.submitButtonSurface);
        this.contentScrollView.Views.push(this.submitButtonSurface.View);

        // Forgot password
        this.forgotPassword = new View();
        this.forgotPassword.StateModifier = new StateModifier();
        this.forgotPassword.Surface = new Surface({
            content: 'Forgot your password? Reset Now',
            size: [undefined, 60],
            classes: ['login-forgot-pass-button']
        });
        this.forgotPassword.Surface.on('click', function(){
            App.history.navigate('forgot');
        });
        this.forgotPassword.add(this.forgotPassword.StateModifier).add(this.forgotPassword.Surface);
        this.contentScrollView.Views.push(this.forgotPassword);

        this.FormContainer.on('submit', function(ev){
            ev.preventDefault();
            return false;
        });

        // Events for surfaces
        this.submitButtonSurface.on('click', this.login.bind(this));


    };

Notice that we're preventing event propagation on the FormContainer, and the Submit button is using a SubmitInputSurface (with `value` instead of `content` like a normal Surface).
    

### Gotchas

Fixed most gotchas, at the moment. 
