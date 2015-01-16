# Email


Sending email is a basic part of any application. We use SendWithUs to manage our email templates, and SendGrid for reliable delivery. 


### Sending an Email 

Sending can be done using 3 methods: 
- supply HTML and Text 
- use local template 
- use SendWithUs template 

#### Sending a basic email 


	models.email.send({
		to: 'nick@famousmobileapps.com',
		from: config.get('admin_email_from'),
		html: 'here is the html for the email<br>with an extra space!',
		text: 'Here is the plain text of the email'
	});


#### Sending using a local template 

	models.email.send({
		to: 'nick@famousmobileapps.com',
		from: config.get('admin_email_from'),
		template: 'test',
		data: {
		    config: swuConfig(),
		    foo: "bar"
		}
	});

Local templates are located in `views/emails`. The folder name specifies the template name (ex: "test"), and is rendered using  `nunjucks` (http://mozilla.github.io/nunjucks/). 

Inside the folder should be two files: `html.tpl` and `text.tpl`. 

The `swuConfig` function (defined in `server.js` is useful for scrubbing config values before sending to the template) 


#### Sending using SendWithUs 

	models.email.send({
		to: 'nick@famousmobileapps.com',
		from: config.get('admin_email_from'),
		swu_template: 'tem_mkeDroRSbsiPyXnp6osH4m',
		data: {
		    config: swuConfig(),
		    foo: "bar"
		}
	});

Templates should always start with `tem_`. 

Some error codes returned from SendWithUs are not explanatory (and no debug console exists). A `statusCode: 400` can result if you're using a template_id that is incorrect! 


### Default Templates 

Five templates should be created at minimum: 

- Welcome (after signup) 
- User Signup Notify Admin (after signup) 
- Invite (from one user to another) 
- Reset Password
- Reset Password Confirmation
 
The template IDs go in your `config.json` (look for `swu_tpl_...`). 

Example HTML for each are at the following links: 

Welcome: https://gist.github.com/nicholasareed/d4a190d6e9102b40803d

User Signup Notify Admin: https://gist.github.com/nicholasareed/4beca2d768744f8004f3 

Invite: https://gist.github.com/nicholasareed/0ab69b314cb6259c512f

ResetPassword: https://gist.github.com/nicholasareed/3e85c0fb6d395c1dd458

ResetPasswordConfirm: https://gist.github.com/nicholasareed/2694153c775f8dccf68b

