# routes, ViewToView, StoredTransitions


### Set up our Routes

We extend Backbone.router to create `DefaultRouter`, and specify our routes. A few routes are explained more in-depth.

#### "" or "nowhere"

Sometimes your app will end up at the "" route, though you should try and prevent it. Sometimes you'll have a wayward `App.history.eraseUntilTag` that clears history for users. Catch that bug! But, fallback to just semi-restarting the app, in case you do end up here.

        '' : function(){
            if(App.history.data.length == 0){
                window.location = window.location.href.split('#')[0];
            }
        },

#### logout

We punt to th Utils.logout function, after verifying the phone is ready (to prevent some nasty redirect loop bugs).

        'logout' : function(){

            // Unregister from Push Notifications
            // - do this before exiting
            App.DeviceReady.ready.then(function(){
                Utils.logout();
            });

        },

#### welcome screens

Notice that the `welcome/username` route comes before `welcome`. Routes match only the first matched key, so the order matters!

        'welcome/username' : function(){
            defaultRoute('WelcomeUsername', 'User/WelcomeUsername', arguments, {cache: false});
        },

        'welcome' : function(){
            defaultRoute('Welcome', 'User/Welcome', arguments, {cache: false});
        },

We'll use the `defaultRoute` many, many times in our routes.

### DefaultRoute

Go read about `defaultRoute` (one or two chapters below).


____

#### More, common routes:

        'modal/helppopover' : function(){
            App.Flags.InPopover = true;
            App.history.navigate('random', {history: false});
            defaultRoute('HelpPopover', 'Misc/HelpPopover', arguments, {cache: false, popover: true});
        },

        'modal/list' : function(){
            App.Flags.InPopover = true;
            App.history.navigate('random', {history: false});
            defaultRoute('Popover', 'Misc/Popover', arguments, {cache: false, popover: true});
        },

        'login' : function(){
            // eh, I should be able to cache this route before login, then destroy after login
            defaultRoute('Login', 'Misc/Login', arguments, {cache: false});
        },

        'signup' : function(){
            defaultRoute('Signup', 'Misc/Signup', arguments, {cache: false});
        },

        'forgot' : function(){
            defaultRoute('Forgot', 'Misc/Forgot', arguments, {cache: false});
        },

        'settings': function(){
            defaultRoute('Settings', 'Misc/Settings', arguments);
        },
        'perks': function(){
            defaultRoute('Perks', 'Misc/Perks', arguments);
        },
        'feedback(/:indicator)': function(){
            defaultRoute('Feedback', 'Misc/Feedback', arguments);
        },

        'add/things' : function(){
            App.Views.MainFooter.route_show = true;
            App.Views.MainFooter.Tabs.select('new', false);
            defaultRoute('AddThings', 'Misc/AddThings', arguments, {cache: false});
        },

        'inbox' : function(){
            defaultRoute('Inbox', 'Message/Inbox', arguments);
        },

        'dash(/:id)' : function(){
            App.history.modifyLast({
                tag: 'Dash'
            });
            App.Views.MainFooter.route_show = true;
            App.Views.MainFooter.Tabs.select('profiles', false);
            defaultRoute('Dash', 'Player/Player', arguments); // used to be Player/Dash
        },


        'actions/all' : function(){
            App.Views.MainFooter.route_show = true;
            App.Views.MainFooter.Tabs.select('news', false);
            defaultRoute('Action', 'Action/Actions', arguments);
        },

        'explore/home' : function(){
            App.Views.MainFooter.route_show = true;
            App.Views.MainFooter.Tabs.select('explore', false);
            defaultRoute('Explore', 'Explore/Explore', arguments);
        },

        'players/search' : function(){
            defaultRoute('PlayerSearch', 'Player/PlayerSearch', arguments, { cache: true });
        },

        'player/add/nolink' : function(){
            defaultRoute('PlayerAddNoLink', 'Player/PlayerAdd', arguments, {cache: false});
        },
        'player/add' : function(){
            defaultRoute('PlayerAdd', 'Player/PlayerAddLink', arguments, {cache: false});
        },
        'player/edit/:id' : function(){
            defaultRoute('PlayerEdit', 'Player/PlayerEdit', arguments, {cache: false});
        },
        'player/relationship_code/:id' : function(){
            defaultRoute('Player', 'Player/PlayerRelationshipCode', arguments);
        },
        'player/:id' : function(){
            App.Views.MainFooter.route_show = true;
            App.Views.MainFooter.Tabs.select('profiles', false);
            defaultRoute('Player', 'Player/Player', arguments);
        },


        'profile/edit' : function(){
            defaultRoute('ProfileEdit', 'User/ProfileEdit', arguments, {cache: false});
        },

        'playerselect': function(){
            defaultRoute('PlayerSelect', 'Misc/PlayerSelect', arguments, {cache: false});
        }


### View To Views

In `defaultRoute` you'll notice the ViewToView variable being accessed. This is for setting custom default transitions between PageViews (such as `slideLeft`). It can be especially useful when prototyping where you want to move a whole screen left/right or spring in/out.

There are three ways of specifying Views:
- `*` (matches any/all)
- empty space
- ViewName

When matching your specified ViewToViews, multiple may end up matching, and the transition to use is specified by the order:
- __most specific / highest priority__: ViewName-ViewName
- __mid__: X -> `*`
- __lowest priority__: `*` -> X

Here are some examples:

        // Handle special transitions if the View->View conditions match
        var ViewToView = {

            // Potentially request data from the PageView here, or tell it how to render in/out
            // - we might simply emit events like "Hey, you're about to get shown" and "here is how long you have to do stuff before you're removed via the Lightbox"


            '* -> Login' : function(){
                return StoredTransitions.OpacityIn;
            },

            'Login -> *' : function(){
                return StoredTransitions.SlideDown;
            },

            'Login -> Signup' : function(){
                return StoredTransitions.SlideLeft;
            },

            'Signup -> Login' : function(){
                return StoredTransitions.SlideRight;
            },


            'Login -> Forgot' : function(){
                return StoredTransitions.SlideLeft;
            },

            'Forgot -> Login' : function(){
                return StoredTransitions.SlideRight;
            }

        };

routes
- defaultRoute
- modifyLast (Dash, etc) for tags
- MainFooter tabs route showing (without triggering)
- logout is the only different one
ViewToView defaults


### StoredTransitions

Here we defined the options available to our ViewToViews, and how quickly they'll move.


        var StoredTransitions = {

            Identity: {
                inOpacity: 1,
                outOpacity: 1,
                inTransform: Transform.identity,
                outTransform: Transform.identity,
                inTransition: { duration: 2400, curve: Easing.easeIn },
                outTransition: { duration: 2400, curve: Easing.easeIn },
            },

            OpacityIn: {
                inOpacity: 0,
                outOpacity: 0,
                inTransform: Transform.identity,
                outTransform: Transform.identity,
                inTransition: { duration: 750, curve: Easing.easeIn },
                outTransition: { duration: 750, curve: Easing.easeOut }
            },

            HideOutgoingSpringIn: {
                inOpacity: 0,
                outOpacity: 0,
                inTransform: Transform.scale(0,-0.1, 0), //Transform.translate(window.innerWidth,0,0),
                outTransform: Transform.translate(0, 0, 1),
                inTransition: { method: 'spring', period: 500, dampingRatio: 0.6 },
                outTransition: { duration: 300, curve: Easing.easeOut }
            },

            SlideDown: {
                inOpacity: 1,
                outOpacity: 1,
                inTransform: Transform.identity, //Transform.translate(0,window.innerHeight * -1,0),
                outTransform: Transform.translate(0,window.innerHeight,0),
                inTransition: { duration: 750},
                outTransition: { duration: 750},
                overlap: true
            },

            SlideUp: {
                inOpacity: 1,
                outOpacity: 1,
                inTransform: Transform.translate(0,window.innerHeight,0),
                outTransform: Transform.identity, //Transform.translate(0,window.innerHeight * -1,0),
                inTransition: { duration: 750},
                outTransition: { duration: 750},
                overlap: true
            },

            SlideLeft: {
                inOpacity: 1,
                outOpacity: 1,
                inTransform: Transform.translate(window.innerWidth,0,0),
                outTransform: Transform.translate(window.innerWidth * -1,0,0),
                inTransition: { duration: 500, curve: Easing.easeIn },
                outTransition: { duration: 500, curve: Easing.easeIn },
                overlap: true
            },

            SlideRight: {
                inOpacity: 1,
                outOpacity: 1,
                inTransform: Transform.translate(window.innerWidth * -1,0,0),
                outTransform: Transform.translate(window.innerWidth,0,0),
                inTransition: { duration: 500, curve: Easing.easeIn },
                outTransition: { duration: 500, curve: Easing.easeIn },
                overlap: true
            }
        };
