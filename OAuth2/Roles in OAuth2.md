#oauth2 #web 

OAuth defines four roles:

### Resource Owner
An entity capable of granting access to a protected resource. When the #resource-owner is a person, it is referred to as $end-user$.
Example : ==You as a user==, ==a database==
### Resource Server
The server hosting the protected-resource, capable of accepting and responding to protected resource requests using access tokens. 
Example: ==Google cloud serving your data==
### Client
An application making request to the server for protected resources on behalf of the resource owner and with its authorization.
Example: ==An application that wants access to your data, to see contents perhaps==
### Authorization Server
The server issuing the access tokens to the client after successfully authenticating the resource owner and obtaining authorization.
Example: ==Google Login Applicaiton==

> [!info] OAuth2 doesn't cover the interaction between the authorization server and the resource server


Next -> [[Protocol Flow in OAuth]]





