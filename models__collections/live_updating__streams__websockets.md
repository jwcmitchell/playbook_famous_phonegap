# Live Updating / Streams / Websockets / Push Models


> Requires __Firebase__ or a similar provider

Instead of worrying about keeping the "latest" model representations on the client, and trying to maintain a server/client model/collection relationship, we've taken the following approach:

- when something happens on the server that affects a user, that user is notified via `m.User.remoteRefresh`
- that function simply updates the `last-modified` (actually, `updated`) value for that user's ID on a Firebase
- the client is listening on that single value (via `child_added`) and emits a `firebase.child_added` event on `App.Events`.
- `history.js` picks up the `firebase.child_added` event and runs the displayed `PageView.remoteRefresh`

Sample `PageView.prototype.remoteRefresh`:


    PageView.prototype.remoteRefresh = function(snapshot){
        var that = this;
        console.log('RemoteRefresh - PageView');
        Utils.RemoteRefresh(this, snapshot);
    };

`Utils.RemoteRefresh` has the default juicy bits, where we call `fetch` on `context.model` and `context.collection` if they exist, before calling `remoteRefresh` on any subviews.

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


### Previous Notes (to delete)

How does this work?
- rooms created per-user (like Emailbox)
- is Firebase overkill? (yeah)
- Just need to be notified of new models, changes, and to know the last __version, corret?

This will be tested/used on the Inbox PageView at first. It is a perfect candidate to be "pushed" updates.

When a collection is fetched, it would want to know when a certain model type is updated, correct?

Collection "live" status can be
- triggers a "fetch" or "pager" or something on the collection?
    - nah, just listen for an update, then do the necessary fetch/pager
    - `App.Events.on('model-added-of-type-Profile', ...`
    -

Models listen on App.Events.


if App.Events is a Firebase queue, can I choose which queue it is on? And then set permissions to access that queue?
- yes
-

Looks like I need to simply add an Auth rules for every queue (

https://www.firebase.com/docs/security/custom-login.html



one of your models has been updated, or a friend's model updated
- on server: determine everybody the action/db change affects (user_ids)
- determine the Models affected (User, Message)
- increment _version of that Model for each affected user
- basically creates a few rooms/channels/urls(firebase) for each user
- on client: connect to stream/websocket from server
- when an update occurs, we'll get the Model it affects, and update all the created local models (by checking version?)
- ...does it make sense to update all the models?
-




