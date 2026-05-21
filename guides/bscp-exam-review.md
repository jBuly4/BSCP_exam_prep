## General thoughts
- PortSwigger recommends to solve [23 labs from 23 topics](https://portswigger.net/web-security/certification/how-to-prepare/practitioner-labs-prep-step-one). Learn that topics and solve at least apprentice and 
  practitioner level laboratories - but start from xss, sqli and dom-based topics;
- systematize your payloads even if you won't use it. that at least will help you to refresh your knowledge and be 
  familiar which payload for which case;
- check and read these repos before exam - they're definitely going to help you:
  - https://github.com/DingyShark/BurpSuiteCertifiedPractitioner;
  - https://py-us3r.github.io/bscp-roadmap-bscproadmap/;
  - https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study;
  - don't forget to open browser tabs with them before exam;
- also prepare a tab with your own payloads;
- study all functionality of the challenge application according to vuln distribution on different stages;
- try harder and don't worry, stay calm:
  - you have enough time;
  - even if you took first vuln in 1.5 hours you have enough time to solve other 2 vulns;
  - if you stuck - stay calm and try harder.

## EXAM
### 1st APP
- Req smuggling + XSS
- BAC + cookie
- OS command injection with SSRF

#### Req smuggling + XSS
- scan specific paths (/, search function, /post/comment or something like this) - I've got tentative HTTP smuggling 
  for home page and High for /post/comment
- also there was User-Agent reflection in the comment form - that means that this case is similar to lab 
  **Exploiting HTTP request smuggling to deliver reflected XSS**
- to create correct payload - use requests from scanner and modify it. For me my lab payloads or payloads from other 
  repos didn't work, so I started to modify request from scanner step by step:
  - first - identify type (CL.TE or TE.CL) and get confirmation via differential responses;
  - second - trigger XSS;
  - third - add payload to send your cookies to collaborator;
  - fourth - use final payload to get carlos cookie.

#### BAC + cookie
- pay attention to your cookie;
- if you see the name of your account then it might be useful on stage 2;
- here I logged in as carlos and refreshed password, but changed username to administrator;
- then cookies of carlos with status **"isloggedin":true** are sent to server and in response you will get the same 
  cookie but instead of carlos username there will be administrator;
- copy that new cookie and use it => gain admin.

#### OS command injection with SSRF
- on the admin page at first I deleted user expecting to gain new cookie with serialized object but got nothing;
- then I found new function to work with image - but nothing in HTTP history, so I switched off filtering;
- then found GET /admin-panel/metrics/blogimages?image-name=2&ImageSize=%22200x133%21%22 - that is the vector for OS 
  command injection with SSRF. example payload you may find inside repos mentioned above;
- I think it is also possible to find that vector using Burp Scan, but I didn't scan it... >_<

### 2nd APP
- Host header injection
- Access control in JSON roleid
- RFI

#### Host header injection
- scanner found this vuln, but the difficulty was that I had to understand how to exploit it. After some time I noticed 
  that app has reflection of host header inside js source link on the forgot password page;
- next step - identify how to bypass validation of host header to get a working link to exploit server;
- found two combination: **exploit-address/app-address** - passed validation but didn't work, 
  **exploit-address?app-address** - worked well;
- then craft standard XSS payload to send session cookie to collab.

#### Access control in JSON roleid
- when I gained access to carlos - new function here was changing email;
- in response got json with role ID - that is the case from Access controls LABS and from repos:
  - log in -> change email -> find 302 -> change roleid and find value for admin rights;
  - solved that part only when I realized not to limit myself with small ranges of numbers.

#### RFI
- main function here was uploading image via link. according to repos mentioned above - that might be a RFI 
  vector;
- so:
  - exploit server - path to file + php script to read file inside body;
  - app - paste url and bypass validation with addition of #somethin.png;
  - don't add bypass link addition **/exploit.php#somethin.png** inside you path on the exploit server - you will 
    get 404... >_<