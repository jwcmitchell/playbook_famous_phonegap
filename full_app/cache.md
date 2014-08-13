# cache

We use the `window.location.hash` value to cache each view. The PageView is only cached in memory, under `App.Cache.RoutesByHash`.

    Cache : {
        get: function(options){
            var hash = window.location.hash;

            if(!App.Cache.RoutesByHash){
                App.Cache.RoutesByHash = {};
            }
            options = options || {};

            // Cached View?
            if(App.Cache.RoutesByHash[hash] != undefined){
                return App.Cache.RoutesByHash[hash];
            }

            return false;
        },
        set: function(view, hash){// Returns a cached view for this route
            hash = hash || window.location.hash;
            App.Cache.RoutesByHash[hash] = view;
            view.isCachedView = true;
            return view;
        }
    }
