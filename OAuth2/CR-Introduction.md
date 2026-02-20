#web #oauth2
Before initializing the protocol, the client registers with the authorization server. The means through which the client registers with the authorization server are not part of this specification [^1], but typically involve end-user interaction with an HTML registration form.

Client registration does not require a direct interaction between the client and the authorization server. When supported by the authorization server, registratior can rely on other means for establishing trust and obtaining the required client properties ( e.g. redirection URL, client-type). For example, registration can be accomplished using a self-issued or ==third-party-issued assertion==, or by authorization server performing ==client discovery using a trusted channel==

When registering a client, the client developer SHALL:
1. Specify the client type as described in [[Client Types]]
2. Provide the client redirection URI as described in [[Authorization Endpoint]]
3. Include mandatory information as requested by the authorization server

[^1]: This means we have to handle the registration somehow, #find-out #missing-link

Next -> [[Client Types]]
