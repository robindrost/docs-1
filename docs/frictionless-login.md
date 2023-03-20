# Frictionless Auth

Frictionless authentication is a feature that allows users to be granted authorization without requiring them to manually interact with the Livery Interactive Client. For instance, if a user is logged into a customer's platform and there is a Livery Video Player on a web page within that platform, the page can pass a set of credentials to the Livery Video Player, and the Interactive Client will automatically log the user into the Livery system. As a result, the end user doesn't need to log in to the Livery system separately from the surrounding platform, making the authentication process "frictionless".

Livery provides 2 solutions; Verified and Unverified Frictionless, the result of both appoaches is the same but the way the user-date is provided to the Interactive client is different. Unverified Frictionless uses a Javascript method to the interactive client with the user-data. Verified Frictionless validates the recieved user-date via token and requers more effort for the cusotmer to implmeet, but is more secure.  

## Unverified Frictionless Auth
The LiveryPlayer component has a method called setUserUnverified(id_token), which accepts a JavaScript Object representation of an OpenID Connect id token as an argument and returns a Promise that resolves upon success. All fields are optional, and for the unverified frictionless auth feature, only the preferred_username and sub fields are important. End-users can view and manually edit any fields typically visible and editable for 'guest' users. However, please note that when a user edits their information within the Livery system, the surrounding context won't be informed.

When users are created using setUserUnverified(id_token), they'll use the default profile icon and be assigned a random color for that icon, the same behavior as when an end-user creates a guest account manually.

User details can be modified by making another call to setUserUnverified(id_token). This allows for updates to the user's information to be passed in by the surrounding web page. To update individual fields, the data is merged with the existing user data (as long as the sub field remains unchanged). If the value of a field is undefined, it indicates that the value shouldn't change. If the value of a field is null, it means the field should be removed (i.e., it should become undefined).

When calling setUserUnverified(id_token), if the id_token's sub value is different from the previous token or is null, the client will log the user out of the server, clear their local user info, and create a new user. This allows end-users to switch accounts on a customer's platform without the Livery system considering it still a single user. However, it also means that every account switch will create a brand new user within the Livery system. Whenever a new user is created this way, no profile data will be carried over from the previous user. The values in the remaining fields of the argument will be applied as if the previous value were undefined.

Please note that within the Livery system, the individual user is identified by the unique id of their connection to the server, not by the id token or any information within it. As a consequence, identities are not portable between devices, and clearing the browser's local storage will detach the user from their progress.

<!---
NOT SUPPORTED YET
If any fields in the passed id token are invalid, the setUserUnverified( id_token ) call will reject with an error.

It may be that certain fields are marked as mandatory for allowing a user to participate in a broadcast as a guest user. Should any of those fields be missing the end user will be immediately directed to an interface where they will be requested to provide the missing mandatory information. The end user is not able to continue until they manually provide that information.
-->
The Livery client support the following fields:

#### preferred_username (optional)
preferred_username is the username that will be used within the broadcast. Note that uniqueness is not enforced for this field; there can be several users with the same username. If no value is passed for this field, or if an empty string is passed, the end user will be asked to enter a username. The end user will be able to edit this field.

#### sub (optional)
sub is the subject identifier, or an identifier by which the customer can uniquely identify the user. It must be a string of ASCII characters (max length 255). It is included in the customer's data exports. Since this information is unverified, uniqueness is not enforced. Note also that whenever setUserUnverified( id_token ) is called with a sub field that has changed, or a sub field of null, a new user will be created. The field is not shown on the user interface.

More fields will be added in the future. 

For a description of the OpenId Connect id token see the specification https://openid.net/specs/openid-connect-core-1_0.html. Specifically, the fields accepted in the id_token argument to setUserUnverified( id_token ) are those listed in Table 1 (§5.1 Standard Claims)
<!---
NOT SUPPORTED YET
### Unverified Frictionless Auth via query parameters

### Tracking data via query parameters

## Verified Frictionless Auth
To be announced
-->
