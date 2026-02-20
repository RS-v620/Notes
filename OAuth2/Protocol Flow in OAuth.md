#web #oauth2 

![[Protocol Flow in OAuth 2025-10-06 12.43.24.excalidraw | 800]]
1. The client requests authorization from the resource owner. This request can be made directly to the resource owner but via **Auth Server** is preferred.
2. The client receives an authorization grant, which is a credential representing the resource owner's authorization, expressed using one of the four grant types ( or a custom defined grant type )
3. The client requests an **access token** by authenticating with the authorization server and presenting the authorization grant.
4. The authorization server authenticates the client and validates the authorization grant, and if valid, issues an access token
## Authorization Grant
The authorization grant is the credential representing the resource owner's authorization to access the protected resource. This credential is utilized to obtain the access tokens. Four predefines + Extension mechanism exists for these grants and are as follows:
1. Authorization Code
2. Implicit
3. Resource Owner's Password Credentials
4. Client Credentials
5. Extensibility Mechanism
### Authorization Code
The authorization code is obtained by using an authorization server as an intermediary between the **client**(application) and resource **owner**(user). Instead of requesting authorization directly from the resource owner, the client directs the resource owner to an authorization server(e.g. client opens a webpage to the auth-server), which in turn directs the resource owner back to the client with the authorization code( essentially passing the login creds to the client).

Before directing the resource owner back to the client with the authorization code, the authorization server authenticates the resource owner and obtains authorization. ==Because the resource owner only authenticates with the authorization server, the resource owner’s credentials are never shared with the client==.

The authorization code provides a few important security benefits, such as the ability to authenticate the client, as well as the transmission of the access token directly to the client without passing it through the resource owner’s user-agent and potentially exposing it to others, including the resource owner.


### Implicit
The implicit grant is a simplified authorization code flow optimized for clients (applications) implemented in a browser using a scripting language using a scripting language such as JS. In the implicit flow, instead of issuing the client an authorization code, the client is issues an access token directly.
When using implicit grant, the authorization server does not authenticate the client. In some cases, the client identity can be verified via the redirection used to deliver the access token to the client. The access-token may be exposed to the resource or other applications with access to the resource owner's user-agent(UI used by user, console used by user)

### Resource Owner Password Credentials
The resource owner password credentials (i.e., username and password) can be used directly as an authorization grant to obtain an access token. ==The credentials should only be used when there is a high degree of trust between the resource owner and the client== (e.g., the client is part of the device operating system or a highly privileged application), and when other authorization grant types are not available (such as an authorization code).
Even though this grant type requires direct client access to the resource owner credentials, ==the resource owner credentials are used for a single request and are exchanged for an access token. This grant type can eliminate the need for the client to store the resource owner credentials for future use, by exchanging the credentials with a long-lived access token or refresh token==.

### Client Credentials
The client credentials (or other forms of client authentication) can be used as an authorization grant when the authorization scope is limited to the protected resources under the control of the client, or to protected resources previously arranged with the authorization server. Client credentials are used as an authorization grant typically when the client is acting on its own behalf (the client is also the resource owner) or is requesting access to protected resources based on an authorization previously arranged with the authorization server.

Next -> [[Tokens in OAuth]]
