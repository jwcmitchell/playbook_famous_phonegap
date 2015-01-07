# CSS Styles



Because we're still simply using JS/HTML/CSS, we can do all of our styling in CSS!

At the top of our index.html, we include:

    
    <link rel="stylesheet" type="text/css" href="src/famous/core/famous.css" />
    <link rel="stylesheet" type="text/css" href="css/ionicons.css" />
    <link rel="stylesheet" type="text/css" href="css/spectrum.css" />
    <link rel="stylesheet" type="text/css" href="css/bootflat.css" />
    <link rel="stylesheet" type="text/css" href="css/rrssb.css" />

    <link rel="stylesheet" type="text/css" href="css/general.css" />
    <link rel="stylesheet" type="text/css" href="css/app.css" />

- famo.us' CSS styles 
- ion icons fonts (great icons) 
- spectrum, bootflat, and rrssb for some default styles
- our app's general CSS (lists, footers, etc.) 
- our app's more specific CSS (landing page, etc.) 

### Inline Styles

Famo.us does offer the option of doing "inline" styles like:

    var s = new Surface({
        content: "hello",
        size: [undefined, undefined],
        properties: {
            fontSize: "14px",
            "line-height": "18px",
            color: "red"
        }
    });

Do not get in the habit of doing this! Eventually you will need to copy-paste some code, and it is MUCH easier dealing with CSS styles. You should almost always leave the `properties` out and instead use `classes`:

    var s = new Surface({
        content: "hello",
        size: [undefined, undefined],
        classes: ["my-class-here"]
    });

    // and then in app.css
    .my-class-here {
        font-size: 14px;
        line-height: 18px;
        color: red;
    }

