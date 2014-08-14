# Footer

> Eventually this will be moved to Components.CreateStandardFooter (or similar function)

    // Main Footer
    var createMainFooter = function(){

Familiar pattern, always creating a View instead of a RenderNode:

        App.Views.MainFooter = new View();

Create the TabBar, we'll add content in a minute:

        App.Views.MainFooter.Tabs = new StandardTabBar();
        var tmpTabs = App.Views.MainFooter.Tabs;


Define a Tab (key, content, css classes):

        tmpTabs.defineSection('story', {
            content: '<i class="icon ion-ios7-photos"></i><div><span class="ellipsis-all">'+App.t('footer.highlights')+'</span></div>',
            onClasses: ['footer-tabbar-default', 'on'],
            offClasses: ['footer-tabbar-default', 'off']
        });

`content`: we're including an icon and text. The text is added after translation (`App.t(...)`) and surrounded by a `span` with class `ellipsis-all` (see CSS Styles).
`onClasses`/`offClasses`:  Array of ALL the classes you want included on that state. Generally I include a default class, plus an `on` and `off` class and my css looks like:

    .footer-tabbar-default.on {
        border-bottom: 4px solid blue;
    }

Let's define the rest of the Tabs (automatic sizing/width):

        tmpTabs.defineSection('explore', {
            content: '<i class="icon ion-play"></i><div><span class="ellipsis-all">'+App.t('footer.play')+'</span></div>',
            onClasses: ['footer-tabbar-default', 'on'],
            offClasses: ['footer-tabbar-default', 'off']
        });

        tmpTabs.defineSection('new', {
            content: '<i class="icon ion-ios7-plus-outline big-icon"></i>',
            onClasses: ['footer-tabbar-default', 'footer-tabbar-withbackground', 'on'],
            offClasses: ['footer-tabbar-default', 'footer-tabbar-withbackground', 'off']
        });
        tmpTabs.defineSection('news', {
            content: '<i class="icon ion-android-note"></i><div><span class="ellipsis-all">'+App.t('footer.news')+'</span></div>',
            onClasses: ['footer-tabbar-default', 'on'],
            offClasses: ['footer-tabbar-default', 'off']
        });
        tmpTabs.defineSection('profiles', {
            content: '<i class="icon ion-person"></i><div><span class="ellipsis-all">'+App.t('footer.profiles')+'</span></div>',
            onClasses: ['footer-tabbar-default', 'on'],
            offClasses: ['footer-tabbar-default', 'off']
        });


Now we need to handle tabs being selected:

        tmpTabs.on('select', function(result){
            switch(result.id){
                case 'profiles':
                    App.history.navigate('dash');
                    break;

                case 'ranking':
                    App.history.navigate('ranking/overall/friends/' + App.Data.Players.findMe().get('_id') + '/all');
                    break;

                case 'inbox':
                    App.history.navigate('inbox');
                    break;

                case 'story':
                    App.history.navigate('story/all');
                    break;

                case 'new':
                    App.history.navigate('add/things', {history: false});
                    break;

                case 'news':
                    App.history.navigate('actions/all');
                    break;

                case 'explore':
                    App.history.navigate('explore/home');
                    break;

                default:
                    console.error("None chosen");
                    return;
                    break;
            }
        });


Attach footer to the layout, and add a `frontMod` so that it is now the "most in front" Transform on this RenderNode.

        App.Views.MainFooter.frontMod = new StateModifier({
            transform: Transform.inFront
        });
        App.Views.MainFooter.originMod = new StateModifier({
            origin: [0, 1]
        });
        App.Views.MainFooter.positionMod = new StateModifier({
            transform: Transform.translate(0,60,0)
        });
        App.Views.MainFooter.sizeMod = new StateModifier({
            size: [undefined, 60]
        });

When we add our tabs to the main Context, it looks confusing but is basically: `View.FrontMod.OriginMod.PositionMod.Tabs`. The order is important; if you switch the OriginMod and PositionMod, for example, you'll get a Footer in a competely different place on-screen!

        App.Views.MainFooter.add(App.Views.MainFooter.frontMod).add(App.Views.MainFooter.originMod).add(App.Views.MainFooter.positionMod).add(App.Views.MainFooter.sizeMod).add(App.Views.MainFooter.Tabs);

We need to handle events for showing/hiding our footer. On `show` we'll bring the footer up from the bottom by adjusting its `PositionMod` using an easing function. `hide` just reverses our values, but quicker!

        App.Views.MainFooter.show = function(transition){
            transition = transition || {
                duration: 750,
                curve: Easing.outExpo
            };
            App.Views.MainFooter.positionMod.setTransform(Transform.translate(0,0,0), transition);
        };

        App.Views.MainFooter.hide = function(transition){
            transition = transition || {
                duration: 250,
                curve: Easing.itExpo
            };
            App.Views.MainFooter.positionMod.setTransform(Transform.translate(0,60,0), transition);
        };


Finally, we add the footer to our App.MainContext

        // Add to maincontext
        App.MainContext.add(App.Views.MainFooter);

    };
    createMainFooter();
