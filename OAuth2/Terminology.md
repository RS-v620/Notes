#app #oauth2 #pcke
In addition to the terms defined in OAuth 2.0 [RFC6749](https://datatracker.ietf.org/doc/html/rfc6749), this specification defines the following terms:

**code verifier** A cryptographically random string that is used to correlate the authorization request to the token request

**code challenge** A challenge derived from the code verifier that is sent in the authorization request to the token request.

**code challenge method** : A method that was used to derive the code challenge.

**Base64url Encoding**: Base64 encoding using the URL - and filename-safe character set defined in [Section 5 of [RFC4648]](https://datatracker.ietf.org/doc/html/rfc4648#section-5) , with all trailing '=' characters omitted and without the inclusion of any line breaks, whitespace, or other additional characters. (See [Appendix A](https://datatracker.ietf.org/doc/html/rfc7636#appendix-A) for notes on implementing base64url encoding without padding.)


## Abbreviations

- **ABNF** : Augmented Backus-Naur Form
- **Authz**: Authorization
- **PCKE**: Proof Key for code Exchange
- **MITM**: Man in the middle
- **MTI**: Mandatory to Implement

Next -> [[PCKE-Protocol]]
