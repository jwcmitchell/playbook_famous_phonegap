# Auth: Login and Signup

    /waiting_nodeserver/routes/user.js

The Waiting app __requires__ a unique email for every user. It doesn't support "merging" of accounts out-of-the-box. 

### Email Signup 

The `/user/signup` API endpoint can be POST'ed to in order to create an email/password combination for a new user. 


### OAuth login (Google+ and Facebook)  

Both Google+ and Facebook follow the same authentication flow on the server:

1. Receive Google Plus or Facebook access_token via POST
2. Validates token against the service's authentication API, including fetching the email address
3. Creates a new user if that email or server id does not exist in our database 
4. Creates an access token for the user

#### Facebook Friends 

On login, a user's facebook friends will be updated, and associated connections/requests searched for and created automatically. Push Notifications will be sent out for newly-created connections. 