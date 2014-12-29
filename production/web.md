# Mobile Web


The base project is also setup to extend to mobile web usage. We do most debugging in the Chrome console, running under http://localhost. It is not recommended to use `file://` prefixes for development. 


### Pushing to GitHub Pages 

As easy way to host publicaly is on GitHub pages. Simple point your DNS record to `yourusername.github.io/name_of_app_repository` and push to the gh-pages branch. 

    git checkout origin/gh-pages -b gh-pages
    git push origin gh-pages
    

After a few minutes to build, you can access your site. 


### Limitations 

__Width__: Set in the `credentials.json` file using the `max_width` property. Default is __500px__. 

__Height__: Set in the `credentials.json` file using the `max_width` property. Default is __2000px__. 

__Background__: Background is specified in the css file under `.overall-background`. The app is is centered. 
