#web #oauth2  ^4.1

The authorization code grant type is used to obtain both access tokens and refresh tokens and is optimized for confidential clients. Since this is a redirection-based flow, the client must be capable of interacting with the resource owner's user-agent ( typically a web browser ) and capable of receiving incoming requests ( via redirection ) from the authorization server:

![[Authorization Code Grant 2025-10-07 11.44.38.excalidraw | 600x400]]


## Authorization Request
The client constructs the request URI by adding the following parameters to the query component of the authorization endpoint URI using the 
```
"application/x-www-form-urlencoded"
``` 
format
```
response_type:
	REQUIRED.
	Value MUST be set to "code".
client_id
	REQUIRED.
	The client identifier as described in Section 2.2.
redirect_uri
	OPTIONAL.
	As described in Section 3.1.2 .
	
scope
	OPTIONAL. The scope of the access request as described by
			  Section 3.3.
state
	RECOMMENDED. An opaque value used by the client to maintain
	state between the request and callback. The authorization
	server includes this value when redirecting the user-agent back
	to the client. The parameter SHOULD be used for preventing
	cross-site request forgery as described in Section 10.12.

```


---
===It is expected that thus far the documentation has covered the basics of the protocoal and following sections comprise of the technical specifications and implementation details. Hence the study is being stopped here as the goal is not to build an OAuth Server, but rather to understand its working to make appropriate use of it===

Next -> [[Navigation]]