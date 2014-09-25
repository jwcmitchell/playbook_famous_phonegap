# Building More Views

We've provided a few working sample views, now you'll want to use your knowledge of Famo.us, combined with the sample code provided, to build your more custom views (dashboards, friend connecting, full onboarding flows, etc.).

Remember that for each PageView, you'll want to add corresponding entries to the `router.js` and ensure you're handling Keyboard, Pause/Resume, BackButton, and other events properly. Leaving out the `PageView.prototype.backbuttonHandler`, for example, will result in the normal Back Button action occurring (`App.history.back()`) when the Android Back Button is pressed; if you're on a page that shouldn't go back, this is a problem! 