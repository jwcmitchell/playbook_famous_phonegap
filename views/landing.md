# Landing Page and Logins (using OAuth)  

Our default landing page is pretty simple: we want to give the user quick options for signing up, or logging in from various services. We currently support: 

- Google+ 
- Facebook 
- Email 

Both Google+ and Facebook use native libraries to sign the user in. When requesting an access_token for the service we also request the equivalent of an `email` scope. We then validate the access_token on our server at the `/user/{gplus|facebook}` endpoint. 


## Route

Helps to know what we're being initialized with!

`www/router.js`

    'landing' : function(){
        defaultRoute('Landing', 'Misc/Landing', arguments);
    },


## Initialization

Back to `www/views/Misc/Landing.js`, we initialize the PageView by applying all of a Famo.us `View`'s properties.

    
    function PageView(options) {
        var that = this;
        StandardPageView.apply(this, arguments);
        this.options = options;


In here we set up a default user model 

        this.loadModels();

Next up is the sizing of our `HeaderFooterLayout`, where we have no header, and our footer size depends on how many buttons we have in it (in our case, 3 rows of buttons, where 200 is about right).

        this.layout = new HeaderFooterLayout({
            headerSize: 0
            footerSize: 200
        });

Our background surface will be styled in CSS 

        this.layout.Bg = new Surface({
            content: '',
            size: [undefined, undefined],
            classes: ['landing-page-bg-default']
        });

Now we create our content, and our footer

        this.createContent();
        this.createFooter();
        
Finally we assign each of our created views to the correct plane (see __Views: Planes / Layers __) 

        // Assign to correct plane
        this.add(Utils.usePlane('content',-1)).add(this.layout.Bg);
        this.add(Utils.usePlane('content',1)).add(this.layout);

    }


#### createFooter 

This is an example of creating a more complex layout using `LayoutBuilder` that also provides animations for the buttons as the page transitions in/out (show/hide). 

    PageView.prototype.createFooter = function(){
        var that = this;


We create a `surfaceAnimation` function that accepts a surface and the x,y coordinates to move the surface when the page hides. 

We've also used a Timeout of 1 in order to wait for the scroller to be initialized before we pipe our surface to it. 

        var surfaceAnimation = function(surface, x, y){

            Timer.setTimeout(function(){
                surface.pipe(that.footer.scroller);
            },1);
            that._eventOutput.on('inOutTransition', function(args){
                if(args[0] == 'showing'){
                    surface.mods.Animate.setTransform(Transform.translate(x,y,0));
                    surface.mods.Animate.setTransform(Transform.translate(0,0,0), {
                        duration: 450,
                        curve: Easing.easeIn
                    });

                    surface.mods.Animate.setOpacity(0);
                    surface.mods.Animate.setOpacity(1,{
                        duration: 960,
                        curve: Easing.easeOutBounce
                    });

                } else {
                    surface.mods.Animate.setTransform(Transform.translate(0,0,0));
                    surface.mods.Animate.setTransform(Transform.translate(x,y,0), {
                        duration: 350,
                        curve: Easing.easeOut
                    });

                    surface.mods.Animate.setOpacity(1);
                    surface.mods.Animate.setOpacity(0,{
                        duration: 350,
                        curve: Easing.easeIn
                    });
                }
            });
        };

Now we create our footer using `LayoutBuilder`: 

        this.footer = new LayoutBuilder({
            scroller: {
                direction: 1,
                sequenceFrom: [{
                    surface: {
                        key: 'GoogleLogin',
                        mods: [{
                            key: 'Animate'
                        }],
                        surface: new Surface({
                            content: '<div><span><i class="icon ion-social-googleplus"></i></span>Google+</div>',
                            size: [Utils.WindowWidth(), 60],
                            classes: ['landing-button','gplus-button']
                        }),
                        click: function(){
                            // Pass off Google+ login/signup to the user's model
                            // Utils.Notification.Toast('Attempting signin');
                            var userModel = new UserModel.User();
                            userModel.gplus_login();
                        },
                        events: function(surface){
                            surfaceAnimation(surface, 0, 100);
                        }
                    }
                },{
                    surface: {
                        key: 'FacebookLogin',
                        mods: [{
                            key: 'Animate'
                        }],
                        surface: new Surface({
                            content: '<div><span><i class="icon ion-social-facebook"></i></span>Facebook</div>',
                            size: [Utils.WindowWidth(), 60],
                            classes: ['landing-button','facebook-button']
                        }),
                        click: function(){
                            // Pass off Facebook login/signup to the user's model
                            var userModel = new UserModel.User();
                            userModel.fb_login();
                        },
                        events: function(surface){
                            surfaceAnimation(surface, 0, 100);
                        }

                    }
                },
                {
                    size: [undefined, 60],
                    flexible: {
                        direction: 0,
                        ratios: [1,1],
                        sequenceFrom: [{
                            surface: {
                                key: 'EmailSignup',
                                mods: [{
                                    key: 'Animate'
                                }],
                                surface: new Surface({
                                    content: '<div>Sign Up</div>',
                                    size: [undefined, true],
                                    classes: ['landing-button','signup-button']
                                }),
                                click: function(){
                                    App.history.navigate('signup');
                                },
                                events: function(surface){
                                    surfaceAnimation(surface, -100, 0);
                                }

                            }
                        },{
                            surface: {
                                key: 'Login',
                                mods: [{
                                    key: 'Animate'
                                }],
                                surface: new Surface({
                                    content: '<div>Log In</div>',
                                    size: [undefined, true],
                                    classes: ['landing-button','login-button'] // landing-login-button
                                }),
                                click: function(){
                                    App.history.navigate('login');
                                },
                                events: function(surface){
                                    surfaceAnimation(surface, 100, 0);
                                }

                            }
                        }]
                    }
                }]
            }
        });

After it is created, we attach it to the `HeaderFooterLayout`  

        this.layout.footer.add(this.footer);
    };


### Email Signup 

The most basic of signup forms is described in the next section: __Email Signup (Forms)__. 


### OAuth login (Google+ and Facebook)  

Both Google+ and Facebook follow the same authentication flow:

1. Initiate native apps, usually pops up a dialog box which allows the user to sign in
2. Receive g+/fb access_token for user
3. Send access_token to our server
4. Server validates token and gets email address
5. Server either signs up the new user, or logs them in, and returns a new access_token to the client
6. Client initiates `UserModel.login` 




