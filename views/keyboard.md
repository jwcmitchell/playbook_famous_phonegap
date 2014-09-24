# Forms and Keyboard


Using the keyboard introduces a few problems with layout and scrolling. Thankfully, Ionic has released a plugin to make it easier, and it is on PhoneGap Build.

https://github.com/driftyco/ionic-plugins-keyboard/tree/3dd0060de1924384a4c1b2b78bef5fa8d786fd8e

When the keyboard opens, depending on the device (iOS versions differ, Android is different) your app (browser) window can be resized a bunch of different ways. We want to do three things:
- disable the "automatic" scrolling to the focused INPUT
- resize the MainController so that everything fits inside the newly-resized window.
- capture `Go` or `Submit` pressed on iOS/Android

In `device_ready.js` we do:


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

            App.mainSize = [App.defaultSize[0],App.defaultSize[1] - e.keyboardHeight];
            App.MainContext.emit('resize');

        });
        window.addEventListener('native.keyboardhide', function(e){

            App.mainSize = [App.defaultSize[0],App.defaultSize[1]];
            App.MainContext.emit('resize');

        });

and when we add an input to a scrollview, we do:

    require('views/common/ScrollviewGoto');

    this.inputNameSurface.on('focus', function(){
        that.contentScrollView.goToIndex(1,0,60);
    });


`goToIndex` takes 3 arguments:
- Index to go to
- velocity
- transform downwards (60px is great if you have a header!)


#### Go or Submit Form with native keyboard

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

On Android, the keyboard height may change depending on whether the "microphone" is shown. This results in a nasty white (or whatever background you have) line about 30-40px in height above the keyboard...occasionally. (Fix welcome!) One option is to detect if you're on Android and simply subtract 40px from the KeyboardHeight before resizing the App.mainSize.


