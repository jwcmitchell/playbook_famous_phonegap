# Connections / Sharing 


### Todos 

- Pending/Invited, Blocked lists of people (need Tabs enabled) 
- List searching/sorting (searching friends easier) 


Most social apps need need a way of facilitating sharing and creating connections between users. The sample app provides a simple mechanism that you can expand upon, but goes a long way towards closing the viral loop for a basic app. 

### Model 

On the server we use a `Friend` model (`user_friends` table) that creates an entry for each side of the relationship. 

Schema: 

    user_id:      my_user_id,
    friend_id:    other_persons_user_id
    type:         "friend",
    active:       true

This could easily be extended to fit a "follower" model, or one that requires approval (pending invites, blocked people, etc.). 


#### _Auto-accept connections _

In our example app (and supported on the server) we have taken an __auto-accept all friends__ approach. By sending an Invite Email to a friend through our server, the relationship is automatically created. If the Invited user is not already registered, the relationship will be created when they sign up. Logins with __Facebook__ will be auto-connected with friends as they sign up. 



### View  

View file is located at: 

    /views/Friend/List.js 
    
We also include the Subview `/views/Friend/Subviews/Connected.js` (eventually will include `Pending.js`, `Blocked.js`, etc.). 
    

#### Inviting 

The `createHeader` function contains our "Invite Somebody via Email" icon/button. In it we create a simple `Prompt` popover that wants an email address and tries inviting the user via our server at the `/invite/user/email` endpoint. 


    this.headerContent.Share.on('click', function(){

        var text = 'Enter a friend\'s email to send them your Wishlist. If they sign up, you\'ll be connected',
            defaultValue = that.lastEmail || '',
            button = 'Send Invite',
            buttonCancel = 'Cancel',
            type = 'email',
            placeholder = 'Email Address';

        Utils.Popover.Prompt(text, defaultValue, button, buttonCancel, type, placeholder)
        .then(function(email){
            if(!email){
                return;
            }

            App.Data.User.inviteViaEmail(email)
            .then(function(){
                Utils.Notification.Toast('Email Invite Sent');
            })
        });

    });


#### List of Friends 

We only have one list of friends to display, but we want to extend to multiple lists eventually, so we lay the foundation by building (but now showing in this version) a `FlexibleLayout` that holds some Tabs and a `RenderController` (for our content). Notice in the `ratios` we have `[1]` and not `[true,1]` because we are excluding the Tabs! 

List location (Subview): 

    /views/Friend/Subviews/Connected.js 
    

This is our standard multi-item list that works with a collection to display all our friends. When clicked, we'll visit a friend's profile. 





