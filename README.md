# mule-https-one-way-and-two-way-ssl
Securing Mule APIs - HTTPS Listener &amp; HTTPS Request with One-way and Two-way SSL

TLS is cryptographic protocol that secures communications in mule apps. Mule provide out-of-the-box support for HTTPS


One-way SSL: Server presents certificate to the client. Client must authenticate the server. 
Client is not required to present a certificate to the server. Server will accept any client into the connection.

Two-way SSL: Both the client and server have to present a certificate before the connection is established between the two.

Mutual Authentication: Two-way SSL authentication is a form of mutual authentication

1) A client requests access to a protected resource.
2) The server presents its certificate to the client.
3) The client verifies the server’s certificate.
4) If successful, the client sends its certificate to the server.
5) The server verifies the client’s credentials.
6) If successful, the server grants access to the protected resource requested by the client.

IMAGE

KeyStore: Used to store private key and identity certificates that a specific program should present to both parties (server or client) for verification
TrustStore: Used to store certificates from Certified Authorities (CA) that verify the certificate presented by the server in SSL connection.

IMAGE

The tls:context element in Mule flow defines a configuration for TLS, which can be used from both the client and server sides. 
From the server side: the truststore contains certificates of the trusted clients, the keystore contains the private and public key of the server.
From the client side: the truststore contains certificates of the trusted servers, the keystore contains the private and public key of the client.
Adding both a keystore and a truststore to one same config implicitly implements two-way TLS authentication, also known as mutual authentication

Image of the table

Commands:
Create KeyStore  (To store Localhost server’s credentials)

keytool -noprompt -validity 365 -genkeypair -v -alias localhost-server -keyalg RSA -storetype PKCS12 -keystore server-keystore.p12 -dname "CN=localhost, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown" -storepass password -keypass password -ext san="DNS:localhost,IP:127.0.0.1"

Create TrustStore (To store localhost client’s and/or external server’s credentials)

keytool -noprompt -validity 365 -genkeypair -v -alias localhost-client -keyalg RSA -storetype PKCS12 -keystore trusted-client-truststore.p12 -dname "CN=localhost, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown" -storepass password -keypass password


Add cert of external server (API) to TrustStore:
Example: https://gorest.co.in/public-api/users/45
- Extract its cert from browser and copy to file
- Add cert to the above TrustStore

You can either use the commands OR "KeyStore Explorer“ (https://keystore-explorer.org/)

