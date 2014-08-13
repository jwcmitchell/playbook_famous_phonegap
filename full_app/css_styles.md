# CSS Styles



Because we're still simply using JS/HTML/CSS, we can do all of our styling in CSS!

At the top of our index.html, we include:

    <link rel="stylesheet" type="text/css" href="src/famous/core/famous.css" />
    <link rel="stylesheet" type="text/css" href="css/ionicons.css" />
    <link rel="stylesheet" type="text/css" href="css/app.css" />

- famous CSS styles
- Ion Icons fonts (great icons)
- our app's CSS

### Inline Styles

Famo.us does offer the option of doing "inline" styles like:

    var s = new Surface({
        content: "hello",
        size: [undefined, undefined],
        properties: {
            fontSize: "14px",
            lineHeight: "18px",
            color: "red"
        }
    });

Don't get in the habit of doing this! You'll quickly enough need to copy-paste some code, and it is MUCH easier dealing with CSS styles. You should almost always leave the `properties` out and instead use the `classes` object:

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

