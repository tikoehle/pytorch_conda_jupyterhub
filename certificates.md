## Certificates Intro
- Certificate utilizes public and private key (public-key cryptography).
- Encrypting uses pub key / decrypting uses priv key.
- Common use-case: encrypting application traffic using Secure Socket Layer (SSL) or Transport Layer Security (TLS).
- Example: Apache HTTPS (HTTP over SSL)
- Certificate is a method to distribute the pub key + other information about the server and the owning organization.

## Certificate Types
- **Digitally signed by a Certification Authority (CA), a trusted third party organization.**
  - Public CA can only issue certificates for *valid public domain names*.
  - CA issues a signed certificate.
  - CA guaranteeing the identity of the organizations web server (DNS resolved server FQDN or wild-card server name. 
    - Example: “comp9.home.org” vs. “*.home.org”).
  - Everyone can download and import CA certificates (browser certificate store vs. system certificate database).
  - Browsers recognize the CA certificate and HTTPS encrypt the connection without prompting the user.
- **Self-signed**
  - Easy creation, *private DNS domains*, for test usage only.
  - Anyone can claim identity.
  - Browser does not recognize it, must be imported into system cert db.

## Self-signed Steps
1. Create a private FQDN for the server.
   * To get a self-signed cert trusted by Google Chrome.
2. Create the cert with server FQDN in subdomain.
   * A recent change in chrome requires a .crt with valid sub-domain which is DNS resolved.
3. Create the self-signed using `openssl`.
4. Copy the certificate .crt to a safe place on the server.
5. Configure the server with the new .key and .crt.
6. Get the self-signed cert trusted by Google Chrome or any other browser.

## Self-signed (Ubuntu 18.04, Google Chrome >= Version 70.0)
1. Create a private FQDN for the server
   * To get a self-signed cert trusted by Google Chrome
   * For example, using dnsmasq:
   ```
   /etc/hosts
   192.168.2.99 comp9
   
   /etc/dnsmasq.conf
   domain=home.org
   
   dnsmasq restart
   ```
   
2. Create the cert with server FQDN in subdomain
   * A recent change in chrome requires a .crt with valid sub-domain which is DNS resolved.
   * Using a openssl.conf file to simplify the certificate creation with openssl.
   * In this section of the openssl.conf is the subdomain configuration of the .crt:
   ```
   [ alternate_names ]
   DNS.1       = home.org
   DNS.2       = *.home.org
   DNS.3       = *.comp9.home.org
   DNS.4       = ftp.home.org 
   ```
   
3. Create the self-signed using `openssl`
   * Passing the openssl.conf file to openssl. This creates a private .key and a public .crt.
   * This will create a self-signed certificate which is accepted by Google Chrome >= v70. 
   ```
   openssl req -config openssl.conf \
   -new -x509 -sha256 -newkey rsa:2048 -nodes \
   -keyout jupyterhub.key -days 3650 -out jupyterhub.crt
   ```
   
4. Copy the certificate .crt to a safe place on the server.
   * The .key must be protected but readable by the process owner who starts server process.
   * If the server process runs as root then the following are the standard default locations for .key and .crt.
   ```
   /etc/ssl/certs/                   # the .crt is public
   /etc/ssl/private/                 # the .key must be protected
   ```
   * If jupyterhub runs under a user, then create a somewhat safe place for that users .key.
   ```
   sudo mkdir /etc/ssl/jupyterhub
   sudo chown root:<user> /etc/ssl/jupyterhub
   sudo chmod 750 /etc/ssl/jupyterhub
   sudo cp jupyterhub.key /etc/ssl/jupyterhub/                    # copy the private key
   sudo chown root:<user> /etc/ssl/jupyterhub/jupyterhub.key
   sudo cp jupyterhub.crt /etc/ssl/certs/                         # copy the public .crt
   ```
   
5. Configure the server with the new .key and .crt.
   * In jupyterhub_config.py:
   ```
   c.JupyterHub.ssl_cert = '/etc/ssl/certs/jupyterhub.crt'
   c.JupyterHub.ssl_key = '/etc/ssl/jupyterhub/jupyterhub.key'
   ```
   
6. Get the self-signed cert trusted by Google Chrome
   * Connect to the hub with https.
   ```
   https://comp9.home.org:8000/hub/login
   ```
   * Note, the cert is not yet trusted (https is red crossed in the browser URL)
   * Next, click the red lock icon in the browser URL (an X),
     * Choose Certificate Information
     * Go to Details tab
     * Click on Export (save as a file, name does not matter, i.e. jupyterhub.p7c)
   * Import that .p7c into the client machine local trust store
   ```
   sudo apt install libnss3-tools
   certutil -d sql:$HOME/.pki/nssdb -A -t "P,," -n jupyterhub.p7c -i jupyterhub.p7c
   ```
   * To see if the import worked, you can list all of your certificates with this command:
   ```
   certutil -d sql:$HOME/.pki/nssdb -L
   ```
   * Restart Google Chrome and connect to the JupyterHub.
   ```
   https://comp9.home.org:8000
   ```
   * The connection is now SSL encrypted and trusted by the browser.
   * Removing A Certificate:
   ```
   certutil -D -d sql:$HOME/.pki/nssdb -n -<certificate>
   ```
   
