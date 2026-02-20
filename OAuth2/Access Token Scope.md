#web #oauth2  ^3.3
The authorization and token endpoints allow the client to specify the scope of the access request using the "scope" request parameter. In turn, the authorization server uses the "scope" response parameter to inform the client of the scope of the access token issued.
The value of the scope parameter is expressed as a list of space- delimited, case-sensitive strings. The strings are defined by the authorization server. If the value contains multiple space-delimited strings, their order does not matter, and each string adds an additional access range to the requested scope.
```
scope =        scope-token *(SP scope-token)
scope-token =  1 * ( %x21 / %x23-5B / %x5D-7E )
```

If the client omits the scope parameter when requesting authorization, the authorization server MUST either process the request using a pre-defined default value or fail the request The authorization server MAY fully or partially ignore the scope requested by the client, based on the authorization server policy or the resource owner’s instructions. 

 Next -> [[AO-Introduction]]