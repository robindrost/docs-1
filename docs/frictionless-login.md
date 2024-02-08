#Frictionless Authentication
Frictionless authentication is a feature that allows users to be granted authorization without requiring them to manually interact with the Livery Interactive Client. For instance, if a user is logged into a customer's platform and there is a Livery Video Player on a web page within that platform, the page can pass a set of credentials to the Livery Video Player, and the Interactive Client will automatically log the user into the Livery system. As a result, the end user doesn't need to log in to the Livery system separately from the surrounding platform, making the authentication process ‘Frictionless’.

Livery accommodates; Verified and Unverified Frictionless, the way how the data is provided is the same for verified and unverified Frictionless. It either uses a query parameter or Javascript method to provide the user information. In case of “Verified” frictionless the provided JWT token is signed with RS256 or HS256 symmetric algorithm that shares one secret key between the identity provider and Livery. The additional verification of the token makes verified frictionless more secure, but requires a bit more effort/knowledge for the customer.  The following tool can be used to create Signed JWT token for testing purposes: https://jwt.io/ 
“Unverified” frictionless allows the customer to provide the claims directly, without the usages of a token. This approach requires less effort but is more insecure, compared to Verified Frictionless. 

The user-data (Token or Claims) can be provided via a Javascript method or as part of the URL initializing the interactive client. 

Javascript method example of Unverified Frictionless:
```
setLiveryUser({sub: '1234', preferred_username: 'Livery'})
```

Javascript method example of Verified Frictionless:
```
setLiveryUser({token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiSGVsbCBvIn0.fgzQ8ps0S63kiD_fQ5F2uEN4IQyufN2pkorwW0fCBSEqpV14GOSdV4cgk4MLYGE'})
```

Query parameter method example of Unverified Frictionless:
```
...&livery_sub=12345&livery_preferred_username=Livery
```

Query parameter method example of Verified Frictionless:
```
…&livery_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiSGVsbCBvIn0.fgzQ8ps0S63kiD_fQ5F2uEN4IQyufN2pkorwW0fCBSEqpV14GOSdV4cgk4MLYGE
```

Livery follows the OpenId Connect specification supporting all default and some additional claims. The supported claims are listed under ‘Profile configuration’ in Livery’s Interactive Portal. Please contact the Livery team if you require any specific claims. 
The ‘sub’ is the only mandatory claim that needs to be provided once the user-data is shared via the JavaScript method or URL parameter. However, please note that when a user edits their information within the Livery system, the surrounding context won’t be informed.

The query parameter or Javascript method can be used to create new users or to update the existing user-data. To update individual fields, the data is merged with the existing user data (as long as the sub field remains unchanged). If the value of a field is undefined, it indicates that the value shouldn't change.
If the ‘sub’ value is different from the previous call or is null, the client will log the user out of the server, clear their local user info, and create a new user. This allows end-users to switch accounts on a customer's platform without the Livery system considering it still a single user. However, it also means that every account switch will create a brand new user within the Livery system and it will not be possible to log back into the platform for users created via the Unverified frictionless method. 

## Enabling Frictionless Authentication:
To activate Verified or Unverified Frictionless Authentication, include it in the ‘Authentication Templates’ within the Livery Interactive Portal. Authentication won't function unless it's defined in the template, and it's not possible to use both Verified and Unverified methods simultaneously. Verified Frictionless requires a Public Authentication Key (raw key or PEM), set in the portal's ‘Settings’ under ‘Authentication Settings’.
The authentication template’s configured profile fields, or user claims, should align with those provided during frictionless login. Livery’s Interactive Client shows the missing data screen, if either the token or the query parameters misses any of the required user data.

Some of Livery’s Profile fields/claims, like Phone number or Email address, can be verified. Verification flags can only be applied through verified tokens, and updating data without these flags reverts the status to unverified.

