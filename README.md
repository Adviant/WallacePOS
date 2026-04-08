> [!IMPORTANT]
> WallacePOS is **END OF LIFE** and is no longer being maintained by [micwallace/wallacepos](https://github.com/micwallace/wallacepos). 
> This is a fork where it will be modernized.

> [!WARNING]
> Not recommended for produciton enviroment.


# WallacePOS

### Install Prerequisites

* PHP 5.4
* Apache 2.4 
* Composer
* Node
* Certbot (Optional)

### Apache Moudles Enabled
 
```
proxy_http
proxy_wstunnel
rewrite
curl
gd
```

### Virtual Host Config

WallacePOS can run with or without SSL but it is recommended to use SSL. Use Certbot to genarate certificates or use your own. Copy the virtual host config below and enter your details in the **%*%** locations. 
```
<VirtualHost *:443>
    DocumentRoot "%/your_install_dir%"
    ServerName %your.server.fqdn%
    ErrorLog "logs/error.log"
    CustomLog "logs/access.log" common

    SSLEngine on
        SSLCipherSuite !ADH:!DSS:!RC4:HIGH:+3DES:+RC4
        SSLProtocol all -SSLv2 -SSLv3
        SSLCertificateFile %certificate_location%
        SSLCertificateKeyFile %key_location%
        SSLCertificateChainFile %cert_chain_location%

    DocumentRoot "%/your_install_dir%"
    ServerName %your.server.fqdn%
    ErrorLog "logs/error.log"
    CustomLog "logs/access.log" common

    RewriteEngine On
        RewriteCond %{QUERY_STRING} transport=websocket [NC]
        RewriteRule /(.*) ws://localhost:8080/$1 [P,L]
        ProxyPass /socket.io/ http://localhost:8080/socket.io/
        ProxyPassReverse /socket.io/ http://localhost:8080/socket.io/
        <Location /socket.io>
            Order allow,deny
            Allow from all
        </Location>
</VirtualHost>
```

If you are **NOT** using SSL copy the virtual host config below and enter your details in the **%*%** locations.

 ```
<VirtualHost *:80>
    DocumentRoot "%/your_install_dir%"
    ServerName %your.server.fqdn%
    ErrorLog "logs/error.log"
    CustomLog "logs/access.log" common

    RewriteEngine On

    RewriteCond %{QUERY_STRING} transport=websocket [NC]
    RewriteRule /(.*) ws://localhost:8080/$1 [P,L]

    ProxyPass /socket.io/ http://localhost:8080/socket.io/
    ProxyPassReverse /socket.io/ http://localhost:8080/socket.io/

     <Location /socket.io>
           Order allow,deny
           Allow from all
     </Location>
</VirtualHost>
```


### Installation & Startup

1. Clone the latest release to %your_install_dir%. This must be the Apache document root directory (www, htdocs, public_html).
   
2. Open a terminal in %your_install_dir% and run `composer i`.

3. Open a terminal in %your_install_dir% and run `npm i`.

4. Visit %your_website%/installer in your browser and follow the installation wizard.

5. Open a Terminal and navigate to the api dir (%your_install_dir%/api) and run `node server.js`. (Don't close this terminal)

4. Visit %your_website%/admin, go to Settings -> Utilities and make sure the feed server has been started successfully.
