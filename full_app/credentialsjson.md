# credentials.json


    {
        "server_root" : "http://30021fb0.ngrok.com/",
        "server_root" : "https://nemesisserver1.herokuapp.com/",

        "GoogleAnalytics" : "UA-52387155-1"
    }

Many methods exist for switching between development/staging/production servers. I use this setup to quickly comment out (or add an underscore) to switch. In parsing, last-seen is the "winner" between keys. In the above example, the `...herokuapp.com/` server would be the one used.
