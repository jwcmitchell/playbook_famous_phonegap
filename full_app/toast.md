# Toast

We're currently using the native system-wide toast, exposed in a manner like:

    var Utils = require('utils');
    Utils.Notifications.Toast('Some message');

This causes a 2-second rectangular message box to appear at the bottom of the screen.

### Problems

Messages stack up in the history, and every message is shown for 2-seconds. If I did:

    for(var i=0;i<60;i++){
        Utils.Notification.Toast(i);
    }

I'd be seeing messages for ~ the next 2 minutes, even though it runs instantly!


### Solution

Creating a more customizable Toast that supports options such as:
- vertical stacking
- grouped Toasts (global upload progress meter)
- custom position
- delegate position to current PageView
