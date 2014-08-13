# Live Updating / Streams / Websockets / Push Models

---

> # stub

---

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




