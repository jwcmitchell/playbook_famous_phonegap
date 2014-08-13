# main.js

This is the entry point for your app. It takes care of removing the splash screen, creating the global App variable, checking authentication, figuring out what page to start the user, etc.

I've broken main.js into three sections:
- __require.config__: Setting up paths and naming used elsewhere
- __general__: Startup functions, loading the most basic of content/libraries
- __MainContext__: Adding the different display layers (main, main footer, popovers, etc.)
