## Notice

WallacePOS is **EOL**. It is no longer being maintained by [micwallace/wallacepos](https://github.com/micwallace/wallacepos). 

This repo is being kept as inspiration for a new POS using seprate frontend and backend technologies powered by Laravel & React. 

If you are wanting to test out this software there will be a docker file added to this repo so that it can be run in a container.

**It is not recommended to deploy WallacePOS in a production evniroment**

## Original Instlation Guide

Below is the original installation guide if you would like to try out WallacePOS on bare metal.

**It is not recommended to deploy WallacePOS in a production evniroment**

## Server Prerequisites

WallacePOS requires:

1. A Lamp server with PHP version>=5.4, PHP cURL & GD extensions and Apache version>=2.4.7 with modules rewrite, proxy_http and proxy_wstunnel.

    - You can enable the modules by typing the following in your terminal

    ```
        sudo a2enmod proxy_http proxy_wstunnel rewrite
        sudo apt-get install php5-curl php5-gd
        sudo service apache2 restart
    ```

    - The following virtual host snippet in your apache config, replace %*% with your values and modify to your needs.


    ```
        <VirtualHost *:443>
             DocumentRoot %/your_install_dir%
             ServerName %your.server.fqdn%

             ErrorLog ${APACHE_LOG_DIR}/error.log
             CustomLog ${APACHE_LOG_DIR}/access.log combined

             SSLEngine on
                 SSLCipherSuite !ADH:!DSS:!RC4:HIGH:+3DES:+RC4
                 SSLProtocol all -SSLv2 -SSLv3
                 SSLCertificateFile %certificate_location%
                 SSLCertificateKeyFile %key_location%
                 SSLCertificateChainFile %cert_chain_location%

             <Directory %/your_install_dir%>
                AllowOverride all
             </Directory>

             # WSPROXY CONF
             ProxyRequests Off
             ProxyPreserveHost On
             <Proxy *>
                     Order deny,allow
                     Allow from all
             </Proxy>
             RewriteEngine On
             RewriteCond %{HTTP:Connection} Upgrade [NC]
             RewriteRule /(.*) ws://localhost:8080/$1 [P,L]
             ProxyPass        /socket.io http://localhost:8080/socket.io/
             ProxyPassReverse /socket.io http://localhost:8080/socket.io/
             <Location /socket.io>
                     Order allow,deny
                     Allow from all
             </Location>
        </VirtualHost>
    ```

    Note: Using plain http is not recommended.

2. Node.js installed along with the socket.io library

    For a Debian distro:

    ```
        sudo apt-get update
        sudo apt-get install nodejs && apt-get install npm
        cd %/your_install_dir%/api
        sudo npm install socket.io
    ```

## Installation & Startup

1. Clone the latest WallacePOS release to %your_install_dir% if you haven't done so already.
   The installation dir must be your Apache document root directory!
   
2. Run `composer install` in your install directory to update PHP dependencies (you may need to install composer first).

3. Run `cd /api` then `node server.js`

5. Visit /installer in your browser & follow the installation wizard.

6. Login to the admin dashboard at /admin, from the menu go to Settings -> Utilities and make sure the feed server has been started successfully.
