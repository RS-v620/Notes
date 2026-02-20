#oauth2 #web #authentication

If the client-type is kept confidential, the client and the authorizations server can establish a client authentication method suitable for the security requirements of the authorization server. The authorization MAY accept any form of client authorization meeting its security criteria.

Confidential clients are typically issued ( or establish ) a set of client credentials used for authenticating with the authorization server (e.g password, public/private key pair)
The authorization server MAY establish a client authentication method with public clients. However, the authorization server MUST NOT rely on public client authentication for the purpose of identifying the client.

Furthermore the client MUST NOT use more than one authentication method in each request.

## Client Password
Clients in possession of a password may use the HTTP Basic Authentication scheme as defined in ***RFC2617*** to authenticate with the authorization server. The client identifier is encoded using the 
```
application/x-www-form-urlencoded
```
encoding algorithm and the encoded value is used as the username; the client password is encoded the same and used as the password. The authorization server must support the HTTP Basic authentication scheme for authenticating the clients that were issues a client password.

Examples :
```
Authorization : Basic dwerFerW3ASDweRQ#RASDwrqweasq3412eqwd!Eqwr23r
```

Or:
```
client_id:
	REQUIRED 
client_password:
	REQUIRED
``` 
NOT RECOMMENDED
> [!danger] Using the client credentials in the body using the two parameters is NOT RECOMMENDED and SHOULD be limited to clients unable to directly utilize the HTTP basic authentication scheme. 

> [!danger] The parameters can only be transmitted in the request body and MUST NOT be included in the request URI

> [!important] The authorization server MUST require the use of TLS when sending requests using password authentication. Since this client authentication method involves a password, the authorization server MUST protect any endpoint utilizing it against brute force attacks.


## Other Authentication Methods

The authorization server MAY support any suitable HTTP authentication scheme matching its security requirements. When using other authentication methods, the authorization server MUST define a mapping between the client identifier (registration record) and
authentication scheme.