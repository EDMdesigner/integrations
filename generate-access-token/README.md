# Generate Access Token

Every new generation plugin at EDMdesigner verifies our API partner based on an access token. You will have to send an HTTP POST request to https://auth.edmdesigner.com and you will get a JWT that you have to pass to your plugin.

## POST getAccessToken
An endpoint to get an accessToken for a user of an API-partner. 
The endpoint has to be called by the publisher’s (API partner) **server side**, with its personal secret (pwd). The retrieved token has to be passed to the user, who will use only that token to authorize itself. 
It is strictly forbidden to pass the publisher’s secret to the client side or to the user.

The POST data has to have the following key-value pairs:
```js
{
	"APIkey": string, APIkey of the partner (mandatory),
	"pwd": string, secret of the API partner (mandatory),
	"components": bool or object, requested components (mandatory) (for details see below)
}
```

The response looks like this:
```js
{
	"error": string with the error details, null if everything was okay,
	"token": string, the JWT token, or null if an error occurred (for details see below).
}
```

## The accessToken component structure

The detailed access rights are organized in a nested object. The key represents a service name or a detail in a service, the value can be a boolean or an object with the same rules.
The true value represents a general access right to the part represented by the key, whereas false represents a complete denial. If a key is missing, it is equivalent to the false value.

Example:
```js
"components": {
	"gallery": true,
	"complex": {
		"addNew": false,
		"browse": true
	},
	"commenting": false
}
```

The getAccessToken method checks the requested component structure with the granted component list for the API partner, and if needed, removes those components that are requested but the API partner has no right to them.
It also checks the nested access rights. If a requested component is true, but the granted is an object with some subproperties, the token will contain the granted access list as an object.

## The accessToken Structure

The accessToken is a JSON Web Token. JWT is a string with exactly 3, base64-encoded parts, divided by a dot (.): XXX.YYY.ZZZ

- The JWT header (XXX) is a JSON, containing the basic information about the JWT: the hash algorithm (HS256) and the type (JWT).
- The JWT payload (YYY) is a JSON containing the
  - the APIkey, 
  - the requested component list (components), 
  - the time of the JWT was issued (iat) in UNIX epoch, and 
  - the expiration of the token in UNIX epoch. The default expiration time is 1 hour.
- The JWT hash (ZZZ) is the hash of the token data.

## POST verifyAccessToken

Every plugin and its backend verifies that the API partner uses a valid access token.

An endpoint to verify an accessToken.
The endpoint has to be called by the client-side component (superpreview, content-editor, etc.), by the client-side code (from the iframe) to check if the user has the right to utilize the component.

The POST data has to have only one field:
```js
{
	"accessToken": string, the accessToken (mandatory, offkorsz)
}
```

and responds with the following JSON:
```js
{
	"error": if the validation was successful, null, string otherwise with the details of the fail,
	"result": true | false - the result of the validation
}
```

The token can be invalid due to more reasons:
- Invalid coding - the token is not base64-encoded
- Invalid format - the decoded token is not a JWT or could not been parsed as a JSON
- Token expired - the expiration specified in the token is past due
- Hash error - the hashed token does not match with the provided hash.
