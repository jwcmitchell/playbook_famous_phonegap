# Signup

Signup uses many of the concepts introduced in the `Login` PageView, but introduces the `HeaderFooterLayout` that is used in almost every other PageView.


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

We'll create a header with only a "Back" button, and the standard "her I want to go back" event options.

    PageView.prototype.createHeader = function(){
        var that = this;

        this.header = new StandardHeader({
            content: "Signup",
            classes: ["normal-header"],
            backClasses: ["normal-header"],
            moreContent: false
        });
        this.header._eventOutput.on('back',function(){
            App.history.back();//.history.go(-1);
        });
        this.header.navBar.title.on('click',function(){
            App.history.back();//.history.go(-1);
        });
        this._eventOutput.on('inOutTransition', function(args){
            this.header.inOutTransition.apply(that.header, args);
        })

Add the header to the created `HeaderFooterLayout`.

        this.layout.header.add(this.header);
    };

### function `createContent()`

Our `Signup` PageView is similar to `Login` in that it uses a vertically-centered `SequentialLayout` (instead of a `ScrollView`).

But, there is now a `this.layout.content.StateModifier` that we'll use for transitioning in everything under our `this.layout.content`.

    PageView.prototype.createContent = function(){
        var that = this;

        // create the scrollView of content
        this.contentScrollView = new SequentialLayout(); //(App.Defaults.ScrollView);
        this.contentScrollView.Views = [];
        this.contentScrollView.sequenceFrom(this.contentScrollView.Views);

Adding the input fields and submit button Surfaces.

        this.addSurfaces();

Adding our `StateModifier`, and the `RenderNode/View` we'll be using to store everything before having it `add`ed it to `this.layout.content`.

        this.layout.content.StateModifier = new StateModifier();
        this.contentView = new View();
        this.contentView.SizeMod = new Modifier({
            size: //[window.innerWidth - 50, true]
                function(){
                    var tmpSize = that.contentScrollView.getSize(true);
                    if(!tmpSize){
                        return [window.innerWidth, undefined];
                    }
                    return [window.innerWidth - 16, tmpSize[1]];
                }
        });
        this.contentView.OriginMod = new StateModifier({
            origin: [0.5, 0.5]
        });
        this.contentView.add(this.contentView.OriginMod).add(this.contentView.SizeMod).add(this.contentScrollView);
        this.layout.content.add(this.layout.content.StateModifier).add(this.contentView);

    };


#### function `addSurfaces()`

Adding only three Surface:, __Email__ and __Password__ and __Signup__.

    PageView.prototype.addSurfaces = function() {
        var that = this;

        // Email
        this.inputEmailSurface = new InputSurface({
            name: 'email',
            placeholder: 'Email Address',
            type: 'text',
            size: [undefined, 50],
            value: ''
        });
        this.contentScrollView.Views.push(this.inputEmailSurface);

        this.contentScrollView.Views.push(new Surface({
            size: [undefined, 4]
        }));

        // Password
        this.inputPasswordSurface = new InputSurface({
            name: 'password',
            placeholder: 'Password',
            type: 'password',
            size: [undefined, 50],
            value: ''
        });
        this.contentScrollView.Views.push(this.inputPasswordSurface);

        this.contentScrollView.Views.push(new Surface({
            size: [undefined, 4]
        }));

        // Submit button
        this.submitButtonSurface = new Surface({
            size: [undefined,60],
            classes: ['form-button-submit-default'],
            content: 'Signup'
        });
        this.contentScrollView.Views.push(this.submitButtonSurface);

Handle the Signup button being clicked.

        this.submitButtonSurface.on('click', this.create_account.bind(this));

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
        var email = $.trim(this.inputEmailSurface.getValue().toString());
        if(email.length === 0){
            this.checking = false;
            return;
        }

        var password = this.inputPasswordSurface.getValue().toString();

        // Disable submit button
        this.submitButtonSurface.setContent('Please wait...');


Gather our data, and submit to the `signup` endpoint on our server.

        var dataBody = {
            email: email,
            password: password
        };

        // Fetch user
        $.ajax({
            url: Credentials.server_root + 'signup',
            method: 'POST',
            data: dataBody,

If signup fails in transit (500 error, timeout, etc.) handle it here.

            error: function(err){

                Utils.Notification.Toast('Failed creating Nemesis account');
                that.submitButtonSurface.setContent('Signup');
                that.checking = false;

            },

On `success` we try to login using the same email/password.

            success: function(response){

                that.checking = false;

                if(response.complete == true){
                    // Awesome, created the new user on the server


If the login fails (should happen rarely/never) we redirect to the login page.

If successful, we do everything from the success part of the `Login` PageView, but the redirect to `welcome` instead.

                    that.model.login(dataBody)
                    .fail(function(){
                        Utils.Notification.Toast('Failed login after signup');
                        App.history.navigate('login');

                    })
                    .then(function(response){
                        // Success logging in

                        // Fetch model
                        if(response.code != 200){
                            console.error('Failed signing in (3424)');
                            console.log(response);
                            console.log(response.code);
                            Utils.Notification.Toast('Failed signing in (3424)');
                            that.submitButtonSurface.setContent('Signup');
                            return;
                        }

                        // Store access_token in localStorage
                        localStorage.setItem('usertoken_v1_',response.token);
                        App.Data.UserToken = response.token;

                        // Get's the User's Model
                        that.model.fetch({
                            error: function(){
                                alert("Failed gathering user model");
                            },
                            success: function(userModel){

                                // Set global logged in user
                                that.options.App.Data.User = userModel;

                                localStorage.setItem('user_v3_',JSON.stringify(userModel.toJSON()));

                                // Preload Models
                                require(['models/_preload'], function(PreloadModels){
                                    PreloadModels(that.options.App);
                                });

                                // Register for Push Notifications
                                App.DeviceReady.initPush();

Here is where we go to the `welcome` route (after clearing history).
                                App.history.eraseUntilTag('allofem');
                                App.history.navigate('welcome');

                            }
                        });

                    });

                    return;

                }

Here is where our error messages for the responses from server. You could also return a `msg` paramter with your responses and use that instead.

Remember that we only get this far if the signup was unsuccessful.

                // See what the error was
                switch(response.msg){
                    case 'bademail':
                        alert('Sorry, that does not look like an email address to us.');
                        break;
                    case 'duplicate':
                        alert('Sorry, that email is already in use.');
                        break;
                    case 'unknown':
                    default:
                        alert('Sorry, we could not sign you up at the moment, please try again!');
                        break;

                }

                // Re-enable submit button
                that.submitButtonSurface.setContent('Signup');

                return false;

            }

        });

    };

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


