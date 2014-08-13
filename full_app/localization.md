# localization

Localization can be done pretty easily. Everything is initialized in `main.js` and anywhere that you want to change strings, simply use `App.t(stringName)`.

### Basics

__i18next plugin__: powers everything. Configuration in `main.js`.


### Directory Structure:


    locales/
        dev/
            locale.strings
            ns.common.json
        en/
            locale.strings
            ns.common.json

Add additional languages (like `ru`) by including both `locale.strings` and `ns.common.json`.

> Notice that `locale.strings` is simply a dummy file, used by PhoneGap Build for telling the iOS/etc. stores what languages you intend to support
