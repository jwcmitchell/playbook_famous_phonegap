# Debugging


### basic debugging Patterns
- serve using heroku's `foreman` and the handy `ngrok`.
- using Chrome console and localhost
- remote logging using TrackJS

### Android: Eclipse debugging for native problems

Install Eclipse and check the log (limit to `tag:chromium`) to detect native-level errors (contacts SUCK).

### Setting up foreman and ngrok

    heroku create myapp
    foreman start
    ./ngrok 5000 my-subdomain-choice

### Phonegap build links (direct download for Android)

> This only works for Android, you have to use TestFlight for iOS

To easily distribute your app to folks on Android, simply copy the links on PhoneGap Build. They should include a `qr_code` string.
