#web #oauth2 #pcke  ^4

## Client Creates a Code Verifier
The client creates a `code_verifier`, for each OAuth2.0 Authz Request, in the following manner:

`code_verifier` = high-entropy cryptographic random STRING using the unreserved characters `[A-Z] / [a-z] / [0-9] / "-" / "." / "\_" / "~"` from [Section 2.3 of [RFC3986]](https://datatracker.ietf.org/doc/html/rfc3986#section-2.3), with a minimum length of 43 characters  and a **maximum length of 128 characters**.

ABNF for "code_verifier" is as follows:
code-verifier = `43\*128unreserved`
unreserved = `ALPHA / DIGIT / "-" / "." / "\_" / "~"`
ALPHA = `%x41-5A / %x61-7A`
DIGIT = `%x30-39`

> [!note] The code verifier SHOULD have enough entropy to make it impractical to guess the value.  It is RECOMMENDED that the output of a suitable random number generator be used to create a 32-octet sequence.  The octet sequence is then base64url-encoded to produce a 43-octet URL safe string to use as the code verifier.

## Client Creates the Code Challenge
 ^4.2
 The client then creates a code challenge derived from the code verifier by using one of the following transformations on the code verifier:
 plain
	`code_challenge = code_verifier`

S256
	`code_challenge = BASE64URL-ENCODE(SHA256(ASCII(code_verifier)))`

If the client is capable of using "`S256`", it MUST use "`S256`", as "`S256`" is Mandatory To Implement (MTI) on the server.  Clients are permitted to use "`plain`" only if they cannot support "S256" for some technical reason and know via out-of-band configuration that the server supports "`plain`".

The plain transformation is for compatibility with existing deployments and for constrained environments that can't use the S256 transformation.

ABNF for "code_challenge" is as follows.

   code-challenge =` 43*128unreserved`
   unreserved = `ALPHA / DIGIT / "-" / "." / "_" / "~"`
   ALPHA = `%x41-5A / %x61-7A`
   DIGIT = `%x30-39`

## Client Send the code challenge with the Authorization Request

The client sends the code challenge as part of the OAuth 2.0 Authorization Request ([Section 4.1.1 of [RFC6749]](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.1)) using the following additional parameters:
```
code_challenge
      REQUIRED.  Code challenge.

code_challenge_method
      OPTIONAL, defaults to "plain" if not present in the request.  Code
      verifier transformation method is "S256" or "plain".
```

## Server returns the code
 ^4.4
When the server issues the authorization code in the authorization response, it MUST associate the "code_challenge" and "code_challenge_method" values with the authorization code so it can be verified later.

Typically, the "`code_challenge`" and "`code_challenge_method`" values are stored in encrypted form in the "code" itself but could alternatively be stored on the server associated with the code.  The server MUST NOT include the "`code_challenge`" value in client requests in a form that other entities can extract.

The exact method that the server uses to associate the "`code_challenge`" with the issued "code" is out of scope for this specification.

### Error Response
 ^4.4.1
When the server issues the authorization code in the authorization response, it MUST associate the "`code_challenge`" and "c`ode_challenge_method`" values with the authorization code so it can be verified later.
Typically, the "`code_challenge`" and "`code_challenge_method`" values are stored in encrypted form in the "code" itself but could alternatively be stored on the server associated with the code.  The server MUST NOT include the "`code_challenge`" value in client requests in a form that other entities can extract.

The exact method that the server uses to associate the "`code_challenge`" with the issued "code" is out of scope for this specification.


## Client Sends the Authorization Code and the Code Verifier to the Token Endpoint
 ^4.5
 Upon receipt of the Authorization Code, the client sends the Access Token Request to the token endpoint.  In addition to the parameters defined in the OAuth 2.0 Access Token Request ([Section 4.1.3 of [RFC6749]](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.3)), it sends the following parameter:
```

code_verifier
      REQUIRED.  Code verifier
```

 The "`code_challenge_method`" is bound to the Authorization Code when the Authorization Code is issued.  That is the method that the token endpoint MUST use to verify the "`code_verifier`".


## Server Verifies code_verifier before returning the tokens
 ^4.6
Upon receipt of the request at the token endpoint, the server verifies it by calculating the code challenge from the received "`code_verifier`" and comparing it with the previously associated "`code_challenge`", after first transforming it according to the "`code_challenge_method`" method specified by the client.

If the "`code_challenge_method`" from [Section 4.3](https://datatracker.ietf.org/doc/html/rfc7636#section-4.3) was "S256", the received "`code_verifie`r" is hashed by SHA-256, base64url-encoded, and then compared to the "`code_challenge`", i.e.:

   `BASE64URL-ENCODE(SHA256(ASCII(code_verifier))) == code_challenge`
   

   If the "`code_challenge_method`" from [Section 4.3](https://datatracker.ietf.org/doc/html/rfc7636#section-4.3) was "plain", they are compared directly, i.e.:

   `code_verifier == code_challenge.`

 If the values are equal, the token endpoint MUST continue processing as normal (as defined by OAuth 2.0 [RFC6749](https://datatracker.ietf.org/doc/html/rfc6749).  If the values are not equal, an error response indicating "`invalid_grant`" as described in [Section 5.2 of [RFC6749]](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2)  MUST be returned.

Next -> [[Compatibility]]




