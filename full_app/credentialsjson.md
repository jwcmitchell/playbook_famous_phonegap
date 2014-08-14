# credentials.json


    {
        "server_root" : "http://30021fb0.ngrok.com/",
        "server_root" : "https://internalappserver1.herokuapp.com/",

        "GoogleAnalytics" : "UA-0000000-1"
    }

Many methods exist for switching between development/staging/production servers. I use this setup to quickly comment out (or add an underscore) to switch (`_server_root`). When parsed, last-seen is the "winner" between keys. In the above example, the `...herokuapp.com/` server would be the one used.
