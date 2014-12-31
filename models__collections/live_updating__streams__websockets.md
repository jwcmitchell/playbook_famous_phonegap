# Live Updating / Streams / Websockets / Push Models


> Requires __Firebase__ or a similar provider

Instead of worrying about keeping the "latest" model representations on the client, and trying to maintain a server/client model/collection relationship, we've taken the following approach:

server-side:
- when something happens on the server that affects a user, that user is notified via `m.User.remoteRefresh`
- that function simply updates the `last-modified` (actually, `updated`) value for that user's ID on a Firebase

client-side:
- the client is listening on that single value (via `child_added`) and emits a `firebase.child_added` event on `App.Events`.
- `history.js` is listening listening for that event and its function runs the displayed `PageView.remoteRefresh`

Sample `PageView.prototype.remoteRefresh`:


    PageView.prototype.remoteRefresh = function(snapshot){
        var that = this;
        console.log('RemoteRefresh - PageView');
        Utils.RemoteRefresh(this, snapshot);
    };

`Utils.RemoteRefresh` has the default juicy bits, where we call `fetch` on the PageView's `context.model` and `context.collection` if they exist, before calling `remoteRefresh` on any subviews.

        RemoteRefresh: function(context, snapshot){

            if(context.model){
                context.model.fetch();
            }
            if(context.collection){
                context.collection.fetch(); // updates, doesn't take "pager" into account?
            }

            // emit on subviews
            if(context._subviews){
                _.each(context._subviews, function(tmpSubview){
                    if(typeof tmpSubview.remoteRefresh == "function"){
                        tmpSubview.remoteRefresh(snapshot);
                    }
                });
            }

        },


This is an OK stopgap approach to keeping up-to-date data on a client. The user will see immediate updates for whichever screen they are looking at, and navigating to new pages triggers additional `fetch`es. It works well for Live Messaging and Profile updates.


### Additional Concerns

Authentication rules should also be provided for each user's queue. See Firebase docs at: https://www.firebase.com/docs/security/custom-login.html
