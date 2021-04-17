# mule-https-one-way-and-two-way-ssl
## Securing Mule APIs - HTTPS Listener &amp; HTTPS Request with One-way and Two-way SSL

* TLS is cryptographic protocol that secures communications in mule apps. Mule provide out-of-the-box support for HTTPS
  * One-way SSL
    * Server presents certificate to the client. Client must authenticate the server. Client is not required to present a certificate to the server. Server will accept any client into the connection.
  * Two-way SSL
    * Both the client and server have to present a certificate before the connection is established between the two.

### Mutual Authentication: Two-way SSL authentication is a form of mutual authentication

1) A client requests access to a protected resource.
2) The server presents its certificate to the client.
3) The client verifies the server’s certificate.
4) If successful, the client sends its certificate to the server.
5) The server verifies the client’s credentials.
6) If successful, the server grants access to the protected resource requested by the client.

![image](https://user-images.githubusercontent.com/16226297/115125366-a3ae8880-9f95-11eb-82b1-ee5e69969ceb.png)

* KeyStore
  *  Used to store private key and identity certificates that a specific program should present to both parties (server or client) for verification
* TrustStore
  * Used to store certificates from Certified Authorities (CA) that verify the certificate presented by the server in SSL connection.

![image](https://user-images.githubusercontent.com/16226297/115125400-cfca0980-9f95-11eb-9f4d-c16a73e20236.png)

### The tls:context element in Mule flow defines a configuration for TLS, which can be used from both the client and server sides. 
* From the server side
  * The truststore contains certificates of the trusted clients, the keystore contains the private and public key of the server.
* From the client side
  * The truststore contains certificates of the trusted servers, the keystore contains the private and public key of the client.

Adding both a keystore and a truststore to one same config implicitly implements two-way TLS authentication, also known as mutual authentication

### This project and scenarios - Summary of what's needed for each
![image](https://user-images.githubusercontent.com/16226297/115125453-25061b00-9f96-11eb-96d2-6cd905d402cb.png)

### Commands (You can either use the commands OR "KeyStore Explorer“ (https://keystore-explorer.org/)
* Create KeyStore  (To store Localhost server’s credentials)
  * keytool -noprompt -validity 365 -genkeypair -v -alias localhost-server -keyalg RSA -storetype PKCS12 -keystore server-keystore.p12 -dname "CN=localhost, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown" -storepass password -keypass password -ext san="DNS:localhost,IP:127.0.0.1"

* Create TrustStore (To store localhost client’s and/or external server’s credentials)
  * keytool -noprompt -validity 365 -genkeypair -v -alias localhost-client -keyalg RSA -storetype PKCS12 -keystore trusted-client-truststore.p12 -dname "CN=localhost, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown" -storepass password -keypass password

* Add cert of external server (API) to TrustStore:
  * Example: https://gorest.co.in/public-api/users/45
  * Extract its cert from browser and copy to file
  * Add cert to the above TrustStore

### Disable “Enable SSL certificate verification” in Postman settings
![image](https://user-images.githubusercontent.com/16226297/115125492-6a2a4d00-9f96-11eb-8b64-9e5fbe00261f.png)

### Adding Client Certificate in Postman for 2-Way SSL on HTTPS Listener in MuleSoft API
* Go to File --> Settings --> Certificates tab --> Add Certificate
* You would supply host, and port where your Mule API is hosted at, along with Truststore file info and its password.
![image](https://user-images.githubusercontent.com/16226297/115125512-9219b080-9f96-11eb-9d90-a9a78e92a0c8.png)

### Extract Certificate for External API when you are calling external HTTPS secured API from HTTP Request component in Mule API
* Example: API being called in the sample project: https://gorest.co.in/public-api/users/359
* Go to Browser and put the above in the address bar
* Click the lock icon
![image](https://user-images.githubusercontent.com/16226297/115125525-b2e20600-9f96-11eb-9d96-844cb38ea74e.png)
* Click Certificate
![image](https://user-images.githubusercontent.com/16226297/115125532-c1302200-9f96-11eb-9bfa-71ff772325dd.png)
![image](https://user-images.githubusercontent.com/16226297/115125538-c7260300-9f96-11eb-9786-0bf27e8fedb3.png)

* Go thru default settings, and select the path and name of the file with .cer extension to which certificate is to be copied.
* Then go to Keystore Explorer tool --> open the Truststore you created (trusted-client-truststore.p12) --> supply password --> go to Tools --> Select import trusted certificate option --> supply the above certificate file .cer extension --> finish adding
* Use the Truststore in Mule Application

## Video
https://vimeo.com/537746026/c7ce729333
