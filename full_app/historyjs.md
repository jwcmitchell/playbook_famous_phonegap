# history.js

Note that we're continuing to use Backbone's route handling via hash fragments, but simply not managing history using the browser's native implementation. We do this because we wanted to add a few useful history features that the browser makes difficult.

### `navigate`

This is the meat and potatoes of the navigation. Other functions like `.back` end up calling this function.

In the app, you'll use `App.history.navigate(...);`.


        historyObj.navigate = function(path, opts, back, reloadCurrent){

            console.log('historyObj.navigate: path:', path, arguments);

            opts = opts || {};

            opts = _.defaults(opts, {
                history: true, // add to history
                group: null,
                tag: null
            });

First we'll see if this was a "going back" type of navigation. If it is, we pop out the last entry in our history, and `navigate` to it using the same arguments as it was original called. We also set a `yes, I'm going back` variable that we'll pick up and reset in a moment.

            if(back === true){
                // Get the last entry, see if we can go back to it

                // Remove the "current entry"
                // - reload the last one (after pop'ing it out too)
                var currentArgs = historyObj.data.pop();
                console.log(currentArgs);

                var lastArgs = historyObj.data.pop();
                console.log('lastArgs', lastArgs);

Sometimes, things break or you do an `eraseUntilTag` for a tag that doesn't exist. In that instance, we want to make sure the user isn't left in limbo, so we just head to our "favorite" route.

                if(lastArgs === undefined || !lastArgs || lastArgs.length < 1 || lastArgs[0] == ''){
                    // No last arguments exist
                    Utils.Notification.Toast('Exiting App');
                    console.error('exiting app');
                    historyObj.navigate('dash');
                    return;
                }


                historyObj.isGoingBack = true;
                console.log('isGoingBack==true');
                historyObj.navigate.apply(HistoryContext, lastArgs);

                return;
            }

Sometimes you'll need (especially in the case of popovers, where we set `{history: false}`) to simply reload the current page. This works just like `back` but doesn't erase the current entry, it just `navigate`s to the existing page.

            if(reloadCurrent === true){
                // Reload the currently-in-place history event
                // - should be used when you want to go "back" from a history:false page

                // pop it out from the array, it'll get popped back in
                var lastArgs = historyObj.data.pop();
                console.log('lastArgs', lastArgs);

                if(lastArgs === undefined || !lastArgs || lastArgs.length < 1 || lastArgs[0] == ''){
                    // No last arguments exist
                    Utils.Notification.Toast('Exiting App');
                    console.error('Exiting app, reloadCurrent failed');
                    historyObj.navigate('dash');
                    return;
                }

                historyObj.isGoingBack = true; // yes, keep as "is going back" from whatever is currently displayed
                historyObj.navigate.apply(HistoryContext, lastArgs);

                return;
            }


Some routes (like popovers or wizard-starters) don't need or want to have a place in the history stack, so by passing `{history:false}` they can avoid being added.

            if(opts.history == true){
                historyObj.data.push([
                    path, opts, back
                ]);
            }

We're done modifying/redirecting our route request, so now we use Backbone to trigger a hash change event.

            Backbone.history.navigate(path, {trigger: true, replace: true});


        };


### `preload` 

Preload a route 

> You probably want to have {cache: true} set for the route! 

    historyObj.preload = function(path){

        App.Cache.PreloadNextPage = true;
        App.BackboneEvents.once('defaultRouteLoaded:#' + path, function(){
            Backbone.history.navigate('preloaded:' + path, {trigger: true, replace: true});
        });
        Backbone.history.navigate(path, {trigger: true, replace: true});

    };
    
    
### `back`

Just a wrapper for `navigate`.

    historyObj.back = function(opts){
        historyObj.navigate(null, opts, true);
    };


### `reloadCurrent`

Just a wrapper for `navigate`.

    historyObj.reloadCurrent = function(opts){
        historyObj.navigate(null, opts, false, true);
    };


### `modifyLast`

Used for adding tags to the last history entry. Use like `App.history.modifyLast({tag: 'PostStart'});`.

In `modifyLast` you'll notice that `tag`s are stored in an array, so you can have multiple tags on one history item.

        historyObj.modifyLast = function(opts){
            var args = historyObj.data[historyObj.data.length - 1];
            if(!args){ args = []; }
            if(args.length < 1){
                // no args
            } else if(args.length < 2){
                // no options exist anyways
                args[1] = opts;
                historyObj.data[historyObj.data.length - 1] = args;
            } else {
                console.info('modifyLast2', opts.tag);
                if(opts.tag){
                    // add tag to array of tags
                    var currentTag = historyObj.data[historyObj.data.length - 1][1].tag;
                    if(currentTag == undefined || currentTag == null || currentTag == false){
                        currentTag = [];
                    }
                    if(typeof currentTag == "string"){
                        currentTag = [currentTag];
                    }
                    // add to array
                    currentTag.push(opts.tag);
                    historyObj.data[historyObj.data.length - 1][1].tag = currentTag;

                    delete opts.tag
                }

                // altering anything but the 'tag'
                historyObj.data[historyObj.data.length - 1][1] = _.defaults(historyObj.data[historyObj.data.length - 1][1],opts);

            }

        };


### `backTo`

We'll erase all the history up until a `tag` matches, and then visit that route (after removing it from the history, but then it immediately gets re-added when navigated to).

        historyObj.backTo = function(tag, opts){
            historyObj.eraseUntilTag(tag, false);

            var lastArgs = historyObj.data.pop(); // get last
            historyObj.navigate.apply(RouterContext, lastArgs);
        };



### `eraseUntilTag`

Erase all the history up to (and if `eraseLast===true`) and including a specified tag. This is especially useful when existing a Wizard and you want to remove the entire process from the history.

Real world example: When adding a Post with an image, or other piece of content in your app, the user flow could be:

`dash` > `post/add` > `post/add/image` > `post/add/confirmAndSave` > [done] > `post/view/:id`

If the user presses "Back" from the `post/view/:id` route, they'd expect to back to `dash`, not `post/add/confirmAndSave`, correct? You can easily do this by calling `post/add` like

    App.history.modifyLast({
        tag: 'PostStart'
    });
    App.history.navigate('post/add');

and when you complete the Post, simply erase using

    App.history.eraseUntilTag('PostStart');

> Notice that we didn't pass `true` also, otherwise it would erase the history entry for whatever PageView launched the Post starting


        historyObj.eraseUntilTag = function(tag, eraseLast){
            // Erase entries up until the last tag
            // - and including the last tag! (if included, by default, ERASE)
            eraseLast = eraseLast === undefined ? true : (eraseLast ? true : false);

            historyObj.data.reverse();

            var continueErasing = true;
            historyObj.data = _.filter(historyObj.data, function(args){
                // check options for tag that matches
                if(continueErasing !== true){
                    return true;
                }
                if(args.length < 2){
                    return false;
                }
                if(!args[1].tag){
                    return false;
                }
                if(typeof args[1].tag == "string" && args[1].tag != tag){
                    return false
                }
                if(typeof args[1].tag === typeof [] && args[1].tag.indexOf(tag) === -1){
                    return false;
                }
                continueErasing = false;
                if(eraseLast === true){
                    return false;
                } else {
                    return true;
                }

            });

            historyObj.data.reverse();
        };





