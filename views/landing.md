# Landing Page

Our default landing page is pretty simple: we want to give the user quick options for signing up or logging in from various services (Google+, Facebook, or Email). 


## Route

Helps to know what we're being initialized with!

> Notice that we are not caching the Landing View

`www/router.js`

    'landing' : function(){
        defaultRoute('Landing', 'Misc/Landing', arguments, {cache: false});
    },


## Initialization

Back to `www/views/Misc/Login.js`, we initialize the PageView by applying all of a Famo.us `View`'s properties.

    function PageView(options) {
        var that = this;
        View.apply(this, arguments);
        this.options = options;

We've already required (`var UserModel = require('models/user');`) our model, now we create an empty User to hold our credentials to login with later.

        // Model
        this.model = new UserModel.User();

Add your background surface. Remember, order counts (but so does Transform position), so we use both this "put it on the RenderTree first" and add a Modifier to move it backwards in Z space.

        // Add background
        var bgSurface = new Surface({
            size: [undefined, undefined],
            classes: ['bg-surface']
        });
        var backMod = new StateModifier({
            transform: Transform.behind
        });
        this.add(backMod).add(bgSurface);

We'll use a simple SequentialLayout (link to Famo.us University, or links to our included resources?) here, as we only have a few fields and don't anticipate a need to scroll.

        // Create the layout
        this.layout = new SequentialLayout();

You'll notice this pattern used frequently:

        this.layout.Views = [];

> Notice that the SequentialLayout is going to sequenceFrom "itself" i.e. `this.layout.views` later


Now we add our surfaces, in order.

Each Surface is `.push`ed onto the `this.layout.Views` array.

        this.topWelcomeSurface = new Surface({
            content: "Nemesis",
            size: [undefined, 80],
            classes: ['login-page-welcome-top-default']
        });
        this.layout.Views.push(this.topWelcomeSurface);

        this.inputEmailSurface = new InputSurface({
            name: 'email',
            placeholder: 'Email Address',
            type: 'text',
            size: [undefined, 50],
            value: '' //nicholas.a.reed@gmail.com
        });
        this.layout.Views.push(this.inputEmailSurface);

This is one way of adding spacers: simply add a blank Surface with whatever size you need. If this were a ScrollView though, you'd also need to `.pipe` the Surface in order to avoid any weird un-scrollable patches on the screen.

        this.spacer1 = new Surface({
            content: "",
            size: [undefined,4]
        });
        this.layout.Views.push(this.spacer1);


Here you'll see another method of adding a spacer after the `inputPasswordSurface`. In this instance, we add a `StateModifier` with a slightly larger `size` than the contained Surface.

        this.inputPasswordView = new View();
        this.inputPasswordView.Surface = new InputSurface({
            name: 'password',
            placeholder: 'Password',
            type: 'password',
            size: [undefined, 50],
            value: '' //testtest
        });
        this.inputPasswordView.PaddingMod = new StateModifier({
            size: [undefined, 54]
        });
        this.inputPasswordView.add(this.inputPasswordView.PaddingMod).add(this.inputPasswordView.Surface);
        this.layout.Views.push(this.inputPasswordView);


Our Login button will trigger the `login` function.

        this.submitSurface = new Surface({
            size: [undefined,60],
            classes: ['form-button-submit-default'],
            content: 'Login'
        });
        this.submitSurface.on('click', this.login.bind(this));
        this.layout.Views.push(this.submitSurface);


        this.spacerSurface = new Surface({
            content: "",
            size: [undefined, 20]
        });
        this.layout.Views.push(this.spacerSurface);


Our Signup and Forgot Password links are styled differently.

        this.signupLinkSurface = new Surface({
            content: 'Signup &gt;',
            size: [undefined,40],
            classes: [],
            properties: {
                color: "black",
                lineHeight: "40px",
                textAlign: "right"
            }
        });
        this.signupLinkSurface.on('click', function(){
            App.history.navigate('signup', {trigger: true});
        });
        this.layout.Views.push(this.signupLinkSurface);


        this.forgotLinkSurface = new Surface({
            content: 'Forgot Password &gt;',
            size: [undefined,40],
            classes: [],
            properties: {
                color: "black",
                lineHeight: "40px",
                textAlign: "right"
            }
        });
        this.forgotLinkSurface.on('click', function(){
            App.history.navigate('forgot', {trigger: true});
        });
        this.layout.Views.push(this.forgotLinkSurface);


Now that all our Surfaces (and one View!) are created and added to the array, we'll `sequenceFrom`.

        this.layout.sequenceFrom(this.layout.Views);

Finally, we created an `originMod` and `sizeMod` to get everything centered and sized correctly. The last step is adding to `this` PageView.


        var originMod = new StateModifier({
            origin: [0.5, 0.5]
        });
        var sizeMod = new StateModifier({
            size: [window.innerWidth - 16, undefined]
        });

        this.add(sizeMod).add(originMod).add(this.layout);

    }



## Login function

When the Login button is pressed, we need to:
- gather the inputs
- validate them slightly (no sense making a user wait for a "hey, you didn't enter a password" response from the server)
- check against the server
- store the token if the login succeeded
- redirect to their homepage


    PageView.prototype.login = function(){
        var that = this;

        if(this.checking === true){
            return;
        }
        this.checking = true;

        var email = this.inputEmailSurface.getValue(),
            password = this.inputPasswordSurface.getValue();

After clicking, we'll disable the input for a moment, and inform the user.

        this.submitSurface.setContent('Please wait...');

Set the data for the login request.

        var body = {
            email: email,
            password: password
        }

Make the request using the `login` method on our `User` model.

        this.model.login(body)

If the login fails, inform the user and reset the submit button text.

        .fail(function(){
            alert('Failed logging in');
            that.submitSurface.setContent('Login');
            that.checking = false;

        })

> We check for a failure condition with the response from the server too

        .then(function(response){
            if(response.code != 200){
                alert('Failed signing in (3424)');
                that.submitSurface.setContent('Login');
                that.checking = false;
                return;
            }

When the login succeeds, we save the `token` and preload all the models for the new User.


            localStorage.setItem('usertoken_v1_',response.token);
            App.Data.UserToken = response.token;

            that.model.fetch({
                error: function(){
                    alert("Failed gathering user model");
                },
                success: function(userModel){
                    console.log('UserModel');
                    console.log(userModel);

You can access the User model anytime at `App.Data.User`.

                    that.options.App.Data.User = userModel;

                    localStorage.setItem('user_v3_',JSON.stringify(userModel.toJSON()));


    Preload the user's models (friends, Posts, etc.)

                    require(['models/_preload'], function(PreloadModels){
                        PreloadModels(that.options.App);
                    });

We need to register for Push Notifications too.

                    // Register for Push Notifications
                    App.DeviceReady.initPush();

Finally, use `eraseUntilTag` to erase the whole history (because no tags called `allofem` are created, of course) and then navigate to our homepage.

                    // Reload home
                    App.history.eraseUntilTag('allofem');
                    App.history.navigate('dash');

                }
            });

        });

    };



## Transitions

Left out of our Login PageView is an inOutTransition function. Instead, we use a simple StoredTransition and ViewToView for displaying the Login page.



