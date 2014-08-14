# general

### Global `App` variable

    // Data store
    App = {
        t: null, // for translation
        Flags: {},
        MainContext: null,
        MainController: null,
        Events: new EventHandler(),
        Config: null, // parsed in a few lines, symlinked to src/config.xml
        ConfigImportant: {},
        BackboneModels: _.extend({}, Backbone.Events),
        Router: null,
        Views: {
            MainFooter: null
        },
        Analytics: null,
        DefaultCache: tmpDefaultCache,
        Cache: _.defaults({},tmpDefaultCache),
        Data: {
            User: null,
            Players: null // preloaded
        },
        Defaults: {
            ScrollView: {
                friction: 0.001,
                drag: 0.0001,
                edgeGrip: 0.5,
                edgePeriod: 500, //300,
                edgeDamp: 1,
                speedLimit: 2

                // friction: 0.0001, // default 0.001
                // edgeGrip: 0.05, // default 0.5
                // speedLimit: 2.5 // default 10
            },
            Header: {
                size: 60
            },
            Footer: {
                size: 0
            }
        },
    };

### Parse config.xml

The version number parsed out of `config.xml` is used for analytics and displayed on the Settings page.

    var ConfigXml = require('text!config.xml');
    App.Config = $($.parseXML(ConfigXml));
    if(App.Config.find("widget").get(0).attributes.id.value.indexOf('.pub') !== -1){
        App.Prod = true;
        App.ConfigImportant.Version = App.Config.find("widget").get(0).attributes.version.value;
    }

### Analytics

Load Google Analytics (cordova plugin).

    // Google Analytics Plugin
    Utils.Analytics.init();

### Device Ready init

    // Run DeviceReady actions
    App.DeviceReady = DeviceReady;
    App.DeviceReady.init();

Check out the device_ready.js article/chapter (below, not this same page).

### Localization / Globalization

We initialize our localization efforts by determining the last-used language, or by trying to guess what language the user would like to use based on their phone's preferences (using the Globalization plugin).

    App.Cache.Language = localStorage.getItem('language_v1');
    var LanguageDef = $.Deferred();
    if(!App.Cache.Language || App.Cache.Language.length <= 0){
        App.Cache.Language = 'en';
        try {
            navigator.globalization.getLocaleName(
                function (locale) {
                    // Set the locale
                    // - should validate we support this locale!
                    var localeNormalized = Utils.Locale.normalize(locale.value);
                    if(localeNormalized !== false){
                        App.Cache.Language = localeNormalized;
                    }
                    LanguageDef.resolve();
                },
                function () {
                    alert('Error getting locale');
                }
            );
        }catch(err){
            // use default
            LanguageDef.resolve();
        }
    } else {
        // Have the language already
        LanguageDef.resolve();
    }

    // Localization
    LanguageDef.promise().then(function(){
        $.i18n.init({
            lng: App.Cache.Language, //'ru',
            ns: {namespaces: ['ns.common'], defaultNs: 'ns.common'},
            useLocalStorage: false,
            debug: true
        },function(t){
            // localization initialized
            App.t = t;

            ... code continues here...

        });


### Initialize Router

    App.Router = require('router')(App);


### Hammer.js for swipes

Famo.us should be able to handle this, but for now I'm continuing to use Hammer.js to handle basic "swipe" gestures.






