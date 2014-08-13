# require.config


require.config
- paths


require.js patterns
- backbone-adapter
- jquery-adapter


Notice the `path` that is set for `utils` pointing to `../src/lib2/utils`. This makes it so that we can simply do

    var Utils = require('utils');

In any of our Views in order to get access to the Utility functions without having to type `../src/lib2/utils` every time. Every keystroke counts!
