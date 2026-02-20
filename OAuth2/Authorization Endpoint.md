#web #oauth2

The authorization endpoint is used to interact with the resource owner and obtain an authorization grant.. The authorization serve MUST first verify the identity of the resource owner. They way in which the authorization server authenticates the resource owner is not part of this specification.
Also, the means through which the client obtains the location of the authorization endpoints are beyond the scope of this specification as well [^1].
Since requests to the authorization endpoint result in user authentication and the transmission of clear-text credentials in the HTTP response, the authorization server MUST require the use of TLS when sending requests to the authorization endpoint.


## Response Type 
#revisit-to-update
The authorization endpoint is used by the authorization code grant type and implicit grant type flows. The client informs the authorization server of the desired grant type using the following parameter:
```
response_type:
	REQUIRED. The value must belong to one of the codes as described in [[]]
```

## Redirection Endpoint
After completing its interaction with the resource owner, the authorization server directs the resource owner’s user-agent back to the client. The authorization server redirects the user-agent to the client’s redirection endpoint previously established with the authorization server during the client registration process or when making the authorization request.
The redirection URI must be an absolute URI as defined in *RFC3986*.
No fragment components should be part of the request.

### Endpoint Request Confidentiality
The redirection endpoint SHOULD require the use if TLS when the quested response type is "code" or "token", or when the request will result in sensitive information transformation in the response.
### Registration Requirement 
 ^3.1.2.2
The authorization server MUST require the following clients to register their redirection endpoint:
- Public clients
- Confidential clients utilizing the implicit grant type
All authorizations must only happen after a redirection URL has been registered. Multiple redirections can be registered.

> [!danger] Lack of a redirection URI registration requirement can enable an attacker to use the authorization endpoint as an open redirector 

### Dynamic Configuration

 The client must provide a redirection URI in the cases where:
 - Multiple redirection URIs have been registered, or 
 - Only partial redirection URI has been provided, or
 - No Redirection URI has been registered
In case of multiple registrations, string comparison of the URIs registered and incoming must be performed to check validity  ^3.1.2.3

### Invalid Endpoint
 If an authorization request fails, the authorization server should inform the client of the error such as:
 - Missing redir-URL
 - Invalid redir-URL
 Any redirection in this case must be stopped then
  ^3.1.2.4
 

Next -> [[Token Endpoint]]

[^1]: This means the endpoints must be provided either in code, or need to find a way to get the endpoints delivered
