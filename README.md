# SSO Node.js Middleware

This repo consists of a simple example to showcase how to use and implement the node middleware for the DevClub SSO.   
[Link to SSO Server](https://github.com/devclub-iitd/SingleSignOn)  

## Prerequisites to use the SSO Server:

-   A public key provided by the SSO for handling verification of tokens.
-   A node.js based client server.

## Setup:

-   Clone the repo to your client server machine.
-   Include the `auth.js` files inside your source directory.
-   Add the `auth` middleware function as a request handler to routes that you want to be private.

Example :

```
// Using the middleware on a single route
app.get('/privatePath', auth, (req,res) => {
// Your route logic here
});

// Using the middleware on a single router
const router = express.router();
router.use(auth);

// Using the middleware on every path
const app = express();
app.use(auth);
```

-   Done!

## Options :-

Several options have been included inside the `auth.js` file to help configure the client server. These have been documented below :

1. **Sever Config Options** : These configuration options will be provided in the `config` file provided by CASI while registering the client
    -   `SSO_Refresh_URL : String` : The url to which refresh token requests will be sent to when the token is about to expire.
    -   `SSO_Login_URL : String` : The url to which users will be redirected to, if they have to login.
    -   `clientURL : String` : The complete root address of the client service.
    -   `accessTokenName : String` : The name of the JWT token cookie.
    -   `refreshTokenName : String` : The name of the JWT 'remember me' style token. These tokens can remember the user for upto 2 days.
    -   `maxTTL : number` : The maximum token age left after which a refresh request will be sent to `SSO_Refresh_URL`.
    -   `redirectURL : String` : The URL for handling redirects if the token signature is corrupted or the user session expires or the token is simply, not available. If set to `null`, the user will simply be redirected to the `SSO_Login_URL` in case an error occurs.
2. **Client Config Options** : These are some of the customizations that a client can utilize to fit its needs.
    -   `publicKey` : The path to the RSA public key provided by the SSO for verifying the token signature.
    -   `publicPaths : array<String>` : An array of regexes which will be checked against a path to determine if it is publicly accessible or not.
    - `ROLES` : A dictionary mapping route regexes to corresponding role arrays. If the path accessed (`req.url`) regex-matches with any of the keys in this dictionary then the user is authorized if and only if he has all the roles specified in the corresponding value of the key (a role array). See `ROLES` in `auth.js` for an example.
    - `defaultRoles : array<String>` : An array of roles that all authorized users should have when accessing non-public paths. 
    - `UnauthorizedHandler` : This is a view that should be called if the user is unauthorized to access a given path.

## Implementation of public / private paths :

At present, their are two methods to implement public / private paths.

1.  If you have a small number of public paths, the easiest way to implement a public path is to use the `publicPaths` array, and simply populate the array with a string of paths that you wish to keep public.
2.  If you have a large number of routes that do not need to be protected include the `auth` function as a request handler only to the routers which lead to private routes.

#### Note : The auth middleware is simply a request handler and any router which does not include the `auth` function will not be checked for token validity by the middleware.
