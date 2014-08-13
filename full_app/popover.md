# Popover

We'll break this out into it's own file, so defined/called as a function immediately.

    var createPopover = function(){

Our Popover is simply a Lightbox, with a `FrontMod` to make sure it is always ahead of our MainContext and Footer (but behind our Toast!).

We also get rid of any default Transition/Transforms as our Popover will be defining them.

        App.Views.Popover = new Lightbox({
            inTransition: false,
            outTransition: false,
        });

        App.Views.Popover.frontMod = new StateModifier({
            transform: Transform.inFront
        });
        App.MainContext.add(App.Views.Popover.frontMod).add(App.Views.Popover);

    };
    createPopover();
