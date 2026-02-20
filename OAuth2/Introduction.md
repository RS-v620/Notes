#oauth2 #web

The OAuth 2.0 authorization framework enables a third-party
application to obtain limited access to an HTTP service.
This is done either on behalf of a resource owner by the application, or letting third-party applications.

### Need ?
In traditional client-server authentication model, the client requests access-restricted resources on a server by authenticating with the server, using resource-owner's credentials. This approach has the following shortcomings:
- Third-party applications are required to store resource owner's credentials for future source.
- Password authentication has inherent security risks
- There is little scope to allow/restrict access to resource data because of a lack of scoping.
- Resource owners can't selectively revoke access rights of all users
- Compromise of credentials exposes the entirety of user's data

Oauth addresses these issues by introducing an authorization layer, and separating the role of the client from that of the resource owner.

> [!info] The Oauth2 specification is designed to be used over **HTTP** only, any other protocol is out of scope

Next -> [[Roles in OAuth2]]



