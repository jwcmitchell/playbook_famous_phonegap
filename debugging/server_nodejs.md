# API Server and Website 

We've included with __Waiting__ a sample server written in Node.js. It supports all of the features the app needs including: 

- Homepage, Support, Privacy Policy (todo) 
- Email signup and login 
- OAuth via Google+ and Facebook 
- Emails with local or remote templates
- Push Notifications to Android and iOS 
- Pagination for collection fetches 

It can easily be served from Heroku (instructions included). 


### GitHub repository

https://github.com/FamousMobileApps/waitingapp_nodeserver


### Clone and Setup 

    git clone git@github.com:FamousMobileApps/waitingapp_nodeserver.git
    cd waitingapp_nodeserver
    mv config_example.json config.json

You will need to create a MongoDB database and add the connection string to `config.json` (or as an environment URL). I've been using https://mongolab.com/ for hosting (500mb free sandbox plan).

Update other variables in `config.json`. See __Chapter 3.1: External Services__ for additional accounts you may want to sign up for to enable various APIs. 


### Develop Locally 

First we'll create our Heroku server application (this adds the `heroku` remote path to your local git repo) 

    heroku create
    
then install dependencies via npm (it is checking the server app's `package.json` file)

    npm install
    
then run foreman (a Heroku-built local server): 

    foreman start

Finally visit `http://localhost:5000/` for the sample homepage! 


### Push to Heroku 

In the above step you created the Heroku server app, so now we just need to push our local master branch to Heroku:   

    git push heroku master

Heroku will build your app, and you can run `heroku logs --tail` to see the output. 








