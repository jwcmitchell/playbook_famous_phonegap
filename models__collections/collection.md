# Collections

Collections can come in a few flavors:
- pagination
- summaries
- normal

## Pagination

This needs to be updated to the current version of the Pagination component

    var ActionCollection = Backbone.Paginator.requestPager.extend({

        model: Action,

        urlRoot: Credentials.server_root + "actions",

        // Paginator Core
        paginator_core: {
          type: 'GET',
          dataType: 'json',
          url: function(){return this.urlRoot}
        },

        // Paginator User Interface (UI)
        paginator_ui: {
          firstPage: 0,
          currentPage: 0, // start/current page
          perPage: 5,
          totalPages: 10 // gets overwritten
        },

        // Paginator Server API
        server_api: {
          '$filter': '',
          '$top': function() { return this.perPage },
          '$skip': function() { return this.currentPage * this.perPage },
          '$format': 'json',
          '$inlinecount': 'allpages',
          // '$callback': 'callback'
        },

        // Paginator Parsing
        parse: function (response) {
            this.totalPages = Math.ceil(response.total / this.perPage);
            this.totalResults = response.total;
            return response.results;
        },


        initialize: function(models, options){
            options = options || {};
            if(options.player_id){
                // Viewing actions for an individual player (what everybody would see about that Player)
                // - used for what I see about myself, and news/actions about other individual players
                this.url = this.urlRoot + '/player/' + options.player_id;
            } else {
                // Summary of actions that I care about (me, as a logged in user_id)
                if(options.type){
                  this.url = this.urlRoot + '/' + options.type;
                }
            }
        }

    });



### _id and __v instead of full model representations

A big difference between mobile and desktop apps is the __mobile connection__, both __speed__ and __reliability__. In order to make your app more responsive when loading data, you can avoid returning entire model representations and instead simply return `_id` and `__v` from your server.

If your collection only contains `_id` and `__v` it will NOT trigger a `reset` or other events (or return `success` or resolve the `.then` promise) until all of the models have been updated for the collection (using Backbone.UniqueModel) to the correct version.


