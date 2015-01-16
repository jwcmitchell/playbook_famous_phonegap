# SSL

### Development 

Heroku automatically provides an HTTPS connection at `https://yourapp.herokuapp.com`. 


### Production 

Heroku's guide to SSL setup: https://devcenter.heroku.com/articles/ssl-endpoint 

In your terminal, create the public key 

    $ openssl genrsa -des3 -out server.pass.key 2048
    // enter an "easy" password value, only using it once

Then strip the key of the password

    $ openssl rsa -in server.pass.key -out server.key
    
Create the CSR 

> "Common Name" must be your subdomain! *.example.com or www.example.com or example.com

    
    $ openssl req -nodes -new -key server.key -out server.csr
    

Submit this CSR file to your certificate provider. When choosing the type of server, choose "nginx" if you're using Heroku. 

> The following examples use the Wildcard Essential SSL from Namecheap for $99/year 


After confirming some information for the certificate provider, you'll be emailed a domain confirmation, then receive another email with a .zip attachment containing your new certificates. 

Download and extract the .zip file, then combine the resulting `.crt` files to create `bundled.crt`: 

    $ cat AddTrustExternalCARoot.crt COMODORSAAddTrustCA.crt COMODORSADomainValidationSecureServerCA.crt STAR_yourdomain_com.crt > bundled.crt


Provision our Heroku app for SSL

    $ heroku addons:add ssl:endpoint
    
and add our `bundled.crt` and (created above) `server.key`. 

    $ heroku certs:add bundled.crt server.key

This will result in something like: 

    Resolving trust chain... done
    Adding SSL Endpoint to yourherokuapphere... done
    yourherokuapphere now served by wakalama-2000.herokussl.com
    Certificate details:
    Common Name(s): *.yourdomain.com
                    yourdomain.com
    
    Expires At:     2016-01-16 23:59 UTC
    Issuer:         /OU=Domain Control Validated/OU=EssentialSSL Wildcard/CN=*.yourdomain.com
    Starts At:      2015-01-16 00:00 UTC
    Subject:        /OU=Domain Control Validated/OU=EssentialSSL Wildcard/CN=*.yourdomain.com
    SSL certificate is verified by a root authority.


Now simply add the newly-created `wakalama-2000.herokussl.com` domain in the Heroku dashboard for the app, and update your DNS entries. 
    
    
    
