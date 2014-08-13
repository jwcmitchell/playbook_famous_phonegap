# Main


## Context

You'll do this ONE time. The "perspective" can be anything from 1 to 1000, but the actual value has no discernable effect on our app. You MUST set it though, otherwise Android will have a rough time displaying some text.

            App.MainContext = Engine.createContext();
            App.MainContext.setPerspective(1000);


## Lightbox (App.MainController)

Let's create the `App.MainController` as a Lightbox, which allows us to easily (and beautifully) switch between arbitrary PageViews.

            App.MainController = new Lightbox();
            App.MainController.resetOptions = function(){
                this.setOptions(Lightbox.DEFAULT_OPTIONS);
            };

            // Add Lightbox/RenderController to mainContext
            App.MainContext.add(App.MainController);

## Main Background

We want a basic `white` background at first. This is also one of the few places we'll use `zIndex` (or `z-index` in CSS) for our front/back positioning.

            App.MainContext.add(new Surface({
                size: [undefined, undefined],
                properties: {
                    // background: "url(img/mochaGrunge.png) repeat",
                    backgroundColor: "white",
                    zIndex : -10
                }
            }));
