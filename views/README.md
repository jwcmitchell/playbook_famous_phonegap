# Views


---

> # stub

---

Views
Ok, you’ve got a decent understanding of how we’re choosing which page to display, so now lets begin at the start…what happens when somebody wants to login?

Login (have them create their own Login page)
structure of PageView and prototypes
create layout (using HeaderFooterLayout)
no header, no footer for this view, but you’ll use this pattern frequently
create content, add surfaces
- different patterns for creating spacers (either add a new Surface, or a StateModifier)
inOutTransition
submitting via button press, ajax request for login/signup

signup
requesting multiple fields, but not username!
- server handles most of the profile-building and whatnot, simply returns success/fail of creation
on success, does a login and continues as normal

Welcome screens (popovers)
occurs after login or signup, after the user has been populated once (to check if username exists)
