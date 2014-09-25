# Planes / Layers

When laying out your Views and Pages, you often want one thing to be in front of another. For example, you might always want your Header to be above your Content in a `HeaderFooterLayout`.

Famo.us wants us to use Z values to manage our front/behind logic, so we combine them with the common gaming programming pattern of using planes or layers with constant names.

In our `main.js` where we define `App` you'll notice:


    Planes: {
        content: 100,
        contentTabs: 400,
        header: 500,
        footer: 500,
        popover: 1000
    }

These are some example values. Notice the "room" between numbers; this makes it easy to define something as "just above the header"

`utils.js` contains our `usePlane()` functionality. It accepts a `plane_name` option that corresponds to one of the constants from above. It returns a StateModifier with a slight z-space transform to move it to the correct Plane/Layer.

    usePlane: function(plane_name, add){
        add = add || 0;
        if(!App.Planes[plane_name]){
            // key doesn't exist, use 'content'
            plane_name = 'content';
        }
        return new StateModifier({
            transform: Transform.translate(0,0, 0.001 + (App.Planes[plane_name] + add)/1000000)
        });

    },

Notice that the Z values only change slightly; if we change them too much (moving the Z to 0.5 or similar) would affect the perspective (potentially causing blurriness).


### Usage

#### Simple usage

To put something on a Plane:

    context.add(Utils.usePlane('content')).add(myView);

#### Moving "slightly above"

    context.add(Utils.usePlane('content',1)).add(myView);

> Notice the "1" we added right after 'content'!

#### More complex usage
See example full-page usage, with a Header, Tabs, and Content on the Inbox page in the Waiting App.
