# Gotchas

### What is a layman's description of famo.us? What is famo.us actually doing?

Famo.us tries to take away the things that a browser is bad at (rendering html, simplistically), while taking advantage of browser benefits (webkit CSS 3d transforms are hardware accelerated!). If you use Chrome Dev Tools to inspect the sample app, you'll notice the `<div>`s only go 2-3 levels deep. Famo.us creates a "virtual DOM" and re-uses the divs on the page. This means you can NEVER use jQuery to reliably query the DOM!

Famo.us uses a scene graph (http://en.wikipedia.org/wiki/Scene_graph), basically a game engine that renders to the screen at (a target) 60 frames-per-second. At 60 FPS, it means that every 16 milliseconds, Famo.us will update its virtual DOM by calling many of the `render` methods on famo.us components, and then render the result to the browser.

Keep in mind that famo.us can be used in many different ways. If you're familiar with Angular.js, check out those integrations!


Famo.us maintains a list of common pitfalls/gotchas as well:
http://famo.us/guides/pitfalls

Following are the biggest issues I've encountered when writing app-level code.

### FastClick issues (double-click of elements)

This seems to manifest itself when writing blocking code, such as for an `alert()`.

    mySurface.on('click', function(){ alert(1); });

will result in _2_ alert messages instead of 1. Until a fix is written, you can easily add a `Timer` that waits a moment and allows the native `click` event to be suppressed by Famo.us:

    mySurface.on('click', function(){
        Timer.setTimeout(function(){
            alert(1);
        }, 200);
    });

### Sizing Issues (true, 0)

When creating some Views, such as a `SequentialLayout`, Famo.us has a rough time ordering `Surfaces` if they have a height/width size of `true` or `0` (in the same direction as the layout: a `size:[100,true]` on `direction:1` causes problems).

A common pattern around this looks like:

    var mySeq = new SequentialLayout({
        direction: 1 // vertical, "Y"
    });
    mySeq.Views = [];

    var myView = new View();
    myView.Surface = new Surface({
        content: 'Content',
        size: [undefined, true]
    });
    myView.getSize = function(){
        return [undefined, myView.Surface._size ? myView.Surface._size[1] : undefined]
    };
    myView.add(myView.Surface);

    mySeq.Views.push(myView);

    mySeq.sequenceFrom(mySeq.Views);


This makes it easy to also add StateModifiers to the Surface/View:

Instead of

    myView.add(myView.Surface);

we create a new `StateModifier` and add it to the RenderTree above our Surface.

    myView.StateMod = new StateModifier();
    myView.add(myView.StateMod).add(myView.Surface);

Now we can easily modify the position, scale, opacity, etc. of the Surface.


### FrontMod (Z-space transforms)

A common problem you'll encounter is one Surface being "on top of" or "in front" of another Surface, causing clicks to fail, or the "behind" Surface to not be visible.

Z-space positioning is affected by 3 things:
- Render order of Surfaces
- Famo.us Transforms
- CSS z-index

If you have overlapping Surfaces, always use Transforms to get it resolved. For example:

    var SlightlyInFrontMod = new Modifier({
        // move forward in z-space by 0.0001
        transform: Transform.translate(0,0,0.0001)
    });
    this.someView.add(SlightlyInFrontMod).add(this.someView.Surface);
    this.someView.add(this.anotherSurfaceThatWillBeBehind);

Avoid using the CSS `z-index` property, it'll just cause problems with your Transforms.



