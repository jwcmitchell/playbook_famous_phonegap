# Common Views


## StandardHeader and StandardNavigationBar

Basically every view uses this combination. Together, they handle "Back" transitions, title surfaces, addditional surfaces (the "more" surfaces in the top-right).


### StandardHeader

details. ehre...

### StandardNavigationBar

details here...

### StandardTabBar

Uses `StandardToggleButton` and changed to not emit an event if `triggerEvent === false` on `.select`.

### StandardToggleButton

Modified `.select` to allow a change of state, without triggering an event.

    App.MainFooter.Tabs.select('dash', false);
