#oauth2 #web 

OAuth defines two client types, based on their ability to authenticate securely with the authorization server ==referring to the ability of the client to maintain confidentiality of the client credentials==:

- **Confidential** : Clients capable of maintaining the confidentiality of their credentials, or otherwise capable of secure client authentication using other means.
- **Public**: Clients with risk of credentials exposure, such as installed native applications, web-applications.
The authorization SHOULD NOT make assumptions about the client type.

Clients themselves can be classified as:
- **Web Application**:A web application is a confidential client running on a web server. The client credentials and the token issued to the client are stored in the browser and are not accessible to the user.
- **User Agent Based Application**:  This is a public client in which the client code is downloaded from a web-server and executes within a user-agent (e.g.  web-browser ) on the device used by the resource owner. Protocol data and credentials are easily accessible and often visible to the resource owner. Since these applications reside within the user-agent, they can make seamless use of the user-agent capabilities when requestion authorization.
- **Native Application**: A native application is a public client installed and executed on the device used by the resource owner. Protocol data and credentials are accessible to the resource owner. It is assumed that any client authentication credentials included in the application can be extracted. On the other hand, dynamically issued credentials such as access tokens or refresh tokens can receive an acceptable level of protection. At a minimum, these credentials are protected from hostile servers with which the application may interact.

# Client Identifier
The authorization server issues the registered client a client identifier -- a unique string representing the registration information provided by the client. The client identifier is not a secret; it is exposed to the resource owner and MUST NOT be used alone for client authentication. The client identifier is unique to the authorization server. The client should not make assumption about the size of the  identifier. The authorization server SHOULD document the size of any identifier it issues.

Next -> [[Client Authentication]]