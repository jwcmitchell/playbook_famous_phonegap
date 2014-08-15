# Server (node.js)

You can build a server in any language. Included with __Waiting__ is a sample server in Node.js.

### GitHub repository

https://github.com/nicholasareed/waitingapp_nodeserver


### Cloning and Building Heroku app

    git clone git@github.com:nicholasareed/waitingapp_nodeserver.git
    cd waitingapp_nodeserver
    mv config_example.json config.json
    heroku create
    npm install

You will need to create a MongoDB database and add the connection string to `config.json` (or as an environment URL). I've been using https://mongolab.com/.

Update any other variables in `config.json`.

Then run foreman:

    foreman start

Visit at `http://localhost:5000/` for the sample homepage.


### Push Notifications

Android and iOS use different systems for Push Notifications.

- more details forthcoming.
