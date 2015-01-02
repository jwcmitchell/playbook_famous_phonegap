# Profile View

> Currently outdated (todo: update to LayoutBuilder) 

The Profile View is broken up into two Views. 

### `router.js`

    'profile/view' : function(){
        defaultRoute('ProfileDefault', 'Profile/Default', arguments, {cache: false});
    },
    'profile/view(/:id)' : function(){
        defaultRoute('ProfileView', 'Profile/View', arguments, {cache: true});
    },

We have two routes so that a redirect to `profile/view` results in the resolution of the correct (logged in user) ID to show.   

### `views/Profile/Default` 

This is not much of a view. It simply determines who the logged-in user is, and redirects to their `profile/view/:id` route. 

### `views/Profile/View` 

This is a basic Profile View
- view/change profile picture
- change name 

Doubles as a Dashboard too, we can edit ourselves from here
- because we're using a promise, and getting our User._id, if nobobody else is specified


MainFooter will be shown, with 2 options:
- Profiles
- Inbox
