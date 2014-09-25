# Welcome Screens

After Signup, you'll want a user to complete a series of basic tasks, to Welcome them to the service
- Say hello
- choose username
- ...


### Welcome Page properties 

These PageViews differ slightly from normal PageViews. Additions include: 
- `PageView.prototype.backbuttonHandler` for preventing reverse traversal of the history (if you're on a welcome screen, you probably do not want to accidentally go Back to the Login page) 
- No Header/Footer - instead they use a single view and differently-placed buttons (generally in the middle-to-lower half of the page). 
- preventing Back after Welcome is complete - the final Welcome screen will usually include `App.history.eraseUntil('all-of-em')` so that additinal Back Button events don't result in a return to the Welcome page(s). 


### Addition Welcome Screens

Following the `welcome/xyz` pattern, you could add additional Welcome screens easily. Common examples include:
- Upload Profile Picture
- Add biography or other text
- Connect with Friends
- Basic feature walkthrough
-
