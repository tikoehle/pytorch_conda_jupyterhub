## Certificates Intro
- Certificate utilizes public and private key (public-key cryptography).
- Encrypting uses pub key / decrypting uses priv key.
- Common use-case: encrypting application traffic using Secure Socket Layer (SSL) or Transport Layer Security (TLS).
- Example: Apache HTTPS (HTTP over SSL)
- Certificate is a method to distribute the pub key + other information about the server and the owning organization.

## Certificate Types
- Digitally signed by a Certification Authority (CA), a trusted third party organization.
  - Public CA can only issue certificates for valid public domain names.
  - CA issues a signed certificate.
  - CA guaranteeing the identity of the organizations web server (DNS resolved server FQDN or wild-card server name. 
    - Example: “comp9.home.org” vs. “*.home.org”).
  - Everyone can download and import CA certificates (browser certificate store vs. system certificate database).
  - Browsers recognize the CA certificate and HTTPS encrypt the connection without prompting the user.
- Self-signed
  - Easy creation, private DNS domains, for test usage only.
  - Anyone can claim identity.
  - Browser does not recognize it, must be imported into system cert db.
