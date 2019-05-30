# SOCtopus
Flask API to assist with automating SOC functions from Security Onion

Clone the repo:   

`git clone https://github.com/weslambert/soctopus`

Build the image:   

`cd SOCtopus && sudo docker build -t soctopus .`

Add the following to `/etc/apache2/sites-available/securityonion.conf`:

````
<Location /soctopus>
	AuthType form
	AuthName "Security Onion"
	AuthFormProvider external
	AuthExternal so-apache-auth-sguil
	Session On
	SessionCookieName session path=/;httponly;secure;
	SessionCryptoPassphraseFile /etc/apache2/session
	ErrorDocument 401 /login.html
	Require valid-user
	ProxyPass http://127.0.0.1:7000
	ProxyPassReverse http://127.0.0.1:7000
</Location>

````

Restart Apache:

`sudo service apache2 restart`

Run Docker container:   

`sudo docker run -d --name=soctopus -p 0.0.0.0:7000:7000 -v $PATHTO/SOCtopus.conf:/SOCtopus/SOCtopus.conf:ro soctopus`

Connect to so-elastic-net:    

`sudo docker network connect --alias soctopus so-elastic-net soctopus`

Add scripted field in Kibana, named `TheHive`, specifying as a string value and the script as:

`'https://SECURITYONIONIP/soctopus/thehive/alert/' + doc['_id'].value`
