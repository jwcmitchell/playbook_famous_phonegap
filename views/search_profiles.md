# Search Profiles

We'll be leaving out the actual user-to-user (connecting friends) mechanics, but lets add a way of finding and viewing other user's profiles.


## Route

    'users/search' : function(){
        defaultRoute('UsersSearch', 'User/Search', arguments, { cache: true });
    },

## Init

Totally standard.

    function PageView(options) {
        var that = this;
        View.apply(this, arguments);
        this.options = options;

        // Models
        this.loadModels();

        // create the layout
        this.layout = new HeaderFooterLayout({
            headerSize: 50,
            footerSize: 0
        });

        this.createContent();
        this.createHeader();

        // Attach to render tree
        this.add(this.layout);

    }


## Content

The layout for our content is a simple `HeaderFooterLayout`. This helps us achieve a  fixed SearchHeader and a scrollable ResultsBody, while still maintaining a consistent overall Header (with Back button) aesthetic.

Our SearchHeader is simply an `InputSurface`, where we also listen for `keyup` events to trigger searches.

    this.SearchHeader = new InputSurface({
        type: 'text',
        size: [undefined, undefined],
        placeholder: 'username',
        name: 'search_username',
        value: ''
    });
    this.SearchHeader.on('keyup', function(){
        that.search_username();
    });


#### search_username function

When triggerd by a `keyup` event on `this.SearchHeader`, we re-initialize the collection with the new (partial) username, and make a request to update with usernames that match.

We also prevent late-arriving (expired, superceded) username searches from affecting the collection.

    var val = this.SearchHeader.getValue();
    this.collection.initialize([],{
        type: 'username',
        username:  val
    });
    if(val == ''){
        this.collection.set([]);
        this.collection.trigger('reset');
    } else {
        this.collection.fetch()
            .then(function(){
                if(val == that.SearchHeader.getValue()){
                    that.collection.trigger('reset');
                }
            });
    }


#### rebuild_username_list

Triggered by a `reset` on our collection.

Responsible for resorting all the views in our `ScrollView`. Instead of simply re-writing the whole display, we're deteriming which views to show/hide, and prevent unnecessary Surface/View creations.


### addOne

Called by `rebuild_username_list` when a username's model does not yet have a View/Surface created in `this.contentScrollView
.Views`.



