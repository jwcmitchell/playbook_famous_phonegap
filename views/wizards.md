# Wizard - Send Message


This shows the common patterns used in a Wizard. It isn't the most expedient way to send a message, but gets our example point across.
- MessageAdd (handles routing)
- MessageAddText
- MessageAddImage (optional)
- MessageConfirm
- View Message (and eraseUntilTag)

When going through a Wizard, there are a few extra things to keep in mind.
- before starting, `modifyLast` of the page you want to return to, or erase up until that `tag`.
- use `{history:false}` when calling the original `ThingAdd` route, because the Wizard will manage it's own history (and the first page viewed is


## Structure

Our example wizard is going to be sending a message to a username, and will include (optional) text and media.

In most cases, you'd want to have more than one action per-screen, or combine all of this sending into a single page.

    www/src/views/Message/
        Subviews/
        Inbox.js
        Add.js
        AddUsername.js
        AddText.js
        AddMedia.js

## Launching Wizard

Before launching the wizard you need to create a `tag` in the history for you to return to after the Wizard completes, or to erase to (in case you are navigating somewhere else after going through the wizard).

We launch the wizard from our `inbox` route by tapping the button in the top-right.

        this.header._eventOutput.on('more',function(){
            App.history.modifyLast({
                tag: 'StartMessageAdd'
            });
            App.history.navigate('message/add',{history: false});
        });


## Wizard Entry Point

Our `Add.js` acts like a router/controller for the wizard. On entry, we set

    this.doNotShow = true;

so that router.js defaultRoute does not try and display this PageView.

We also create the `model` we'll save later.

    this.model = new MessageModel.Message();

Create a hash to use for this wizard process

    this.wizard_hash = CryptoJS.SHA3(new Date().toString());

This allows us to use `cache: true` in wizard routes, so that going back doesn't wipe whatever page you were just on.

#### Create paths / routes / defaults

        this.wizard_startTag = 'StartMessageAdd';

List all of the possible paths/pages involved in the Wizard.

We're using `username`, `text`, and `media` pages.

        this.wizardPaths = {

            'username': {
                    cacheOptions: 'UsernameOptions',
                    title: 'Username',
                    route: 'message/add/username',
                    summaryPath: 'username'
                },
            'text' : {
                    cacheOptions: 'TextOptions',
                    title: 'Text',
                    route: 'message/add/text',
                    summaryPath: 'text',

The `post` function is used by the `on_choose` function (below). It can be useful for dynamic routes. For example, if you leave the text blank, we'll show media, otherwise we don't.

                    post: function(){
                        if(this.summary.text == ''){
                            // show Media
                            if(this.wizardRoute.indexOf('media') == -1){

The following use of the `splice` method is helpful for adding the route to visit:

                                this.wizardRoute.splice(this.wizard_current_route_index+1,0,'media');
                            }
                        } else {
                            this.wizardRoute = _.without(this.wizardRoute,'media');
                        }

                    }
                },
            'media' : {
                    cacheOptions: 'MediaOptions',
                    title: 'Media',
                    route: 'message/add/media',
                    summaryPath: 'media'
                }
        };

Now we define the path we'll take through our Route. Keep in mind that this is a dynamic list; we only splice in `media` if no `text` is set (see `post` above).

        this.wizardRoute = [
            'username',
            'text'
        ];

Set the starting position for the wizard (always `0`):

        this.wizard_current_route_index = 0;
        this.run_wizard();

    }


### run_wizard

    PageView.prototype.run_wizard = function(){

If we've ended our wizard (the last step has completed), we call our `complete` function and gtfo.

        if(this.wizard_current_route_index >= this.wizardRoute.length){
            this.complete();
            return;
        }

Determine which route/page we're showing, and set up the `App.Cache.xyz` options.

You will notice that Step 1 uses this `App.Cache.xyz`, so make sure (at each step) that the `App.Cache.xyz` is correct!

        this.currentRouteName = this.wizardRoute[this.wizard_current_route_index];
        this.currentRoute = this.wizardPaths[this.currentRouteName];

        App.Cache[this.currentRoute.cacheOptions] = {
            on_choose: this.on_choose.bind(this),
            on_cancel: this.on_cancel.bind(this),
            title: this.currentRoute.title
        };

And then we navigate to the route

> Notice that we've appended `this.wizard_hash` to the route

        App.history.navigate(this.currentRoute.route + '/' + this.wizard_hash);


### on_cancel

Canceling, or when the Back button is pressed while in a step, can result in either returning to the previous step, or will utilize `this.wizard_startTag` to completely exit the wizard.

    PageView.prototype.on_cancel = function(){
        // Go backwards

        // first one, need to reset
        if(this.wizard_current_route_index == 0){
            App.history.backTo(this.wizard_startTag);
            return;
        }

        // Go back a page/step
        this.wizard_current_route_index -= 1;

Call the `run_wizard` function again

        this.run_wizard();


### on_choose (after the page returns)

    PageView.prototype.on_choose = function(data){


Update our `this.summary.xyz` for the page.

        this.summary[this.currentRoute.summaryPath] = data;

Call the `post` function (if it exists) for the page.

        if(this.currentRoute.post){
            this.currentRoute.post.apply(this);
        }

Let's go to the next page/step!

        this.wizard_current_route_index += 1;
        this.run_wizard();

    };



## Wizard Step 1 (username)

Choosing a username doesn't currently have any validation, until actually saving.

You can quickly improve the usefulness of this example by looking at the `User/Search` PageView and allowing the user to search for an existing username, instead of typing the whole thing out.

    function PageView(options) {
        var that = this;
        View.apply(this, arguments);
        this.options = options;

See above (`App.Cache.xyz`) for how the object is created. If it doesn't exist, we just restart the app.

        if(!this.options.App.Cache.UsernameOptions){
            window.location = '';
            return;
        }

        this.options.passed = _.extend({}, App.Cache.UsernameOptions || {});


Create our layout, with header and content.

        this.layout = new HeaderFooterLayout({
            headerSize: 50,
            footerSize: 0
        });

        this.createContent();
        this.createHeader();

        this.add(this.layout);
    } // end of "function PageView"

#### Header - going "Back"

If the "Back" button is pressed, always delegate to the passed `on_cancel` function (the wizard's "controller/router" function will handle Back events).

    that.options.passed.on_cancel();


#### Content

The only difference between the content of this wizard step and most every other PageView with a form (for example, the `Signup` page) is that submitting the form simply returns the data to the wizard's controller/router.

    that.options.passed.on_choose(that.inputUsernameSurface.getValue());


## Wizard Step 2 (text)

Same as Step 1, simply adding `text` instead of a `username`.


## Wizard Step 3 (media)

This is an ignored step, but you could modify it to include picture/video upload.


## Saving (end of Wizard)

Saving occurs in the wizard's controller/router PageView, in the `complete` function.

    PageView.prototype.complete = function(ev){
        var that = this;

Inform the user that we're trying to save the new message.

        Utils.Notification.Toast('Saving...');

Gather all your data from `this.summary` and update the model we previously created

        this.model.set({
            to_username: this.summary.username,
            text: this.summary.text
        });


and save

        this.model.save()
            .then(function(newModel){

Note that we're not really handling errors here, just assuming the save was successful.

                Utils.Notification.Toast('Message Created!');
                App.history.backTo('StartMessageAdd');

            });
    };




