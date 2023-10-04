# References
https://github.com/khatwaniNikhil/SystemDesign/blob/main/jwts-not-safe-e-book.pdf

# Cookie based sessions
1. http sessions data is generally stored on server side db for safety and
session token(opaque, random string) is returned to client as cookie which is exchanged in subsequent requests.
2. every sesion will have expiry ttl, in case of oauth session can be lifetime can be fine grained controlled via access token and refresh token.

## Drawbacks
1. session data lookup from db for every http request does not scale well in high loads.
2. sticky sessions also won't be scalable

# JWT
1. eliminates db calls for session data lookup altogether
2. JWT token itself stores the session data(header + payload + signed secret(verfied at server side))

## security issues
1. bypass JWT signature validation by passing algo as "none"
2.  If you change the algorithm in the JWT header from RS256 to HS256, the backend code uses
the public key as the secret key and then uses the HS256 algorithm to verify the signature.
3. claims are optional
4. Event with JWT, state needs to maintained for rate limiting and IP-whitelisting.
5. No concept of logout and JWT token can be used until expired.
6. Other than blocking bad users, we need to maintain blacklist list of JWT tokens.
7. JWT tokens could be stale(let say admin user demoted to regular user), in this case until expired JWT token can be used for admin allowed actions.

# Alternative - Blazing fast DB lookup - Redis
