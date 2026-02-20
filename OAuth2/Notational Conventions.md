The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in "Key words for use in RFCs to Indicate Requirement Levels" [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119). If these words are used without being spelled in uppercase, then they are to be interpreted with their natural language meanings. This specification uses the Augmented Backus-Naur Form (ABNF) notation of [RFC5234](https://datatracker.ietf.org/doc/html/rfc5234).

STRING denotes a sequence of zero or more ASCII [RFC20](https://datatracker.ietf.org/doc/html/rfc20) characters. OCTETS denotes a sequence of zero or more octets. ASCII(STRING) denotes the octets of the ASCII [RFC20](https://datatracker.ietf.org/doc/html/rfc20) representation of STRING where STRING is a sequence of zero or more ASCII characters.

BASE64URL-ENCODE(OCTETS) denotes the base64url encoding of OCTETS, per [Appendix A](https://datatracker.ietf.org/doc/html/rfc7636#appendix-A), producing a STRING. BASE64URL-DECODE(STRING) denotes the base64url decoding of STRING, per [Appendix A](https://datatracker.ietf.org/doc/html/rfc7636#appendix-A), producing a sequence of octets.

SHA256(OCTETS) denotes a SHA2 256-bit hash [RFC6234](https://datatracker.ietf.org/doc/html/rfc6234) of OCTETS.


Next -> [[Terminology]]