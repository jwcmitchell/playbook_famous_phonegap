# index.html


There is a conflict between Cordova, require.js, and Famo.us, so we load everything in this order to ensure it works correctly. When `deviceready` is fired by cordova, we'll load require.js, which in turn loads our `main.js`.

    <html>
        <head>
            <title>HelloWorld</title>
            <meta name="viewport" content="width=device-width, maximum-scale=1, user-scalable=no" />
            <meta name="mobile-web-app-capable" content="yes" />
            <meta name="apple-mobile-web-app-capable" content="yes" />
            <meta name="apple-mobile-web-app-status-bar-style" content="black" />
            <link rel="stylesheet" type="text/css" href="src/famous/core/famous.css" />
            <link rel="stylesheet" type="text/css" href="css/ionicons.css" />
            <link rel="stylesheet" type="text/css" href="css/app.css" />

            <script type="text/javascript" src="cordova.js"></script>

            <script type="text/javascript" src="src/lib/functionPrototypeBind.js"></script>
            <script type="text/javascript" src="src/lib/classList.js"></script>
            <script type="text/javascript" src="src/lib/requestAnimationFrame.js"></script>


            <script type="text/javascript">
                var GLOBAL_onReady = false;

                // track.js customer id
                var _trackJs = {
                    customer: '...'
                };

                // Required scripts to add after deviceready
                var require_scripts = [
                    {
                        src: "src/lib/require.js",
                        attr: [{
                            "data-main" : "src/main"
                        }]
                    }
                ];

                var addScripts = function(scripts){

                    // Add scripts
                    scripts.forEach(function(script_info){

                        var script = document.createElement( 'script' );
                        script.type = 'text/javascript';

                        if(typeof(script_info) === 'string'){
                            script.src = script_info;
                        } else {
                            script.src = script_info.src;
                            if(script_info.attr){
                                script_info.attr.forEach(function(scriptAttr){
                                    Object.keys(scriptAttr).forEach(function(key){
                                        script.setAttribute(key, scriptAttr[key]);
                                    });
                                });
                            }
                        }

                        document.body.appendChild( script );

                    });

                };

                function onLoad() {

                    // browser?
                    if (!navigator.userAgent.match(/(iPhone|iPod|iPad|Android|BlackBerry)/)) {
                        // browser
                        addScripts(require_scripts);
                    }

                    // wait for deviceready from cordova
                    document.addEventListener("deviceready", function(){
                        // alert('deviceready listener fired!');
                        GLOBAL_onReady = true;
                        addScripts(require_scripts);
                        // loadGoogleMapsScript();
                    }, false);

                }
            </script>

        </head>
        <body onload="onLoad()" style="background:#666;"></body>
    </html>
