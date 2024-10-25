## Terms

**Authorization** - process of granting or denying access to resources or system based on users identity

**Authentication** - verify identity of user or system. Confirms that a user is who they say they are and allows access once their identity has been verified

**OAuth** - used for authorization. Allows user to grant access to a third-party application to access their resources without giving the third-party application their credentials. It does not do authentication. ⇒ purely about delegating access

**OpenID Connect** - Built on top of OAuth. Allows user to authenticate with an identity provider (Facebook, Google, etc.) which issues an `ID Token` to the user containing the users identity. The token is used to grant access ⇒

  

## OAuth

- About Authorization

  


Client ⇒ backend web application

1) client requests access that triggers redirect

2) browser redirects to Auth server

3) Auth server asks user to authenticate (login creds) (auth server is Github, Facebook, Google, etc.)

4) User logs in

5) Auth server tells user that third-party application (the client) is requesting access to resources

6) User authorizes the client to get access

7) Auth server issues authorization code via the callback url

- callback URL is configured when the OAuth application is registered with the Authorization server, i.e. registered with Github, Facebook, Google
- Authorization code does NOT grant access
- Authorization code is a one-time use and only valid for a short period of time

8) Client gets the authorization code

9) Client exchanges Authorization code + Credentials for access token

- credentials are a secret code that is set up when OAuth application is registered
- secret code must live in the BE

10) Authorization server gives the client an access token

- key difference in OIDC ⇒ in OIDC we return an identity token AND an access token

11) Access token is used by the client to make requests to the resource server

This flow allows a User to authorize a client to access resources on their behalf

- steps 9 and 10 are back channel (machine to machine) communication and are more secure

## OpenID Connect

- About authentication
- Same flow as OAuth BUT instead of issuing an Access Token, it issues an Identity Token


## Resources

> [!info] Authorizing OAuth Apps - GitHub Docs  
> GitHub's OAuth implementation supports the standard authorization code grant type and the OAuth 2.  
> [https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps](https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps)  

> [!info] How do I implement social login with GitHub accounts?  
> I've been asked by my employer to implement a log-in system for our web application using users' GitHub accounts.  
> [https://stackoverflow.com/questions/71741596/how-do-i-implement-social-login-with-github-accounts](https://stackoverflow.com/questions/71741596/how-do-i-implement-social-login-with-github-accounts)  

> [!info] How could we use GitHub account as an AWS Cognito Identity provider?  
> Since first writing this answer, I implemented and published a project that provides a wrapper for Cognito to talk to GitHub.  
> [https://stackoverflow.com/questions/44397268/how-could-we-use-github-account-as-an-aws-cognito-identity-provider](https://stackoverflow.com/questions/44397268/how-could-we-use-github-account-as-an-aws-cognito-identity-provider)  

> [!info] How to Implement "Login with GitHub" in a React App  
> I came across a scenario where I needed to implement a "Login with Github" feature in a React App using Create React App.  
> [https://levelup.gitconnected.com/how-to-implement-login-with-github-in-a-react-app-bd3d704c64fc](https://levelup.gitconnected.com/how-to-implement-login-with-github-in-a-react-app-bd3d704c64fc)  

[https://fusebit.io/blog/github-oauth/?utm_source=www.google.com&utm_medium=referral&utm_campaign=none](https://fusebit.io/blog/github-oauth/?utm_source=www.google.com&utm_medium=referral&utm_campaign=none)

> [!info] OAuth 2.0 and OpenID Connect (in plain English)  
> Developer Advocate Nate Barbettini breaks down OpenID and OAuth 2.  
> [https://www.youtube.com/watch?v=996OiexHze0&list=PLOCsNO5dco0Hk1-Jluf-gayxB488O3cQd&index=2](https://www.youtube.com/watch?v=996OiexHze0&list=PLOCsNO5dco0Hk1-Jluf-gayxB488O3cQd&index=2)  

[https://pragmaticwebsecurity.com/files/talks/introductionoauth.pdf](https://pragmaticwebsecurity.com/files/talks/introductionoauth.pdf)
