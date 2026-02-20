#oauth2 #web #token

Tokens are random generated strings that are used to authenticate the requests made to various servers. Token represent specific scopes and durations of access, granted by the resource owner, and enforced by the resource server and authorization server. The token may denote an identifier used to retrieve the authorization information( e.g. UUID to a table entry listing access ) or may contain the authorization information themselves. Apart from the ability to define scopes, the tokens allow the resource-server to focus on just handling token-based authentication.

In OAuth2, the following tokens are defined:
## Access Token
Access tokens are credentials used to access protected resources. Access tokens can have different formats, structures, and methods of utilization (e.g., cryptographic properties) based on the resource server security requirements.  Access token attributes and the methods used to access protected resources are beyond the scope of this specification and are defined by companion specifications such as.

## Refresh Tokens
Refresh tokens are credentials used to obtain access tokens. Refresh tokens are issued to the client by the authorization server and are used to obtain a new access token when the current access token becomes invalid or expires, or to obtain additional access tokens with identical or narrower scope (access tokens may have a shorter lifetime and fewer permissions than authorized by the resource owner). Issuing a refresh token is optional at the discretion of the authorization server. 

Unlike access tokens, refresh tokens are intended for use only with authorization servers and are never sent to resource servers

![[Tokens in OAuth 2025-10-06 14.04.18.excalidraw |  800x400]]

Steps (C), (D), (E), and (F) are outside the scope of this
specification.

## HTTP Redirections
This specification makes extensive use of HTTP redirections, in which
the client or the authorization server directs the resource owner’s
user-agent to another destination. While the examples in this
specification show the use of the HTTP 302 status code, any other
method available via the user-agent to accomplish this redirection is
allowed and is considered to be an implementation detail.

Next -> [[CR-Introduction]]