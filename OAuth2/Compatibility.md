#web #oauth2 #pcke ^5.0

Server implementations of this specification MAY accept OAuth2.0 clients that do not implement this extension.  If the "code_verifier" is not received from the client in the Authorization Request, servers supporting backwards compatibility revert to the OAuth 2.0 [[RFC6749](https://datatracker.ietf.org/doc/html/rfc6749)] protocol without this extension.  
As the OAuth 2.0 [[RFC6749](https://datatracker.ietf.org/doc/html/rfc6749)] server responses are unchanged by this specification, client implementations of this specification do not need to know if the server has implemented this specification or not and SHOULD send the additional parameters as defined in [Section 4](https://datatracker.ietf.org/doc/html/rfc7636#section-4) to all servers.


Next -> [[Navigation]]