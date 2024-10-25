---
tags:
  - Github
  - Guide
  - Security
---
# Requirements

- FrontEndUI
- BackEndServer
- Registered OAuth app with Github

# Flow

![[/Untitled 31.png|Untitled 31.png]]

1) Client redirects `onClick` to GitHub auth page with url params

- `scope` - access requested
- `clientid` - generated when registering OAuth app with Github
- `state` (optional) - extra security
- `redirectUri` - Where user is sent back to after authorization

2) Github prompts user to authorize

![[/Untitled 1 18.png|Untitled 1 18.png]]

3) Github redirects back to `redirecturi` with optional `state` and `code`

4) Client makes `POST` request to Server with the `code`

5) Server uses `code`, `clientSecret`, `state`, `clientId`, to request `access_token`

`https://github.com/login/oauth/access_token`

6) Server can make Github API requests on behalf of the Client based on the requested `scope`

# Links

> [!info] Basics of authentication - GitHub Docs  
> In this section, we're going to focus on the basics of authentication.  
> [https://docs.github.com/en/rest/guides/basics-of-authentication](https://docs.github.com/en/rest/guides/basics-of-authentication)  

> [!info] Endpoints available for fine-grained personal access tokens - GitHub Docs  
> Your fine-grained personal access token can make requests to the following REST endpoints.  
> [https://docs.github.com/en/rest/overview/endpoints-available-for-fine-grained-personal-access-tokens#orgs](https://docs.github.com/en/rest/overview/endpoints-available-for-fine-grained-personal-access-tokens#orgs)  

> [!info] How to Implement GitHub OAuth in Your Node.js App  
> OAuth is a standard protocol used for authorization.  
> [https://fusebit.io/blog/github-oauth/?utm_source=www.google.com&utm_medium=referral&utm_campaign=none](https://fusebit.io/blog/github-oauth/?utm_source=www.google.com&utm_medium=referral&utm_campaign=none)