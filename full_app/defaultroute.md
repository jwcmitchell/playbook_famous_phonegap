# defaultRoute

This is the logistics of loading PageViews, determining transitions, and displaying PageViews.

It ends up switching PageViews using the previously-created `App.MainController`, `App.MainFooter`, and `App.Views.Popover`.

    var defaultRoute = function(viewName, viewPath, args, options){

        options = options ? options : {};
        if(args === undefined){
            args = viewPath;
            viewPath = viewName;
        }

        console.log('viewPath:', viewPath);

Using our `Utils.Analytics.trackRoute` function we'll immediately report the loaded page's route to our Google Analytics plugin.

        Utils.Analytics.trackRoute(window.location.hash);


We require each PageView at runtime because it is easier than listing every PageView we could potentially need.

        require(['views/' + viewPath], function(LoadedView){


You'll see `delayShowing` a few times. The logic is that we should give uncached PageViews a moment to "get ready" before displaying.

            var delayShowing = 0;


Get our PageView from the cache, if it is cached. If it isn't cached, then we go ahead and load it up, then cache if told to.

            var PageView = App.Router.Cache.get();

            var cachePath = window.location.hash;

            if(PageView === false || options.cache === false){
                // create it!
                // - first time creating it
                PageView = new LoadedView({
                    args: args,
                    App: App
                });

                // Only a pass-through?
                if(PageView.doNotShow){
                    return;
                }

                // Cache it
                App.Router.Cache.set(PageView, cachePath);

                delayShowing = 100;
            } else {
                // Already cached, use the cached version
            }

            // Cache pageview
            App.Views.currentPageView = PageView;


If the route specified it was a popover, then we use the `App.Views.Popover` Lightbox to display the PageView.

            if(options.popover === true){
                PageView.inOutTransitionPopover('showing');
                App.Views.Popover.show(PageView);
                return;
            }


If we're not a popover, then we know were going to be using our `App.MainController`.

Reset the Lightbox's transition back to its defaults, and then determine what transition we'll end up using. We reset to defaults, otherwise an updated `curve` property could be propogated across __every__ `App.MainController` transition.

            App.MainController.resetOptions();
            var transitionOptions = {};

            // Setting using default options
            console.log('Animating using Default SlideLeft');
            transitionOptions = StoredTransitions.SlideLeft;

            // See if we're going back a page
            // - use a Default "back" animation SlideRight
            var goingBack = false;
            if(App.history.isGoingBack){
                goingBack = true;
                App.history.isGoingBack = false; // reset

                console.log('Animating using BACK');
                transitionOptions = StoredTransitions.SlideRight;
            }


ViewToView checking (see ViewToView in the above section) is used to find the most-specific transition to use.

            App.Cache.LastViewName = App.Cache.LastViewName === undefined ? "" : App.Cache.LastViewName;
            var exactMatchView = App.Cache.LastViewName + ' -> ' + viewName, // highest priority
                toAnyView = App.Cache.LastViewName + ' -> *',
                fromAnyView = '* -> ' + viewName; // lowest priority

            // Extract out multiple key values
            // - todo, would allow: "* -> Login, Home -> Login" for the occurrences
            // - could also add an "!important" flag?

            // Check broastest->specific ViewToView transitions
            if(ViewToView[exactMatchView]){
                // Most specific match
                console.log('ViewToView Match: Specific:', exactMatchView);
                transitionOptions = ViewToView[exactMatchView]();
            } else if(ViewToView[toAnyView]){
                // mid
                console.log('ViewToView Match: ToAny:', toAnyView);
                transitionOptions = ViewToView[toAnyView]();
            } else if(ViewToView[fromAnyView]){
                // low
                console.log('ViewToView Match: FromAny:', fromAnyView);
                transitionOptions = ViewToView[fromAnyView]();
            }


            // Lastly, send the selected "options" to the incoming and outgoing views
            // - each View should receive

            // takes in the direction it is going (in/out) and returns an options to set (which we compare against)
            // - views are assuming that we're doing a "show" immediately following this, no promises
            // - also serves as a trigger for the view that they are doing something

Tell the "hiding" PageView first

            if(App.MainController.lastView && App.MainController.lastView.inOutTransition){
                transitionOptions = App.MainController.lastView.inOutTransition('hiding', viewName, transitionOptions, delayShowing, PageView, goingBack);
            }

Tell "showing" PageView last

            if(PageView.inOutTransition){
                transitionOptions = PageView.inOutTransition('showing', App.Cache.LastViewName, transitionOptions, delayShowing, App.MainController.lastView, goingBack);
            }

Set `transitionOptions` for the `App.MainController` Lightbox.

            App.MainController.setOptions(transitionOptions);


Update our lastView with the now-current view.

            App.MainController.lastView = PageView;

#### Switch displaying the PageViews

Notice the `delayShowing`, as well as

            Timer.setTimeout(function(){
                App.MainController.show(PageView);
            }, delayShowing);


            // Update LastViewName
            App.Cache.LastViewName = '' + viewName;

Finally, show/hide the footer depending on the latest value for `App.Views.MainFooter.route_show`. We `.hide()` it by default.

            if(App.Views.MainFooter){
                if(App.Views.MainFooter.route_show === true){
                    App.Views.MainFooter.show();
                } else {
                    App.Views.MainFooter.hide();
                }
                App.Views.MainFooter.route_show = false;
            }

        });

    };
