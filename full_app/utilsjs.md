# utils.js

I wanted a more central place to handle Utility functions that I was repeatedly calling. `utils.js` serves that purpose.

    var Utils = {


### CheckFlag / PostFlag

These functions are great for triggering one-time actions for a user, such as a Help popover when then visit a page for the first time, or when you've added a new feature to an app.

Requires the necessary DB routes.

        CheckFlag: function(flag){
            var def = $.Deferred()
            App.Data.User.populated().then(function(){
                if(App.Data.User.get('flags.' + flag)){
                    def.reject();
                } else {
                    def.resolve(true);
                }
            });
            return def.promise();
        },
        PostFlag: function(flag, value){
            var tmpData = {
                flags: {}
            };
            tmpData.flags[flag] = value;
            App.Data.User.set('flags.' + flag, value);
            return $.ajax({
                url: Credentials.server_root + 'user/flag',
                method: 'PATCH',
                data: tmpData,
                error: function(){
                    Utils.Notification.Toast('Failed Flag');
                    debugger;
                },
                success: function(){
                    // awesome
                }
            });
        },


Sample usage:

        var localFlag = 'highlights/home';
        Utils.CheckFlag(localFlag).then(function(){
            // popover
            alert('this will only happen once!');
            // update flag
            Utils.PostFlag(localFlag, true);
        });


### QuickModels


Here we provide a simple way to return a single model instance.

        QuickModel: function(ModelName, model_file){
            if(model_file == undefined){
                model_file = ModelName.toLowerCase();
            }

            var defer = $.Deferred();

            require(['models/' + model_file], function(Model){

                if(!id || id.length < 1){
                    defer.reject();
                    return;
                }

                var newModel = new Model[ModelName]({
                    _id: id
                });
                if(newModel.hasFetched){
                    // Already fetched
                    defer.resolve(newModel);
                } else {
                    newModel.fetch({prefill: true});
                    newModel.populated().then(function(){
                        defer.resolve(newModel);
                    });
                }
            });

            return defer.promise();

        },


### Replacing surfaces with resolved models

aka __dataModelReplaceOnSurface__ is a handy way of adding Surfaces with content tags that get filled in as the model populates.

        dataModelReplaceOnSurface : function(Surface){
            // Load driver name and image
            // console.log(context);
            if(typeof App.Cache != typeof {}){
                App.Cache = {};
            }

            var context = $('<div/>').html(Surface.getContent());

            App.Cache.ModelReplacers = App.Cache.ModelReplacers || {};

            context.find('[data-replace-model]').each(function(index){
                var elem = this;
                var model = $(elem).attr('data-replace-model'),
                    id = $(elem).attr('data-replace-id'),
                    field = $(elem).attr('data-replace-field'),
                    target = $(elem).attr('data-target-type'),
                    pre = $(elem).attr('data-replace-pre') || '',
                    cachestring = 'cached_display_v1_' + model + id + field;

            });



        },


This lets us do something like:

    var s = new Surface({
        content: '<span data-replace-id="'+player_id+'" data-replace-model="Player" data-replace-field="name">&nbsp;</span>'
    });

    Utils.dataModelReplaceOnSurface(s);

and after my "Player" is resolved (probably cached) it will replace the corresponding fields.


### Locale

Localization / Globalization

        Locale: {

            normalize: function(value){
                var tmpValue = value.toString().toLowerCase();
                var allowed_locales = {
                    'en' : ['undefined','en','en_us','en-us']
                };

                var normalized = false;
                _.each(allowed_locales, function(locales, locale_normalized){
                    if(locales.indexOf(tmpValue) !== -1){
                        normalized = locale_normalized;
                    }
                });

                return normalized;
            }

        },

### Analytics

Called from `device_ready.js.

        Analytics: {
            init: function(){
                try {
                    App.Analytics = window.plugins.gaPlugin;
                    App.Analytics.init(function(){
                        // success
                        console.log('Success init gaPlugin');
                    }, function(){
                        // error
                        if(App.Data.usePg){
                            console.error('Failed init gaPlugin');
                            Utils.Notification.Toast('Failed init gaPlugin');
                        }
                    }, Credentials.GoogleAnalytics, 30);
                }catch(err){
                    if(App.Data.usePg){
                        console.error(err);
                    }
                    return false;
                }

                return true;

            },

            trackRoute: function(pageRoute){
                // needs to wait for Utils.Analytics.init()? (should be init'd)
                try{
                    App.Analytics.trackPage( function(){
                        // success
                        console.log('success');
                    }, function(){
                        // error
                        console.error('error');
                    }, 'nemesis.app/' + pageRoute);
                }catch(err){
                    if(App.Data.usePg){
                        console.error('Utils.Analytics.trackPage');
                        console.error(err);
                    }
                }
            }
        },


### Take Picture

We use a consistent image size and quality across the app (though you can provide options to combine with).

Requires `options.sourceType` to be either `Camera.PictureSourceType.PHOTOLIBRARY` or `Camera.PictureSourceType.CAMERA`.

        takePicture: function(camera_type_short, opts, successCallback, errorCallback){
            // Take a picture using the camera or select one from the library
            var that = this;

            var options = {
                quality: 80,
                targetWidth: 1000,
                targetHeight: 1000,
                destinationType: Camera.DestinationType.FILE_URI,
                encodingType: Camera.EncodingType.JPEG,
                sourceType: null // Camera.PictureSourceType.CAMERA
            };

            switch(camera_type_short){
                case 'gallery':
                    options.sourceType = Camera.PictureSourceType.PHOTOLIBRARY;
                    break;

                case 'camera':
                default:
                    // camera
                    options.sourceType = Camera.PictureSourceType.CAMERA;
                    break;
            }

            navigator.camera.getPicture(successCallback, errorCallback, options);

            return false;

        },

### HTML Encoding


By default, you can use

    S( "<h1>Bad HTML HERE!</h1>" );

for encoding/sanitizing user input before displaying. `S()` maps to the `Utils.hbSanitize` function.

        hbSanitize: function(string){
            // copied from Handlebars.js sanitizing of strings
            var escape = {
                "&": "&amp;",
                "<": "&lt;",
                ">": "&gt;",
                '"': "&quot;",
                "'": "&#x27;",
                "`": "&#x60;"
            };

            var badChars = /[&<>"'`]/g;
            var possible = /[&<>"'`]/;

            function escapeChar(chr) {
                return escape[chr] || "&amp;";
            }

            if (!string && string !== 0) {
              return "";
            }
            string = "" + string;

            if(!possible.test(string)) { return string; }
            return string.replace(badChars, escapeChar);
        },


We also have the option of using jQuery to do our sanitization.

        htmlEncode: function(value){
          //create a in-memory div, set it's inner text(which jQuery automatically encodes)
          //then grab the encoded contents back out.  The div never exists on the page.
          return $('<div/>').text(value).html();
        },



### Notifications

        Notification: { ...

#### Toast

Defaults to using a native plugin for displaying a system-wide Toast dialog box, with positioning (top, middle, bottom).

You can also use the `App.MainToast.show('text')` to display a toast for a moment.

            Toast: function(msg, position){
                // attempting Toast message
                // - position is ignored
                var defer = $.Deferred();
                try {
                    window.plugins.toast.showShortBottom(msg,
                        function(a){
                            defer.resolve(a);
                        },
                        function(b){
                            defer.reject(b);
                        }
                    );
                }catch(err){
                    console.log('TOAST failed');
                }
                return defer.promise();
            }
        },


### Base64 libraries

        /**
        *
        *  Base64 encode / decode
        *  http://www.webtoolkit.info/
        *
        **/
        Base64: {

            // private property
            _keyStr : "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=",

            // public method for encoding
            encode : function (input) {
                var output = "";
                var chr1, chr2, chr3, enc1, enc2, enc3, enc4;
                var i = 0;

                input = this._utf8_encode(input);

                while (i < input.length) {

                    chr1 = input.charCodeAt(i++);
                    chr2 = input.charCodeAt(i++);
                    chr3 = input.charCodeAt(i++);

                    enc1 = chr1 >> 2;
                    enc2 = ((chr1 & 3) << 4) | (chr2 >> 4);
                    enc3 = ((chr2 & 15) << 2) | (chr3 >> 6);
                    enc4 = chr3 & 63;

                    if (isNaN(chr2)) {
                        enc3 = enc4 = 64;
                    } else if (isNaN(chr3)) {
                        enc4 = 64;
                    }

                    output = output +
                    this._keyStr.charAt(enc1) + this._keyStr.charAt(enc2) +
                    this._keyStr.charAt(enc3) + this._keyStr.charAt(enc4);

                }

                return output;
            },

            // public method for decoding
            decode : function (input) {
                var output = "";
                var chr1, chr2, chr3;
                var enc1, enc2, enc3, enc4;
                var i = 0;

                input = input.replace(/[^A-Za-z0-9\+\/\=]/g, "");

                while (i < input.length) {

                    enc1 = this._keyStr.indexOf(input.charAt(i++));
                    enc2 = this._keyStr.indexOf(input.charAt(i++));
                    enc3 = this._keyStr.indexOf(input.charAt(i++));
                    enc4 = this._keyStr.indexOf(input.charAt(i++));

                    chr1 = (enc1 << 2) | (enc2 >> 4);
                    chr2 = ((enc2 & 15) << 4) | (enc3 >> 2);
                    chr3 = ((enc3 & 3) << 6) | enc4;

                    output = output + String.fromCharCode(chr1);

                    if (enc3 != 64) {
                        output = output + String.fromCharCode(chr2);
                    }
                    if (enc4 != 64) {
                        output = output + String.fromCharCode(chr3);
                    }

                }

                output = this._utf8_decode(output);

                return output;

            },


#### UTF-8 encoding/decoding

            // private method for UTF-8 encoding
            _utf8_encode : function (string) {
                string = string.replace(/\r\n/g,"\n");
                var utftext = "";

                for (var n = 0; n < string.length; n++) {

                    var c = string.charCodeAt(n);

                    if (c < 128) {
                        utftext += String.fromCharCode(c);
                    }
                    else if((c > 127) && (c < 2048)) {
                        utftext += String.fromCharCode((c >> 6) | 192);
                        utftext += String.fromCharCode((c & 63) | 128);
                    }
                    else {
                        utftext += String.fromCharCode((c >> 12) | 224);
                        utftext += String.fromCharCode(((c >> 6) & 63) | 128);
                        utftext += String.fromCharCode((c & 63) | 128);
                    }

                }

                return utftext;
            },

            // private method for UTF-8 decoding
            _utf8_decode : function (utftext) {
                var string = "";
                var i = 0;
                var c2 = 0,
                    c1 = c2,
                    c = c1;

                while ( i < utftext.length ) {

                    c = utftext.charCodeAt(i);

                    if (c < 128) {
                        string += String.fromCharCode(c);
                        i++;
                    }
                    else if((c > 191) && (c < 224)) {
                        c2 = utftext.charCodeAt(i+1);
                        string += String.fromCharCode(((c & 31) << 6) | (c2 & 63));
                        i += 2;
                    }
                    else {
                        c2 = utftext.charCodeAt(i+1);
                        c3 = utftext.charCodeAt(i+2);
                        string += String.fromCharCode(((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63));
                        i += 3;
                    }

                }

                return string;
            }

        },


### Logout

Logging out can get tricky, so we try and handle all the use cases here. Clear caches, unregister from Push Notifications, reset the $.ajax headers.

        logout: function(){

            // Reset caches
            App.Cache = {}; //_.defaults({},App.DefaultCache);
            // console.log(App.Cache);
            App.Data = {
                User: null,
                Players: null // preloaded
            };
            localStorage.clear();

            // Unregister from Push
            console.info('Unregisering from PushNotification');
            try {
                window.plugins.pushNotification.unregister();
            }catch(err){
                console.error('Failed unregistering from PushNotification');
            }

            // Reset credentials
            $.ajaxSetup({
                headers: {
                    'x-token' : ''
                }
            });

            // // Try and exit on logout, because we cannot effectively clear views
            // try {
            //     navigator.app.exitApp();
            // } catch(err){
            // }

            // try {
            //     navigator.device.exitApp();
            // } catch(err){
            // }

            // Last effort, reload the page
            // - probably lose all native hooks
            // console.log(window.location.href);
            window.location = window.location.href.split('#')[0] + '#login';

            return true;
        },


### GPS functions, updating and haversine

        updateGpsPosition: function(){

            try {
                navigator.geolocation.getCurrentPosition(function(position){
                    console.log('coords');
                    console.log(position.coords);
                    App.Events.emit('updated_user_current_location');
                    App.Cache.geolocation_coords = position.coords;

                }, function(err){
                    console.log('GPS failure');
                    console.log(err);
                });
            } catch(err){
                return false;
            }

            return true;

        },

        haversine : function(lat1,lat2,lon1,lon2){

            // Run haversine formula
            var toRad = function(val) {
               return val * Math.PI / 180;
            };

            // var lat2 = homelat;
            // var lon2 = homelon;
            // var lat1 = lat;
            // var lon1 = lon;

            var R = 3959; // km=6371. mi=3959

            //has a problem with the .toRad() method below.
            var x1 = lat2-lat1;
            var dLat = toRad(x1);
            var x2 = lon2-lon1;
            var dLon = toRad(x2);
            var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                            Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
                            Math.sin(dLon/2) * Math.sin(dLon/2);
            var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
            var d = R * c;

            return d;

        },


### Push Notifications



        process_push_notification_message : function(e){
            // Processing a single Push Notification
            // - not meant for handling a bunch in a row

We are told if the notification was received while the app was being looked at (imagine when somebody instantly accepts a friend request).

            if (e.foreground) {
                switch(e.payload.type){
                    // no case: statements yet
                    case 'default':
                        // nothing
                        console.log('default');
                    default:
                        break;
                }


If the notification contains a soundname, play it.

                // var my_media = new Media("/android_asset/www/"+e.soundname);
                // my_media.play();

If we launched and the app wasn't in the foreground (running), we could do something else.

            } else {
                // Launched because the user touched a notification in the notification tray.
            }

We can now run our default actions based on the payload of the Push Notification. You don't have to worry about actually registering, receiving, etc. from here, that is handled in `device_ready.js`

            // Default actions to follow

            // Is there a URL we should be visiting?
            switch(e.payload.type){
                case 'url':
                    // Visit an internal url
                    App.history.navigate(e.payload.url);
                    break;

                default:
                    // Unknown type
                    // - don't do anything
                    alert('Unable to process Push Notification');
                    break;
            }

        },


### Random Functions

#### slugToCamel


        slugToCamel: function (slug) {
            var words = slug.split('_');

            for(var i = 0; i < words.length; i++) {
              var word = words[i];
              words[i] = word.charAt(0).toUpperCase() + word.slice(1);
            }

            return words.join(' ');
        },

#### Random integer

        randomInt: function(min, max){
            return Math.floor(Math.random() * (max - min + 1)) + min;
        },

#### Fixed int or none

        toFixedOrNone: function(val, len){
            var tmp = parseFloat(val).toFixed(len).toString();
            if (parseInt(tmp, 10).toString() == tmp){
                return isNaN(tmp) ? '--' : parseInt(tmp, 10).toString();
            }
            return isNaN(tmp) ? '--' : tmp;
        },



