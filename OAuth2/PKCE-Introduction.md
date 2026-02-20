#app #oauth2 #pcke

OAuth 2.0 [RFC6749](https://datatracker.ietf.org/doc/html/rfc6749) public clients are susceptible to the authorization code interception attack.

In this attack, the attacker intercepts the authorization code returned from the authorization endpoint within a communication path not protected by Transport Layer Security ( TLS ), such as inter-application communication within the client's operating system. 

Once the attackers have access to the authorization code, it can be used to obtain access token.
Figure 1 shows the attack graphically. In step (1), the native application running on the end device, such as a smartphone, issues an OAuth 2.0 Authorization Request via the browser/operating system. 
The Redirection Endpoint URI in this case typically uses a custom URI scheme.  Step (1) happens through a secure API that cannot be intercepted, though it may potentially be observed in advanced attack scenarios.  The request then gets forwarded to the OAuth 2.0 authorization server in step (2).  Because OAuth requires the use of TLS, this communication is protected by TLS and cannot be intercepted.  The authorization server returns the authorization code in step (3).  In step (4), the Authorization Code is returned to the requester via the Redirection Endpoint URI that was provided in step (1).

Note that it is possible for a malicious app to register itself as a handler for the custom scheme in addition to the legitimate OAuth 2.0 app.  Once it does so, the malicious app is now able to intercept the authorization code in step (4).  This allows the attacker to request and obtain an access token in steps (5) and (6), respectively.

![[PKCE-Introduction 2025-10-07 14.51.15.excalidraw | 700x300]]


A number of pre-conditions need to hold for this attack to work:
1. The client manages to register a malicious application on the client device and registers a custom URI scheme that is also used by another application. The operating systems must allow a custom URI scheme to be registered by multiple applications.
2. The OAuth 2.0 Authorization "code grant" is used.
3. Either one of the following conditions is met:
	1. The attacker ( via the installed application ) is able to observe only the responses from the authorization endpoint. When `code_challenge_method` value is `plain`, only this attack is mitigated
	2. A more sophisticated attach scenario allows the attacker to observe requests ( in addition to responses ) to the authorization endpoint. The attacker is however, not able to act as a man in the middle. This can be caused by leakage of HTTP log information in the OS. To mitigate this, `code_challenge_method` value must be set either to `S256` or a value defined by a cryptographically secure `code_chanllenge_method` extension.
While the pre-conditions are specific and extensive, attacks have been observed.  While the OAuth 2.0 threat model ([Section 4.4.1 of [RFC6819]](https://datatracker.ietf.org/doc/html/rfc6819#section-4.4.1)) describes mitigation techniques, they are, unfortunately, not applicable since they rely on a per-client instance secret or a per-client instance redirect URI.

  To mitigate this attack, this extension utilizes a dynamically created cryptographically random key called "**code verifier**". A unique code verifier is created for every authorization request, and its transformed value, called "code challenge", is sent to the
   authorization server to obtain the authorization code.  The authorization code obtained is then sent to the token endpoint with the "code verifier", and the server compares it with the previously received request code so that it can perform the proof of possession
   of the "code verifier" by the client.  This works as the mitigation since the attacker would not know this one-time key, since it is sent over TLS and cannot be intercepted.
## Protocol Flow

![[PKCE-Introduction 2025-10-07 15.39.59.excalidraw | 700]]This specification adds additional parameters to the OAuth 2.0  Authorization and Access Token Requests, shown in abstract form in Figure above.

> A. The client creates and records a secret named the `code_verifier`
      and derives a transformed version "t(`code_verifier`)" (referred to
      as the "`code_challenge`"), which is sent in the OAuth 2.0
      Authorization Request along with the transformation method "t_m".
  >B. The Authorization Endpoint responds as usual but records "t(`code_verifier`)" and the transformation method.
  >C. The client then sends the authorization code in the Access Token
      Request as usual but includes the "`code_verifier`" secret generated
      at (A)
  >D. The authorization server transforms "`code_verifier`" and compares
      it to "t(`code_verifier`)" from (B).  Access is denied if they are
      not equal.

An attacker who intercepts the authorization code at B) is unable to redeem it for an access token, as they are not in possession of the "`code_verifier`" secret.

Next -> [[Notational Conventions]]
