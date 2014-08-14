# Common Views

I've slightly modified a few famo.us components, and included them in the `views/common/` directory.


### `StandardHeader` and `StandardNavigationBar`

Basically every view uses this combination. Together, they handle "Back" transitions, title surfaces, addditional surfaces (the "more" surfaces in the top-right).


### StandardHeader

Includes a back icon, title text, and optional `moreSurfaces` for including icons/links/buttons in the header.

    PageView.prototype.createHeader = function(){
        var that = this;

        this.headerContent = new View();
        this.headerContent.Settings = new Surface({
            content: '<i class="icon ion-gear-a">',
            size: [60, undefined],
            classes: ['header-tab-icon-text-big']
        });
        this.headerContent.Settings.on('click', function(){
            App.history.navigate('settings');
        });

        // create the header
        this.header = new StandardHeader({
            content: 'Invite Friends',
            classes: ["normal-header"],
            backClasses: ["normal-header"],
            moreSurfaces: [
                this.headerContent.Settings
            ]
        });
        this.header._eventOutput.on('back',function(){
            App.history.back();
        });
        this.header.navBar.title.on('click',function(){
            App.history.back();

        });
        this.header.pipe(this._eventInput);


The StandardHeader is equipped to handle `inOutTransition` events.

        this._eventOutput.on('inOutTransition', function(args){
            this.header.inOutTransition.apply(this.header, args);
        })

        // Attach header to the layout
        this.layout.header.add(this.header);

    };



### StandardNavigationBar

This is used as part of the `StandardHeader`, and includes the logic for `moreSurfaces`.

### StandardTabBar

Uses `StandardToggleButton` and changed to not emit an event if `triggerEvent === false` on `.select`.

### StandardToggleButton

Modified `.select` to allow a change of state, without triggering an event.

    App.MainFooter.Tabs.select('dash', false);
