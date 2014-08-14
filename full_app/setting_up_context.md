# MainContext


---

> stub

---

MainContext
MainController (Lightbox, with defaults)
BgSurface for MainController (with zIndex)
MainFooter
- frontMod
Popover
ToastNode
FPS calculator

Start backbone.history (should be App.history)
Ajax Parameters (todo, fix this, leaks token!)
Get localUser from localStorage
- set headers with token (should do it in the model, per-fetch?)
- go to Profiles tab if logged in
- preload Models
- update the user (necessary here, can’t we just call App.Data.User.fetch()?)
     - more like testing that they are still valid, and then logging out if not
if not logged in:
- unregister from Push
- go to logout/login page
