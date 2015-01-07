# Layout Builder


> Currently under heavy development, input welcome! 

This is an optional component you can use to build a layout. It tries to make it easy and quick (and syntactially more concise) to build a layout using Famo.us components like `SequentialLayout`, `FlexibleLayout`, and `RenderController`, combined with `Modifiers` for **size**, **origin**, **transform**, and **planes**. 

Another layout component in a similar vein is https://github.com/IjzerenHein/famous-flex. 


## Usage 

Require it in a View: 

    var LayoutBuilder = require('views/common/LayoutBuilder');
    
    
A LayoutBuilder accepts a dictionary of arguments, with the following keys allowed:

- sequential 
- scroller 
- flexible 
- grid 
- controller 
- surface  

Each of the keys maps to the expected Famo.us element, and you pass some additional per-element parameters. 


### surface 

At the most basic level, you might want to create a Surface using the LayoutBuilder. 

Example: 

    var tmpSurface = new LayoutBuilder({
        surface: {
            key: 'MySurface',
            mods: [{
                key: 'MyOpacity',
                opacity: 0.5
            },{
                key: 'MyTransform'
            }],
            margins: [10,10,10,10],
            surface: new Surface({
                content: 'Hello',
                wrap: '<span></span>',
                size: [undefined, true]
            }),
            click: function(){
                alert('awesome')
            },
            deploy: function(){
                // ran when the surface deploys
            },
            pipe: this.OtherScrollView,
            events: function(theSurface){
                // start an animation, etc.
            }
        }
    });
    
    myContext.add(tmp);
    
    
Let's break it down: 

#### `key`

Makes it easy to reference this element from elsewhere. This becomes more apparent as you use more complex/deep layouts. In the above example, you can reference the created element in multiple ways:

    tmpSurface.node = "the lowest-level node, the Surface"
    tmpSurface.MySurface = "the lowest-level node, the Surface"
    tmpSurface = "the top-most node with margins, mods, etc."


#### `mods` 

An array of objects that will be parsed so that each created `new Modifier(obj_properties)` is added _in front of_ your node. The `key` inside mods is useful for a reference point. In our example above, we are effectively doing: 

    myContext.add("MyOpacity Mod").add("MyTransform Mod").add(the_actual_surface);
    
If we wanted to animate our surface by moving it down slightly (20px), we need to reference the "MyTransform" mod:

    tmpSurface.mods.MyTransform.setTransform(Transform.translate(0,20,0), {
        duration: 1250,
        curve: Easing.easeOut
    });
    

`mods` also supports being passed the string "sizer" that creates a surface that determines the actual size of a resolved `size: [undefined, undefined]` and can be accessed in an array at `YourCreatedObj.sizer[0]`. It is useful for centering a surface using a technique like: 

    mods: [{
        size: [undefined, undefined]
    },"sizer",{
        origin: [0.5,0.5],
        align: [0.5, 0.5]
    }]
    
> better example of 'sizer' needed...


#### `margins` 

An array of margins like `[10,8,6,4]` for `[top, right, bottom, left]`

> It is recommended to have a Surface _behind_ a margin'd element that has .pipe (otherwise the margins are effecively "empty" and don't transmit events) 


#### `surface` 

A RenderNode (usually a `Surface`) that is not intended to have a sequenceFrom. It could easily be an `ImageSurface` or other property. 


#### `click` 

A function that will be fired when the Surface is clicked. Only works on a Surface. 


#### `deploy` 

Fires every time the Surface is written to the actual DOM. Only works with a Surface. 


#### `pipe` 

Generally used to pipe to an external ScrollView, see the "scroller" example below. Not meant to be used if you're trying to pipe to a scroller that is in the LayoutBuilder you're creating! 

#### `events`

A function that is passed the created Surface (the actual Surface). Run whatever arbitrary stuff you want here. 




### Complex Example: `scroller` and `sequenceFrom` and `events` 
    
Here we're creating a vertical ScrollView that has three items in it. 
    
    var myScroller = new LayoutBuilder({
        scroller: {
            key: 'TheScroll',
            direction: 1,
            sequenceFrom: [{
                surface: {
                    key: 'Surface1',
                    surface: new Surface({
                        content: 'Surface1',
                        size: [undefined, 100]
                    }),
                    events: function(surface){
                        Timer.setTimeout(function(){
                            surface.pipe(myScroller);
                        },1);
                    }
                }
            },{
                surface: {
                    key: 'Surface2',
                    surface: new Surface({
                        content: 'Surface2',
                        size: [undefined, 100]
                    }),
                    events: function(surface){
                        Timer.setTimeout(function(){
                            surface.pipe(myScroller);
                        },1);
                    }
                }
            },(function(){
                var d = new Date();
                return (d.getHours() < 12) ? null : {
                    surface: {
                        key: 'Surface3',
                        surface: new Surface({
                            content: 'Surface3',
                            size: [undefined, 100]
                        }),
                        events: function(surface){
                            Timer.setTimeout(function(){
                                surface.pipe(myScroller);
                            },1);
                        }
                    }
                };
            })()]
        }
    });


### `sequenceFrom`  

This accepts an array and evaluates each node at runtime. If the node is one of Famous's types (ElementOutput, RenderNode, View, Surface, etc.) it will be included without modification, but if it is just an object we create a new LayoutBuilder from the node! 

Notice that we can modify our Surface1 by writing: 

    myScroller.TheScroll.Surface1.setContent('Surface1NewContent');

Also notice that we `pipe` from our surface to the scroller inside the `event` function, which fires _after_ myScroller is created (Timer.setTimeout with delay of 1, see __`events`__ above). 

Surface3 is created only if it is past 12pm, otherwise it returns a `null` value that the sequenceFrom ignores. 


### Example landing page (signup/login buttons) 

Following is the code used at the bottom of a sample landing page to create 4 buttons: two larger ones for Google+ and Facebook, and two smaller side-by-side buttons for Email Sign Up and Login.  

Notice that we're using a `SequentialLayout` that flows vertically (`direction: 1`). 

    this.footer = new LayoutBuilder({
        sequential: {
            direction: 1,
            sequenceFrom: [{
                surface: {
                    key: 'GoogleLogin',
                    surface: new Surface({
                        content: '<div><i class="icon ion-social-googleplus"></i><span>Google+</span></div>',
                        size: [Utils.WindowWidth(), 60],
                        classes: ['landing-button','gplus-button']
                    }),
                    click: function(){
                        // Pass off Google+ login/signup to the user's model
                        var userModel = new UserModel.User();
                        userModel.gplus_login();
                    }
                }
            },{
                surface: {
                    key: 'FacebookLogin',
                    surface: new Surface({
                        content: '<div><i class="icon ion-social-facebook"></i><span>Facebook</span></div>',
                        size: [Utils.WindowWidth(), 60],
                        classes: ['landing-button','facebook-button']
                    }),
                    click: function(){
                        // Pass off Facebook login/signup to the user's model
                        var userModel = new UserModel.User();
                        userModel.fb_login();
                    }
                }
            },
            {
                size: [undefined, 60],
                flexible: {
                    direction: 0,
                    ratios: [1,1],
                    sequenceFrom: [{
                        surface: {
                            key: 'EmailSignup',
                            surface: new Surface({
                                content: '<div>Sign Up</div>',
                                size: [undefined, true],
                                classes: ['landing-button','signup-button']
                            }),
                            click: function(){
                                App.history.navigate('signup');
                            }
                        }
                    },{
                        surface: {
                            key: 'Login',
                            surface: new Surface({
                                content: '<div>Log In</div>',
                                size: [undefined, true],
                                classes: ['landing-button','login-button']
                            }),
                            click: function(){
                                App.history.navigate('login');
                            }
                        }
                    }]
                }
            }]
        }
    });


The above looks roughly like: 

    | -----------------------|
    |           G+           |
    | -----------------------|
    |                        |
    | -----------------------|
    |        Facebook        |
    | -----------------------|
    |                        |
    | -----------------------|
    |   Sign Up  |   Login   |
    | -----------------------|
    
        



### FlexibleLayout HEIGHT when `size: [undefined, true]` 

LayoutBuilder includes some modifications to sizing, specifically situations with `size: [undefined, true]` where we want the height of a horizontally-aligned FlexibleLayout to be equal to the height of the longest element. An example could where we want to display a SequentialLayout of posts by users, and we have no idea how long each post is:

    var myLayout = new LayoutBuilder({
        sequential: {
            key: 'PostList',
            direction: 1,
            sequenceFrom: [{
                flexible: {
                    key: 'Post',
                    direction: 0,
                    ratios: [true, 1],
                    size: [undefined, true], // Notice this line!
                    sequenceFrom:[{
                        surface: {
                            key: 'UserImage',
                            surface: new ImageSurface({
                                src: 'img/user.png',
                                size: [100,100]
                            })
                        }
                    },{
                        surface: {
                            key: 'PostText',
                            surface: new Surface({
                                content: 'an insane amount of text here...',
                                size: [undefined, true]
                            })
                        }
                    }]
                }
            }]
        }
    });

> remember that for direction: 0=horizontal, 1=vertical

Usually, the SequentialLayout doesn't understand when a FlexibleLayout has a `true` size, so it counts it as `0` and moves along, causing each FlexibleLayout to stack up on top of each other. 

But by including `size: [undefined, true]` _inside_ the `flexible:{...}` we've triggered the following code in our LayoutBuilder:

    tmp.getSize = function(){

        var useHeight = 0;

        tmp.Views.forEach(function(v){
            var h = 0;
            try{
                h = v.getSize(true)[1];
            }catch(err){}
            if(h > useHeight){
                useHeight = h;
            }
        });

        // check against passed minSize
        if(options.minSize && (options.minSize[1] > useHeight)){
            useHeight = options.minSize[1];
        }

        return [options.size[0], useHeight];
    }
    
We essentially replace the `getSize` function for the FlexibleLayout with our own, that computes the height based on the max height of each view that we sequenceFrom'd. 


> Note that the GridLayout (in LayoutBuilder) does not yet support this type of sizing



