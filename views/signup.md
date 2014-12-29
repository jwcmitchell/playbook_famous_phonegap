# Signup and using Forms

We expect most users to signup using the Google+ or Facebook logins on the Landing page, but some may wish to sign up using an email address. 
This page provides an example of using the FormHelper to create simple forms. 


## Route

`www/router.js`


    'signup' : function(){
        defaultRoute('Signup', 'Misc/Signup', arguments, {cache: false});
    },


## Initialization


    function PageView(options) {
        var that = this;
        View.apply(this, arguments);
        this.options = options;

Initialize User model, same reasoning as `Login`.

        // User
        this.model = new UserModel.User();


#### `HeaderFooterLayout`

This is a standard Famo.us component. It is basically a vertical `FlexibleLayout` with up 3 surfaces allowed, but defined using `header`, `content`, and `footer`. `content` will always fill the remaining vertical space between

If you think of it as a `FlexibleLayout` it would have `ratios: [true, 1, true]`.

layout.header  
///////  
layout.content  
//////  
layout.footer  

We also define our sizing here, though in the future you may want to provide a function for `headerSize` (or a workaround to offer a Modifier on the header's `getSize` function).

Notice that we've made footerSize

        this.layout = new HeaderFooterLayout({
            headerSize: 50,
            footerSize: 0
        });

Create Header

        this.createHeader();

Create Content

        this.createContent();

Add layout to RenderTree

        this.add(this.layout);
    }


## function `createHeader()`

> You should read about `StandardHeader` and how to use it.

We'll create a header with only a "Back" button, and the standard "I want to go back" event options.

    PageView.prototype.createHeader = function(){
        var that = this;

        this.header = new StandardHeader({
            content: "Signup",
            classes: ["normal-header"],
            backClasses: ["normal-header"],
            moreContent: false
        });
        this.header._eventOutput.on('back',function(){
            App.history.back();
        });
        this.header.navBar.title.on('click',function(){
            App.history.back();
        });
        this._eventOutput.on('inOutTransition', function(args){
            this.header.inOutTransition.apply(that.header, args);
        })

Add the header to the created `HeaderFooterLayout`.

        this.layout.header.add(this.header);
    };

### function `createContent()` (FormHelper) 

Here we'll be introduced to the `FormHelper` that makes it simpler to handle form inputs (and the resulting effects on the keyboard and sizing). 


    PageView.prototype.createContent = function(){
        var that = this;


We create our form using the FormHelper we require'd at the top of the file. This how we create our wrapper/FormContainer for the form's input surfaces. 

        this.form = new FormHelper({
            type: 'form',
            scroll: true
        });

We add our services from a seperate function

        // Add surfaces to content (buttons)
        this.addSurfaces();


There is also now a `this.layout.content.StateModifier` that we'll use for transitioning in everything under our `this.layout.content`.

        // Content Modifiers
        this.layout.content.StateModifier = new StateModifier();

        // Now add content
        this.layout.content.add(this.layout.content.StateModifier).add(Utils.usePlane('content')).add(this.form);

ews);


#### function `addSurfaces()`

Adding four Surfaces: __Name__, __Email__, __Password__ and __Signup__.

    PageView.prototype.addSurfaces = function() {
        var that = this;


We'll tear apart our first input, the user's __Name__, to explain what each part is. First, the entire function:

        this.inputName = new FormHelper({
            type: 'text',
            form: this.form,

            margins: [10,10],

            name: 'name',
            placeholder: 'Name',
            value: ''
        });

        
#### `type` 

Must be one of 'text', 'textarea', 'email', 'number', etc.

#### `form` 

The form we created earlier, which is used so that we can scroll to the necessary input when it is focused on (typing in it). 

#### `margins` 

An array of margins like `[10,10,10,10]`. 

##### other properties 

Everything else you pass is being merged into the actual `InputSurface` or `TextAreaSurface` anyways. 


Continuing the Signup code, we'll create our Email and Password inputs. 

        this.inputEmail = new FormHelper({

            margins: [10,10],

            form: this.form,
            name: 'email',
            placeholder: 'Email Address',
            type: 'email',
            value: ''
        });

        this.inputPassword = new FormHelper({

            margins: [10,10],

            form: this.form,
            name: 'password',
            placeholder: 'Password',
            type: 'password',
            value: ''
        });


The following submit button also has a `click` event, that we'll bind to our `create_account` function. 

        this.submitButton = new FormHelper({
            type: 'submit',
            value: 'Sign Up with Email',
            margins: [10,10],
            click: this.create_account.bind(this)
        });


After creating all of our views/nodes, we'll add them to our form's context using the `addInputsToForm` function that accepts an array of nodes. 

The form context is important because we want the "submit" action to function and trigger when pressed on a keyboard (the Enter key on a keyboard, or an iPhone/Android popup keyboard). 

        this.form.addInputsToForm([
            this.inputName,
            this.inputEmail,
            this.inputPassword,
            this.submitButton
        ]);
    };

## Creating the account

We do a slight amount of validation on the email address and password, before submitting using collected form data (`inputEmailSurface.getValue()`), verifying a successful signup, logging in, and redirecting to the `welcome` PageView.

    PageView.prototype.create_account = function(ev){
        var that = this;

        if(this.checking === true){
            return;
        }
        this.checking = true;

        // Get email and password
        
        var name = this.inputName.getValue().toString();
        if(!name.length){
            Utils.Notification.Toast('Please include your Name!');
            return;
        }

        // Get email and password
        var email = $.trim(this.inputEmail.getValue().toString());
        if(email.length === 0){
            this.checking = false;
            Utils.Notification.Toast('Email Missing');
            return;
        }
        // todo: validate email

        var password = this.inputPassword.getValue().toString();

        // Disable submit button
        this.submitButtonSurface.setContent('Please wait...');


Gather our data, and submit to the `signup` endpoint on our server, via the User model's signup button.

        var dataBody = {
            email: email,
            password: password,
            profile_name: name,
            platform: App.Config.devicePlatform
        };
        


        // Try signup
        // - and then login
        that.model.signup(dataBody)
        .then(function(result){
            
            that.submitButton.setContent('Logging In');

            // Login
            // - same as Login.js
            that.model.login(dataBody)
            .fail(function(){

                that.checking = false;
                that.submitButton.setContent('Sign Up with Email');

                // invalid login
                console.error('Fail, invalid login');

                // Toast
                Utils.Notification.Toast('Failed login after signup');

                // Go to login
                App.history.navigate('login');

            })
            .then(function(response){
                // Success logging in

                that.checking = false;
                that.submitButton.setContent('Sign Up with Email');

                // Go to signup/home (will get redirected)
                App.history.eraseUntilTag('all-of-em');
                App.history.navigate(App.Credentials.home_route);

            });


        })
        .fail(function(){

            Utils.Notification.Toast('Failed creating account');
            that.submitButton.setContent('Sign Up with Email');
            that.checking = false;
        });

### Transitions

We want to transition our PageView differently depending on whether it is being shown or hidden, and how we're arriving/leaving the page. This `inOutTransition` function is used in nearly every PageView and makes it easy to define custom PageView-level transition/transforms.

For `Signup`, we want to modify the __opacity__ of our

    PageView.prototype.inOutTransition = function(direction, otherViewName, transitionOptions, delayShowing, otherView, goingBack){
        var that = this;

We told the `StandardHeader` to listen for this event, because it does its own transition/transform. Leaving out either this `emit` or the header's `_eventOutput.on` will cause the header to not animate in/out (but you could create your own transitions here for the header).

        this._eventOutput.emit('inOutTransition', arguments);

        switch(direction){
            case 'hiding':
                switch(otherViewName){

                    default:

We don't want to have any default transforms applied, so we tell the `App.MainController` to use `Transform.identity` for this PageView's transform.

                        // Overwriting and using default identity
                        transitionOptions.outTransform = Transform.identity;

                        // Content
                        Timer.setTimeout(function(){

                            // Bring content back
                            // that.layout.content.StateModifier.setTransform(Transform.translate(window.innerWidth,0,0), transitionOptions.inTransition);

                        }, delayShowing);

                        break;
                }

                break;
            case 'showing':
                if(this._refreshData){
                    // window.setTimeout(this.refreshData.bind(this), 1000);
                }
                this._refreshData = true;
                switch(otherViewName){

                    default:

                        // No animation by default
                        transitionOptions.inTransform = Transform.identity;


Our content starts out inivisble, where `opacity == 0`.

                        that.layout.content.StateModifier.setOpacity(0);


We then wait for the other view to finish its transition (`delayShowing + transitionOptions.outTransition.duration`) before bringing up the Opacity.

                        Timer.setTimeout(function(){

                            // Bring content back
                            that.layout.content.StateModifier.setOpacity(1, transitionOptions.inTransition);

                        }, delayShowing +transitionOptions.outTransition.duration);


                        break;
                }
                break;
        }

`inOutTransition` returns our modified transitionOptions.

        return transitionOptions;
    };


